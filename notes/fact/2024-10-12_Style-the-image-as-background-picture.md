---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Style the image as background picture

```tsx
import Image from "next/image"
import homeImg from "/public/home.jpg";

export default function Home() {
  return (
    <>
      <h1>Home</h1>
      <p>Welcome to the home page</p>
      <div className="absolute -z-10 inset-0">  {/* this will make the image cover the whole screen */}
        <Image
          src={homeImg}
          alt="Home Image"
          fill                             // this will make the image fill the container
          style={{ objectFit: "cover" }}   // this will make the image cover the container
        />
      </div>
    </>
  );
}

```

Not only adding property `fill` and `style={{ objectFit: "cover" }}` to the `Image` component, but also wrapping the `Image` component with a `div` and setting the `div`'s `className` to `absolute -z-10 inset-0` will make the image cover the whole screen as a background picture.

We can further make the background and title reusable by creating a [[2024-10-12_Reusable-component---hero|reusable component - hero]].
```tsx
