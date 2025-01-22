---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss|tailwindcss]]"
---

# Features & Accession sections


## Features Section

Let's create the Features section:

```html
<body>
  <section id="hero">
    <!-- ... -->
  </section>

  <section id="snippets">
    <!-- ... -->
  </section>

  <!-- Features Section -->
  <section id="features">
    <div class="section-container my-20 px-10">
      <div class="relative flex flex-col md:flex-row md:space-x-32">
             <!-- ^^^^^^^^ for image absolute when middle screen -->
        <!-- Image -->
        <div class="md:w-1/2">
          <img src="images/image-computer.png" alt="" class="md:absolute top-0 right-[50%]">
                                                        <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ make this image in middle screen always has fixed size and align right side with 50% of screen -->
                                                        <!--                               (so it means that the image left-side will be cutted off in some screen size) -->
        </div>

        <!-- Items Container -->
        <div class="flex flex-col mt-16 mb-24 space-y-12 text-xl md:mb-60 md:text-left md:pl-16">
          <!-- Item 1 -->
          <div>
            <h5 class="mb-2 text-2xl font-bold text-darkGrayishBlue">
              Quick Search
            </h5>
            <p class="max-w-md mx-auto text-grayishBlue">
              Easily search your snippets by content, category, web address, application and more...
            </p>
          </div>
          <!-- Item 2 -->
          <div>
            <h5 class="mb-2 text-2xl font-bold text-darkGrayishBlue">
              iCloud Sync
            </h5>
            <p class="max-w-md mx-auto text-grayishBlue">
              Instantly saves and syncs snippets across all your devices.
            </p>
          </div>
          <!-- Item 3 -->
          <div>
            <h5 class="mb-2 text-2xl font-bold text-darkGrayishBlue">
              Completely History
            </h5>
            <p class="max-w-md mx-auto text-grayishBlue">
              Retrieve any snippets from the first moment you started using the app.
            </p>
          </div>
        </div>
      </div>
    </div>
  </section>
</body>

</html>

```
![md-features.png](../assets/imgs/md-features.png)

![sm-features.png](../assets/imgs/sm-features.png)


## Accession Section

This section is very simple, just group with h3, p and img without any RWD, so we hurry up to finish this section:

```html
<body>
  <section id="hero"> ... </section>

  <section id="snippets"> ... </section>

  <section id="features"> ... </section>

  <!-- Access Section -->
  <section id="access">
    <div class="section-container my-20">
      <!-- this section contents just simple from top to bottom and no need to RWD, so we don't need to create a div for flex container here -->
      <h3>Access Cliboard Anywhere</h3>
      <p class="max-w-3xl mx-auto mb-24 text-xl leading-9 text-center text-grayishBlue">  <!-- styles are exactly the same as the previous section, you can make it a custom class (but I'm lazy to do that) -->
        Whether you're on the go, or at your computer, you can access all your Clipboard snippets in a few simple
        clicks.
      </p>
      <img src="images/image-devices.png" alt="" class="mx-auto">
                                                   <!-- ^^^^^^^ without flexbox, we still have many ways to align elements such as text-center, mx-auto, etc. -->
    </div>
  </section>
</body>

</html>
```

![accession-no-rwd.png](../assets/imgs/accession-no-rwd.png)
