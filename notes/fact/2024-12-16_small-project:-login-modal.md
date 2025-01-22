---
date: 2024-12-16
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# small project: login modal

In this small project, we demonstrate various styles of button designs, more vivid hover button effects, and replacing the default sans-serif and monospace fonts.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- import Mulish and Rokkitt fonts to replace the default fonts -->
  <link
    href="https://fonts.googleapis.com/css2?family=Mulish:wght@300;400;500;600;700&family=Rokkitt:wght@600;700&display=swap"
    rel="stylesheet"
  />
  <title>Document</title>
</head>
<body>

    <!-- ... -->

```
```js
// tailwind.config.js 

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ['./dist/*.{html,js}', './src/*.{html,js}'],
  theme: {

    // replace the default fonts with Mulish and Rokkitt
    fontFamily: {
      sans: ['Mulish', 'sans-serif'],
      mono: ['Rokkitt', 'monospace'],
    },

    extend: {},
  },
  plugins: [],
}

```

```html

<!-- ... -->

<body>
  <!-- Global Container -->
  <div class="flex items-center justify-center min-h-screen bg-rose-50">
    <!-- Card Container -->
    <div class="relative flex flex-col md:flex-row m-6 md:m-0 gap-10 md:gap-0 bg-white shadow-2xl rounded-2xl">
           <!-- ^^^^^^^^ for absolute close button at the end -->
      <!-- Left Side -->
      <div class="p-6 md:p-20">  <!-- this container don't use flexbox, so items use margins to space themselves -->
             <!-- ^^^^^^^^^^^ it's common to use smaller padding for mobile and larger padding for desktop -->
        <!-- Top Content -->
        <h2 class="font-mono mb-5 text-4xl font-bold">Log In</h2>
              <!-- ^^^^^^^^^ used mono font for the title and headline -->
        <p class="max-w-sm mb-12 font-sans font-light text-gray-600">
             <!-- ^^^^^^^^       ^^^^^^^^^used sans font for the paragraph and normal text... Because we import special google fonts for sans and mono fonts
             <!-- ^^^^^^^^it's common to use max-width to limit the width of the long text such paragraph -->
          Log in to your account to upload or download pictures, videos or music.
        </p>
        <input
          type="text"
          class="w-full p-6 border border-gray-300 rounded-md placeholder:font-sans placeholder:font-light"
          placeholder="Enter your email address"
        />

        <!-- Middle Content -->
        <div class="flex flex-col md:flex-row gap-6 md:gap-0 items-center justify-between mt-6">
          <div class="font-thin text-cyan-700">Forgot password</div>
          <button class="w-full md:w-auto flex justify-center items-center p-6 gap-4 font-sans font-bold text-white rounded-md px-9 bg-cyan-700 shadow-cyan-100 hover:bg-opacity-90 shadow-sm hover:shadow-lg border transition hover:-translate-y-0.5 duration-150">
                                                                                                                                                           <!-- ^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^                   ^^^^^^^^^^^^^^^^^^^^^^ -->
                                                                                                                                                           <!-- this project we use these styles to make the button looks like it's pressed widely -->
                                                                                                                                                           <!-- you can see repeated styles in the following buttons -->
            <span>Next</span>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="w-7"
              viewBox="0 0 24 24"
              stroke-width="1.5"
              stroke="#ffffff"
              fill="none"
              stroke-linecap="round"
              stroke-linejoin="round"
            >
              <path stroke="none" d="M0 0h24v24H0z" fill="none" />
              <line x1="5" y1="12" x2="19" y2="12" />
              <line x1="13" y1="18" x2="19" y2="12" />
              <line x1="13" y1="6" x2="19" y2="12" />
            </svg>
          </button>
        </div>

        <!-- Border -->
        <div class="mt-12 border-b border-b-gray-300"></div> <!-- simply use border-b to create a divider -->

        <!-- Bottom Content -->
        <p class="py-6 text-sm font-thin text-center text-gray-400">
          or log in with
        </p>

        <!-- Bottom Buttons Container -->
        <div class="flex flex-col md:flex-row space-x-0 md:space-x-4 space-y-6 md:space-y-0">
          <button class="flex items-center justify-center py-2 space-x-3 border-border-gray-300 rounded shadow-sm hover:shadow-lg hover:bg-opacity-30 hover:-translate-y-0 5 transition duration-150 md:w-1/2">
            <img src="google.png" alt="" class="w-9">
            <span class="font-thin">Google</span>
          </button>
          <button class="flex items-center justify-center py-2 space-x-3 border-border-gray-300 rounded shadow-sm hover:shadow-lg hover:bg-opacity-30 hover:-translate-y-0 5 transition duration-150 md:w-1/2">
            <img src="facebook.png" alt="" class="w-9">
            <span class="font-thin">Facebook</span>
          </button>
        </div>

      </div>

      <!-- Right Side -->
      <img src="image.jpg" alt="" class="w-[430px] hidden md:block" />

      <!-- Close Button -->
      <div class="group absolute -top-5 md:top-4 right-4 flex items-center justify-center w-10 h-10 bg-gray-200 md:bg-white rounded-full hover:cursor-pointer hover:-translate-y-0 5 transition duration-150">
             <!-- ^^^^^                                                                   ^^^^^^^^^ bigger size for button and no any padding -->
             <!-- ^^^^^ because svg inside has hover effects but trigger when hovering the whole button instead of svg itself -->
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="w-6 h-6 text-black group-hover:text-gray-600"
            <!-- ^^^^^^^            ^^^^^^^^^^^^^^^^^^^^^^^^^ hover effect when hovering the whole button -->
            <!-- ^^^^^^^ smaller size for inner svg, so we don't need padding to create space inside button --></svg>
          viewBox="0 0 24 24"
          stroke-width="1.5"
          stroke="currentColor"
          fill="none"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          <path stroke="none" d="M0 0h24v24H0z" fill="none" />
          <line x1="18" y1="6" x2="6" y2="18" />
          <line x1="6" y1="6" x2="18" y2="18" />
        </svg>
      </div>
    </div>
  </div>

  
  <script src="main.js"></script>
</body>
</html>

```
