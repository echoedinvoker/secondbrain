---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Make container center horizontally with RWD width

```tsx
import "./globals.css";

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body className="container mx-auto p-4">  {/* make container center horizontally with RWD width */}
        {children}
      </body>
    </html>
  );
}

```
