---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# small project: image gallery

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <!-- Global Container -->
  <div class="flex items-center justify-center min-h-screen bg-cyan-50">
    <!-- Card Container -->
    <div class="bg-white p-6 md:p-40 m-3 space-y-10 shadow-2xl rounded-3xl">
                                    <!-- ^^^^^^^^^^ without flexbox, we can space between elements using by space-y-* and space-x-* classes. -->
                                    <!-- ^^^^^^^^^^ it can also use to space between elements in flexbox -->
      <!-- Menu Container -->
      <div class="flex flex-col md:flex-row items-center justify-center md:justify-end space-y-3 md:space-y-0 md:space-x-8 md:mb-24">
                                                                                  <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ x and y direction space between elements are different. -->
        <!-- Menu Items -->
        <div class="group"> <!-- we want a bottom line shows when hover on the menu item, so we wrap them with a div which has a group class -->
                            <!-- so that no matter which element we hover, the bottom line will show. -->
          <a href='#'>Vector</a>
          <div class="mx-2 mt-2 border-b-2 border-black opacity-0 group-hover:opacity-100 duration-500"></div>
                                                   <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ instead of hidden, we use opacity to hidden or show the bottom line. -->
                                                   <!--                                                this way work better with transition and not break the layout. -->
        </div>
        <div class="group">
          <a href='#'>Illustrations</a>
          <div class="mx-2 mt-2 border-b-2 border-black opacity-0 group-hover:opacity-100 duration-500"></div>
        </div>
        <div class="group">
          <a href='#'>Images</a>
          <div class="mx-2 mt-2 border-b-2 border-black opacity-0 group-hover:opacity-100 duration-500"></div>
        </div>
        <div class="group">
          <a href='#'>Icons</a>
          <div class="mx-2 mt-2 border-b-2 border-black opacity-0 group-hover:opacity-100 duration-500"></div>
        </div>
      </div>
      <!-- Search Container -->
      <div class="flex flex-col md:flex-row justify-between gap-5">
        <!-- Input and SVG Container -->
        <div class="flex justify-between border-b">
                                    <!-- ^^^^^^^^ we use input and icon button to group a input ui, so bottom border is created on the container of them -->
          <input type="text" class="ml-6 border-none md:w-80 placeholder:font-thin focus:outline-none" placeholder="Search" />
                                    <!-- ^^^^^^^^^^^                               ^^^^^^^^^^^^^^^^^^ input outline is ugly, so we remove it. -->
                                    <!-- ^^^^^^^^^^^ border thing is not needed for input, so we remove it. -->
          <button> <!-- this is a icon button, it will work as a part of the input ui. -->
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="w-8 text-gray-300 duration-200 hover:scale-110"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="currentColor"
              fill="none"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path stroke="none" d="M0 0h24v24H0z" fill="none" />
              <circle cx="10" cy="10" r="7" />
              <line x1="21" y1="21" x2="15" y2="15" />
            </svg>
          </button>
        </div>
        <!-- Upload Button -->
        <button class="py-3 px-14 text-lg font-normal text-white hover:text-black bg-black hover:bg-white border border-black rounded-md shadow-2xl duration-200">
          Upload
        </button>
      </div>
      <!-- Gallery Container -->
      <div class="grid md:grid-cols-3 gap-4">
             <!-- ^^^^^^^^^^^^^^^^^^^ by default, grid only 1 column -->
        <!-- Image 1 -->
        <div class="relative group">
               <!-- ^^^^^^^^ ^^^^^ because we want to show some info on hover, so we wrap the image and info with a div to handle hover event. -->
               <!-- ^^^^^^^^ and info is a absolute position, so we set relative position to the container. -->
          <img src="image1.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
                 <!-- ^^^^^^^^          ^^^^^^^^^ this is shorthand of `left-0 right-0`       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ here we use group-hover, so even hover on image the info will show. -->
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
        <!-- Image 2 -->
        <div class="relative group">
          <img src="image2.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
        <!-- Image 3 -->
        <div class="relative group">
          <img src="image3.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
        <!-- Image 4 -->
        <div class="relative group">
          <img src="image4.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
        <!-- Image 5 -->
        <div class="relative group">
          <img src="image5.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
        <!-- Image 6 -->
        <div class="relative group">
          <img src="image6.jpg" alt="" class="w-72">
          <div class="absolute bottom-0 inset-x-0 py-2 px-4 text-white bg-black bg-opacity-40 opacity-0 group-hover:opacity-100 duration-500">
            <div class="flex justify-between w-full">
              <div class="font-normal">
                <p class="text-sm">Abstract Painting</p>
                <p class="text-xs">245 likes - 35 Shares</p>
              </div>
              <div class="flex items-center">
                <img src="bookmark.svg" alt="bookmark">
              </div>
            </div>
          </div>
        </div>
      </div>
      
    </div>
  </div>
  
  <script src="main.js"></script>
</body>
</html>

```

