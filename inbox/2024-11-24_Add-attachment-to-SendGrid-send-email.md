---
date: 2024-11-24
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Add attachment to SendGrid send email

In [[2024-11-24_Send-email-through-SendGrid|this topic]] we learned how to send an email through SendGrid. We want to further enhance the email by adding an attachment to it as below:

```ts
// src/routes/api/purchase-confirmation/+server.ts

import { json, type RequestHandler } from "@sveltejs/kit";
import sgMail from "@sendgrid/mail";
import { SENDGRID_API_KEY } from "$env/static/private";

sgMail.setApiKey(SENDGRID_API_KEY);

// If we store the PDF file in AWS S3
const PDF_GUIDE_URL =
  "https://narrify-public.s3.eu-central-1.amazonaws.com/sample.pdf";

export const POST: RequestHandler = async ({ request }) => {
  const requestBody = await request.json();

  // Fetch the PDF and convert it to base64 string
  // message.attachments is an array of attachment which accepts base64 string
  const response = await fetch(PDF_GUIDE_URL);
  const pdfBuffer = await response.arrayBuffer();
  const base64Pdf = Buffer.from(pdfBuffer).toString("base64");

  const customerEmail = requestBody.data.object.customer_details.email;
  const customerName = requestBody.data.object.customer_details.name;

  const message = {
    to: customerEmail,
    from: "mattchang@mirle.com.tw",
    subject: "Your Purchase Confirmation - Complete Spain Relocation Guide",
    html: `
    <h1>Thank You for Your Purchase!</h1>
    <p>Dear ${customerName},</p>
    <p>We appreciate your purchase of the <strong>Complete Spain Relocation Guide</strong>. We're confident that this ebook will provide you with the insights and advice you need to make your move to Spain as smooth and stress-free as possible.</p>
    <p><strong>What happens next?</strong></p>
    <ul>
      <li>You will find your ebook attached to this email. Please download and save it for future reference.</li>
      <li>A separate purchase confirmation has been sent to your email as well.</li>
      <li>If you have any questions or need further assistance, don't hesitate to reach out to us at support@kizo-agency.com.</li>
    </ul>
    <p>Thank you once again for choosing our guide. We wish you the best of luck on your journey to Spain!</p>
    <p>Best regards,<br/>The Kizo Agency Team</p>
  `,
    // add property attachments to include the PDF base64 string as attachment
    attachments: [
      {
        content: base64Pdf,
        filename: "Digital Ebook - Spain relocation.pdf",
        type: "application/pdf",
        disposition: "attachment",
      },
    ],
  };

  await sgMail.send(message);

  return json({ response: "Email sent" });
}

```
