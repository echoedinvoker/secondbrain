---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Import image

**Put the image in the right place.**

```bash
mattc@x ~/r/my-app (main)> tree -I "node_modules"
.
├── public               # If using local images, put them here
│   ├── home.jpg
│   ├── performance.jpg
│   ├── reliability.jpg
│   └── scale.jpg
├── src
│   ├── app
│   │   └── page.tsx
│   ├── components
│   ├── data
│   └── utils
├── tailwind.config.ts
└── tsconfig.json

```

**Import with Next.js Image component.**

```tsx
// src/app/page.tsx
import Image from "next/image";  // This component is optimized for images
import homeImg from "/public/home.jpg";  // import image file, use / as root directory of project

export default function Home() {
  return (
    <>
      <h1>Home</h1>
      <p>Welcome to the home page</p>
      <Image // Import image with Image component
        src={homeImg}
        alt="Home Image"
        fill  // fill the container (image placeholder) to prevent layout shift
      />  
    </>
  );
}
```

Next.js Image component is optimized for images. It automatically creates multiple sizes of the image and serves the most appropriate size based on the device's screen size. It also lazy-loads images by default, which means it only loads images when they are visible on the screen.

When loading remote images, the layout shift can be prevented by setting the `fill` attribute. It fills the image placeholder to prevent layout shift.

In this case, the homeImg is actually a page backgroud image. So we should do more [[2024-10-12_Style-the-image-as-background-picture|style]] to make it look better. (cover whole page, center the image and let the text on top of the image)
