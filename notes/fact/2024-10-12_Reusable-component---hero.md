---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Reusable component: hero

This component includes the entire page background and title, and can be used on the homepage or other important pages.

```jsx
// src/components/Hero.tsx
import Image, { StaticImageData } from "next/image";

interface HeroProps {       // Because this component is reusable, we define the props to make it flexible
  imgData: StaticImageData;
  imgAlt: string;
  title: string;
}
export default function Hero(props: HeroProps) {
  return (
    <div className="relative h-screen">          {/* The hero component takes up the entire screen */}
      <div className="absolute -z-10 inset-0">   {/* The image is set as the background */}
        <Image
          src={props.imgData}
          alt={props.imgAlt}
          fill
          style={{ objectFit: "cover" }}  
        />
      </div>
      <div className="pt-48 flex justify-center items-center">  {/* The title is centered in the middle, pt-48 is padding top for the header space */}
        <h1 className="text-white text-6xl">{props.title}</h1>  {/* You can modify the title style as needed */}
      </div>
    </div>
  );
}
```

```jsx
// src/app/page.tsx
import homeImg from "/public/home.jpg";
import Hero from "@/components/hero";

export default function Home() {
  return (
    <Hero
      imgData={homeImg}  // The image data is passed as a prop
      imgAlt="Home"      // The image alt text is passed as a prop
      title="Welcome to My Website"  // The title is passed as a prop
    />
  );
}

```
As mentioned above, the parts of the hero component that need to be changed according to requirements are passed in through props. This allows the hero component to be used in different places without modifying the original code.


