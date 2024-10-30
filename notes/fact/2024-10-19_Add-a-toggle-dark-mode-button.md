---
date: 2024-10-19
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Add a toggle dark mode button

**Installation**

```bash
npm install @nextui-org/react tailwindcss next-themes
```


**tailwind.config.ts**

```ts
// tailwind.config.ts
import type { Config } from 'tailwindcss'
import { nextui } from '@nextui-org/react' // import nextui from @nextui-org/react

const config: Config = {
  content: [
    // ...
    './node_modules/@nextui-org/theme/dist/**/*.{js,ts,jsx,tsx}'
  ],
  // ...
  darkMode: 'class',  // add this line
  plugins: [nextui()],  // add nextui() into plugins array
}
export default config

```


**Context**

```tsx
// src/app/providers.tsx 
'use client';

import { ThemeProvider } from "next-themes";
import { NextUIProvider } from "@nextui-org/react";

interface ProvidersProps {
  children: React.ReactNode;
}

export default function Providers({ children }: ProvidersProps) {
  return (
    <ThemeProvider attribute="class" defaultTheme="system">
        <NextUIProvider>
          {children}
        </NextUIProvider>
    </ThemeProvider>
  );
}

```

**Component**

```tsx
// src/components/theme-toggle.tsx 
'use client';  // we use the hook `useTheme` to toggle the theme, so must be the client component

import React from 'react'
import { Button } from '@nextui-org/react'
import { useTheme } from 'next-themes'

export const ThemeToggle: React.FC = () => {
  const { theme, setTheme } = useTheme()  // simply change the value of theme to change the theme

  return (
    <Button
      onClick={() => setTheme(theme === 'dark' ? 'light' : 'dark')} // use triary operator to toggle the theme
    >
      {theme === 'dark' ? <span>🌞</span> : <span>🌙</span>}
    </Button>
  )
}
```

Then you can use the `ThemeToggle` component in your app to toggle the theme.
