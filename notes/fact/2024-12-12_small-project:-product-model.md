---
date: 2024-12-12
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# small project: product model

In this small project, graphics and buttons have transitions to make the whole project look more lively. We also used `ping` to quickly create a small animation.

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
  <div class="flex items-center justify-center min-h-screen bg-slate-100"> <!-- centering the content, don't forget add height -->
    <!-- RWD Card Container -->
    <div class="flex flex-col md:flex-row gap-10 p-6 md:p-16 m-3 md:m-0 bg-white rounded-2xl shadow-2xl">
                                            <!-- ^^^^^^^^^^^^^^^^^^^^^^ margin is required in small screen, and large screen better to have more padding -->
      <!-- Image Div -->
      <div> <!-- I have no idea why using div here, maybe just a better practice -->
        <img src="headphone.png" alt="" class="w-60 mx-auto hover:scale-105 duration-200">
      </div>
      <!-- Content -->
      <div class="flex flex-col gap-6">
        <!-- Label & Title Container -->
        <div class="flex flex-col mb-4 gap-3 text-center md:text-left">
                                        <!-- ^^^^^^^^^^^^^^^^^^^^^^^^ in fact, I can use md:items-start do the same thing -->
          <div> <!-- add this div to prevent the child element to be stretched -->
            <div class="inline-block px-3 py-1 text-sm text-white bg-black rounded-full">Free Shipping</div>
          </div>
          <!-- Title -->
          <div class="max-w-sm text-2xl font-medium">
                 <!-- ^^^^^^^^ to avoid the text to be too wide -->
            Razer Kraken X Ultralight Gaming Headset
          </div>
          <!-- Price Container <-->
          <div class="flex flex-col mb-4 gap-3 text-center md:text-left">
            <p class="line-through">$799</p>
                 <!-- ^^^^^^^^^^^^ to make the text line-through -->
            <p class="text-5xl font-bold">$599</p>
            <p class="text-sm font-light text-gray-500">
              This offer is valid until April 3rd or as long as stock lasts!
            </p>
          </div>
          <!-- Button Group -->
          <div class="group"> <!-- to listen hover event, and use `group` to pass the hover effect to the child element -->
            <!-- this button wraps a div to make some effect -->
            <button class="w-full bg-blue-700 text-white border-b-8 group-hover:border-b-0 border-b-blue-700 group-hover:border-t-8 group-hover:border-t-blue-700 rounded-lg group-hover:shadow-lg transition-all duration-150">
              <div class="px-8 py-4 bg-blue-500 rounded-lg group-hover:bg-blue-700 duration-150">
                Add to cart
              </div>
            </button>
          </div>
          <!-- Stock -->
          <div class="flex items-center gap-3 group">
            <div class="w-3 h-3 bg-green-400 rounded-full group-hover:animate-ping"></div> <!-- create a small circle -->
                                                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^ in tailwindcss, we can generate a animation very easily -->
                        
            <div class="text-sm">50+ pcs. in stock</div>
          </div>
          <!-- Bottom Buttons Container -->
          <div class="flex flex-col md:flex-row gap-4">
            <button class="flex items-center justify-center py-3 px-5 gap-3 border-2 border-gray-300 rounded-lg shadow-sm hover:shadow-lg hover:-translate-y-0.5 duration-150">
                                                                                                                     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this transition makes the button look like in 3D -->
              <img src="weight.png" alt="" class="w-8">
              <span>Add to cart</span>
            </button>
            <button class="flex items-center justify-center py-3 px-5 gap-3 border-2 border-gray-300 rounded-lg shadow-sm hover:shadow-lg hover:-translate-y-0.5 duration-150">
              <img src="heart.png" alt="" class="w-8">
              <span>Add to wishlist</span>
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

