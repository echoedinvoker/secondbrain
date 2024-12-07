---
date: 2024-12-06
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Collecting user contact informations with SendGrid

Continuing with [[2024-12-06_Create-api-endpoint-for-sending-email:-make-sure-inputs-data-to-backend|this topic]], obtain the SendGrid API Key using the method of [[2024-11-23_Get-SENDGRID_API_KEY-from-Sendgrid-website|this topic]], and then use library `@sendgrid/mail` to send emails to your own inbox in order to collect user contact information. You can check [[2024-11-24_Send-email-through-SendGrid|this topic]] for more information about how to implement the email sending functionality at the backend.


```bash
npm i @sendgrid/mail

```

```.env
SENDGRID_API_KEY=SG.xxxxxx....   // get this key from SendGrid website

```

```ts
// src/routes/api/send-mail/+server.ts

import { json } from "@sveltejs/kit";
import type { RequestHandler } from "./$types";
import { SENDGRID_API_KEY } from "$env/static/private";  // extract the key from .env file as static private variable
import sgMail from "@sendgrid/mail";  // import SendGrid library

sgMail.setApiKey(SENDGRID_API_KEY);  // setup the API key to SendGrid client

export const POST: RequestHandler = async ({ request }) => {
  const { name, email, message } = await request.json();

  // a simple validation
  if (!name || !email || !message) {
    return json({ message: "All fields are required" }, { status: 400 });
  }

  // create the mail message for sgMail.send()
  const mailMessage = {
    to: "echoedinvoker@gmail.com",
    from: "mattchang@mirle.com.tw",  // from email address must be registered in SendGrid website, don't forget it ._.
    subject: "Message from dev-portfolio contact form",
    html: `Someone sent you a message from your portfolio website:<br><br>
    <strong>Name:</strong> ${name}<br>
    <strong>Email:</strong> ${email}<br>
    <strong>Message:</strong> ${message}`,
  };

  // send the email by using SendGrid client and wrap it with trycatch block for error handling
  try {
    await sgMail.send(mailMessage);
    return json({ emailSentSuccessfully: true });
  } catch (err) {
    return json({ err }, { status: 500 }); // in SvelteKit, even if you return status 500, it will still be displayed as 2
  }
}

```

