---
date: 2024-11-27
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create simple version interface from too nested type of Sanity

In [[2024-11-27_Create-new-document-schema-in-Sanity-Studio-for-Work-section|this topic]], we add a new type `Project` to `sanity.d.ts` as below:

```ts
// src/lib/types/sanity.d.ts 

// ...omit

type Project = {
  _id: string
  _type: 'project'
  _createdAt: string
  _updatedAt: string
  _rev: string
  name: string
  company: string
  slug: string
  image: {
    asset: {
      _ref: string
      _type: 'reference'
      _weak?: boolean
      [internalGroqTypeReferenceTo]?: 'sanity.imageAsset'
    }
    hotspot?: SanityImageHotspot
    crop?: SanityImageCrop
    _type: 'image'
  }
  dateAccomplished: string
  content: Array<
    | {
      children: Array<{
        marks?: Array<string>
        text: string
        _type: 'span'
        _key: string
      }>
      style: 'normal' | 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6' | 'blockquote'
      listItem?: 'bullet' | 'number'
      markDefs?: Array<{
        href?: string
        _type: 'link'
        _key: string
      }>
      level?: number
      _type: 'block'
      _key: string
    }
    | {
      asset: {
        _ref: string
        _type: 'reference'
        _weak?: boolean
        [internalGroqTypeReferenceTo]?: 'sanity.imageAsset'
      }
      hotspot?: SanityImageHotspot
      crop?: SanityImageCrop
      _type: 'image'
      _key: string
    }
  >
  stack: Array<string>
}

type SanityImageCrop = {
  _type: 'sanity.imageCrop'
  top?: number
  bottom?: number
  left?: number
  right?: number
}

type SanityImageHotspot = {
  _type: 'sanity.imageHotspot'
  x?: number
  y?: number
  height?: number
  width?: number
}

```

Because the project data obtained directly from Sanity is too complex, we added a function in `sanity.ts` to simplify it into a data format that is easy to use in the frontend:

```ts
// src/lib/utils/sanity.ts

// omit other codes...

export function processProjectEntries(rawProject: Project): ProcessedProject {
//                                                ^^^^^^^   ^^^^^^^^^^^^^^^^
//                                                convert complex nested data to simple version

  // some processing logic, then return the processed project
  // we will implement it after we finish the type definition

}

```

Start writing interface `ProcessedProject`:

```ts
// src/lib/types/sanity.d.ts 

// ...omit

type SanityProject = {
//   ^^^^^^ name `Project` is easy to conflict with other types, so we rename it to `SanityProject`
//          remember type name in `sanity.ts` also need to change to `SanityProject`
  _id: string
  _type: 'project'
  _createdAt: string
  _updatedAt: string
  _rev: string
  name: string  // frontend type need this field
  company: string  // frontend type need this field
  slug: string  // frontend type need this field
  image: {
    asset: {
      _ref: string  // frontend type need this field, convert to field `projectImageUrl`
      _type: 'reference'
      _weak?: boolean
      [internalGroqTypeReferenceTo]?: 'sanity.imageAsset'
    }
    hotspot?: SanityImageHotspot
    crop?: SanityImageCrop
    _type: 'image'
  }
  dateAccomplished: string  // frontend type need this field

  // we split nested content to two types for easy to read and analysis
  content: Array<
    | RawTextContent 
  //  ^^^^^^^^^^^^^^
    | RawImageContent
  //  ^^^^^^^^^^^^^^^
  >

  stack?: Array<string>
}

interface RawTextContent {
  children: Array<{
    marks?: Array<string>
    text: string  // frontend type need this field, convert to field `textToRender`
    _type: 'span'
    _key: string
  }>
  style: 'normal' | 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6' | 'blockquote'  // frontend type need this field
  listItem?: 'bullet' | 'number'
  markDefs?: Array<{
    href?: string
    _type: 'link'
    _key: string
  }>
  level?: number
  _type: 'block'
  _key: string
}

interface RawImageContent {
  asset: {
    _ref: string  // frontend type need this field, convert to field `url`
    _type: 'reference'
    _weak?: boolean
    [internalGroqTypeReferenceTo]?: 'sanity.imageAsset'
  }
  hotspot?: SanityImageHotspot
  crop?: SanityImageCrop
  _type: 'image'
  _key: string
}


// below are new types for frontend use, only keep necessary fields
interface ProcessedProject {
  name: string;
  company: string;
  slug: string;
  projectImageUrl: string;
  dateAccomplished: string;
  stack: string[];
  content: Array<ProcessedTextContent | ProcessedImageContent>;
}

interface ProcessedTextContent {
  type: 'text';  // not from Sanity, but we need to distinguish from image
  style: 'normal' | 'h1' | 'h2' | 'h3' | 'h4' | 'h5' | 'h6' | 'blockquote';
  textToRender: string;
}

interface ProcessedImageContent {
  type: 'image';  // not from Sanity, but we need to distinguish from text
  url: string;
}
```


