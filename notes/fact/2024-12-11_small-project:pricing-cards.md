---
date: 2024-12-11
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# small project:pricing cards

In this project, we can learn how to use outer cards and inner cards to combine them into a complete card, and also learn how to reuse flexbox items to save development time.

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
  <div class="flex items-center justify-center min-h-screen bg-slate-800">
    <!-- Inner Container -->
    <div class="flex flex-col md:flex-row gap-6 my-6 md:my-0">
                <!-- ^^^^^^^^^^^^^^^^^^^^       ^^^^^^^^^^^^ to align the cards horizontally on large screens and vertically on small screens -->
      <!-- Col 1 -->
      <div class="bg-slate-700 rounded-xl text-white">
             <!-- ^^^^^^^^^^^^ this is treated as border color of the card,
             <!--              we will use three different parts (upper container, border, lower container) to group inner card inside. -->
        <!-- Upper Container -->
        <div class="p-8 mx-3 mt-3 rounded-t-xl bg-slate-800">
                   <!-- ^^^^^^^^^ define the top border radius -->
          <div class="text-center uppercase">Basic</div>
          <h2 class="mt-10 font-serif text-5xl text-center">100GB</h2>
          <h3 class="mt-2 text-center">$1.99/Month</h3>
          <div class="flex justify-center"> <!-- wrap anchor with a div to align it in the center is common practice -->
            <a href="" class="inline-block px-10 py-3 my-6 text-center border border-violet-600 rounded-lg duration-200 hover:bg-violet-800 hover:border-violet-800">Purchase</a>
                         <!-- ^^^^^^^^^^^^ anchor is inline by default, better change to inline-block      ^^^^^^^^^^^^ simply use only `duration-*` to add transition effect -->
          </div>
        </div>
        <!-- Border -->
        <div class="border-t border-slate-700"></div>
               <!-- ^^^^^^^^ this div element is only for a border line, so we can use border-t or border-b for it -->
        <!-- Lower Container -->
        <div class="p-8 mx-3 mb-3 rounded-b-xl bg-slate-800">
                   <!-- ^^^^^^^^^ define the bottom border radius, should be the same as the upper container -->
          <!-- List Container -->
          <div class="flex flex-col gap-2">
            <!-- List Item 1 -->
            <div class="flex justify-center"> <!-- to align a check icon with span text on a single line -->

              <!-- check icon -->
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>

              <span class="text-sm ml-1">100 GB of storage</span>
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

Because both `Col 1` and `Item 1` above are wrapped in flexbox, we can directly copy and paste more columns or items, and flexbox will automatically layout them for us. Below is an example:


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
  <div class="flex items-center justify-center min-h-screen bg-slate-800">
    <!-- Inner Container -->
    <div class="flex flex-col md:flex-row gap-6 my-6 md:my-0">
      <!-- Col 1 -->
      <div class="bg-slate-700 rounded-xl text-white">
        <!-- Upper Container -->
        <div class="p-8 mx-3 mt-3 rounded-t-xl bg-slate-800">
          <div class="text-center uppercase">Basic</div>
          <h2 class="mt-10 font-serif text-5xl text-center">100GB</h2>
          <h3 class="mt-2 text-center">$11.99/Month</h3>
          <div class="flex justify-center">
            <a href="" class="inline-block px-10 py-3 my-6 text-center border border-violet-600 rounded-lg duration-200 hover:bg-violet-800 hover:border-violet-800">Purchase</a>
          </div>
        </div>
        <!-- Border -->
        <div class="border-t border-slate-700"></div>
        <!-- Lower Container -->
        <div class="p-8 mx-3 mb-3 rounded-b-xl bg-slate-800">
          <!-- List Container -->
          <div class="flex flex-col gap-2">
            <!-- List Item 1 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">100 GB of storage</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Option to add memebers</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Extra member benefits</span>
            </div>
          </div>
        </div>
      </div>
      <!-- Col 2, copy and paste Col 1 and this card need to be emphasized -->
      <div class="bg-violet-600 rounded-xl text-white">
             <!-- ^^^^^^^^^^^^^ Change the outer border color to emphasize this card -->
        <!-- Upper Container -->
        <div class="p-8 mx-3 mt-3 rounded-t-xl bg-slate-800">
          <div class="text-center uppercase">Standard</div>
          <h2 class="mt-10 font-serif text-5xl text-center">200GB</h2>
          <h3 class="mt-2 text-center">$3.99/Month</h3>
          <div class="flex justify-center">
            <a href="" class="inline-block bg-violet-600 px-10 py-3 my-6 text-center border border-violet-600 rounded-lg duration-200 hover:bg-violet-800 hover:border-violet-800">Purchase</a>
                                      <!-- ^^^^^^^^^^^^^ default button without bg color (outlined btn style), for emphasis change bg color to violet-800 -->
          </div>
        </div>
        <!-- Border -->
        <div class="border-t border-slate-700"></div>
        <!-- Lower Container -->
        <div class="p-8 mx-3 mb-3 rounded-b-xl bg-slate-800">
          <!-- List Container -->
          <div class="flex flex-col gap-2">
            <!-- List Item 1 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">200 GB of storage</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Option to add memebers</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Extra member benefits</span>
            </div>
          </div>
        </div>
      </div>
      <!-- Col 3 -->
      <div class="bg-slate-700 rounded-xl text-white">
        <!-- Upper Container -->
        <div class="p-8 mx-3 mt-3 rounded-t-xl bg-slate-800">
          <div class="text-center uppercase">Premium</div>
          <h2 class="mt-10 font-serif text-5xl text-center">1 TB</h2>
          <h3 class="mt-2 text-center">$8.99/Month</h3>
          <div class="flex justify-center">
            <a href="" class="inline-block px-10 py-3 my-6 text-center border border-violet-600 rounded-lg duration-200 hover:bg-violet-800 hover:border-violet-800">Purchase</a>
          </div>
        </div>
        <!-- Border -->
        <div class="border-t border-slate-700"></div>
        <!-- Lower Container -->
        <div class="p-8 mx-3 mb-3 rounded-b-xl bg-slate-800">
          <!-- List Container -->
          <div class="flex flex-col gap-2">
            <!-- List Item 1 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">2 TB of storage</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Option to add memebers</span>
            </div>
            <!-- List Item 2 -->
            <div class="flex justify-center">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="w-5 h-5"
                viewBox="0 0 24 24"
                stroke-width="2"
                stroke="currentColor"
                fill="none"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path stroke="none" d="M0 0h24v24H0z" fill="none" />
                <path d="M5 12l5 5l10 -10" />
              </svg>
              <span class="text-sm ml-1">Extra member benefits</span>
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
