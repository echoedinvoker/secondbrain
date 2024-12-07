---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Add a div after body to better layout the page

```tsx
// src/app/layout.tsx 
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'
import Providers from './providers'
import Header from '@/components/header'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'Create Next App',
  description: 'Generated by create next app',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <div className='container mx-auto px-4 max-w-6xl'>  {/* add this div for better layout */}
          <Providers>                                       {/* max-w-6xl is to prevent the content from stretching too wide */}
            <Header />
            {children}
          </Providers>
        </div>
      </body>
    </html>
  )
}

```