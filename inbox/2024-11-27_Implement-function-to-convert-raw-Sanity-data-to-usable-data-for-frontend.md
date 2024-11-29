---
date: 2024-11-27
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Implement function to convert raw Sanity data to usable data for frontend

If fetched data from Sanity is way too nested, we need to convert it to a more usable formatted data for the frontend.

We use [[2024-11-27_Create-simple-version-interface-from-too-nested-type-of-Sanity|this topic]] as an example. In this topic, we have already defined the type after conversion. Next, we will implement the function to convert data in `sanity.ts`.

```ts
import imageUrlBuilder from '@sanity/image-url'  // `npm i @sanity/image-url`, use this to compute image CDN URL from raw image data

// ...omit codes to produce sanityClient

export function processProjectEntries(rawProject: SanityProject): ProcessedProject {
  const builder = imageUrlBuilder(sanityClient)  // create a new instance of imageUrlBuilder
                                                 // we will use this to compute image CDN URL from raw image data

  // start to convert raw data to usable data
  const ProcessedProject: ProcessedProject = {
    name: rawProject.name,
    company: rawProject.company,
    slug: rawProject.slug,
    projectImageUrl: builder.image(rawProject.image).url(),
    //               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ compute image CDN URL from raw image data
    dateAccomplished: rawProject.dateAccomplished,
    stack: rawProject.stack || [],
    content: rawProject.content.map(processProjectContent),
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ we created a fn to convert each element in `content` array of raw data
    //                                                     to regroup them in a more usable format content
  }

  return ProcessedProject
}

// create a function to convert each element in `content` array of raw data
function processProjectContent(content: RawTextContent | RawImageContent) {
//                             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ be careful, `content` here means a element of `content` array of raw data
//                                                                       it might be text content or image content, we use its `_type` to distinguish them
  
  if (content._type === 'block') {
  //  ^^^^^^^^^^^^^^^^^^^^^^^^^ if this is true, this fn gets a text content
    const processedTextContent: ProcessedTextContent = {
      type: 'text',  // this field is used to distinguish text content from image content
      style: content.style,
      textToRender: content.children.map((child) => child.text).join('\n'),
    }
    return processedTextContent

  } else {
    // handle image content

    const builder = imageUrlBuilder(sanityClient)  // create a new instance of imageUrlBuilder
    const processedImageContent: ProcessedImageContent = {
      type: 'image',  // this field is used to distinguish text content from image content
      url: builder.image(content).url(),
      //   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ compute image CDN URL from raw image data
    }
    return processedImageContent
  }
}

```

Then, whenever we fetch data with type `SanityProject`, we can use fn `processProjectEntries` to convert raw data to usable data for the frontend.
```ts
