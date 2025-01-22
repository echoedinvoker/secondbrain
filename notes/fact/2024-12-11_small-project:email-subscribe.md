---
date: 2024-12-11
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# small project:email subscribe

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <!-- Background Container -->
  <div class="flex justify-center items-center h-screen bg-zinc-700">
                             <!-- ^^^^^^^^^^^^^^^^^^^^^ must use h-screen, or height is the same as children's by default, so that items-center is meaningless -->
    <!-- Card -->
    <div class="bg-zinc-800 p-2 mx-6 rounded-2xl">
                           <!-- ^^^^ i have no idea why this is needed, I can't see any difference -->
      <!-- Flex Container -->
      <div class="flex flex-col md:flex-row rounded-l-xl">
                                            <!-- ^^^^^^^^^^^^ i have no idea why this is needed, I can't see any difference -->
        <!-- Image -->
        <img
          src="./image.jpg"
          alt=""
          class="object-fit rounded-xl md:rounded-r-none h-80 md:h-64 transform hover:scale-105 duration-200">
                       <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^              ^^^^^^^^^                 ^^^^^^^^^^^^ always forget this, it's important for animation work, we can omit transition but need duration -->
                       <!-- ^                                         ^ no difference, I have no idea why this is needed -->
                       <!-- ^ when md, only remove right rounded, but left rounded is still there -->
        <!-- Content -->
        <div class="p-6 md:p-12">
          <h2 class="font-serif text-xl font-medium text-white text-center md:text-left">
                <!-- ^^^^^^^^^^ serif font is great for heading and title in general -->
            Get diet and fitness tips in your inbox
          </h2>
          <p class="max-w-xs my-4 text-xs text-white leading-5 tracking-wider text-center md:text-left">
               <!-- ^^^^^^^^                         ^^^^^^^^^^^^^^^^^^^^^^^^ increase the space between lines and characters, let the text not too tight, make it more readable -->
               <!-- ^ in long text, use it to avoid the block of text too wide -->
            Eating right and exercising regularly can help you avoid excess weight gain and maintain a healthy weight.
          </p>
          <div class="flex flex-col md:flex-row mt-3 gap-3">
            <input
              type="text"
              placeholder="Enter your email"
              class="p-2 px-4 bg-zinc-800 border border-zinc-600 focus:outline-none text-center md:text-left text-xs text-white placeholder:text-center placeholder:md:text-left placeholder:text-xs"
                                                            <!-- ^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ no placeholder is to define user input text -->
                                                            <!-- ^ usually avoid default focus outline of input, it's ugly                                                                           placeholder: is to define the placeholder text -->
                                                            <!--                                                                                                                                     they are needed to be styled separately -->
            />
            <button class="px-5 py-3 text-xs rounded-md text-zinc-800 bg-lime-500 hover:bg-lime-700 hover:text-white duration-500">
                                                                                                                <!-- ^^^^^^^^^^^^ only use `duration-*` to enable the transition effect, no need to use `transition-*` -->
              Subscribe
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>

  <script src="main.js"></script>
</body>
</html>

```

