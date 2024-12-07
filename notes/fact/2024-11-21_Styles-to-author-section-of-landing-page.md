---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styles to author section of landing page

After [[2024-11-04_HTML-structure-of-AuthorSection|HTML structure]], here we start to add styles to it.

```svelte
<script lang="ts">
	import authorPic from '$assets/profile_pic.jpg';
</script>

<section class="author-section">
                ^^^^^^^^^^^^^^  section container style
	<h2>About the Author</h2>
	<div class="author-container">
              ^^^^^^^^^^^^^^^^ flex container for author image and text
		<div class="author-text">
                ^^^^^^^^^^^ 55% width of viewport
			<p class="mb-xs">
				Matthew is a software engineer and writer from the UK. He has been working in the tech
				industry for over 10 years and has a passion for writing about technology and software
				development. Matthew is a self-taught developer and has worked with a variety of programming
				languages and frameworks.
			</p>
			<p>
				He is currently working as a freelance software developer and technical writer. In his free
				time, Matthew enjoys reading, writing, and playing video games. He is also an avid fan of
				science fiction and fantasy literature. Matthew is always looking for new opportunities to
				learn and grow as a developer and writer.
			</p>
		</div>
		<img src={authorPic} alt="Author Matthew Chang" />   // image of author, 40% width of viewport with border and shadow
									                                       // so left 5% gap between image and text
	</div>
</section>

// implement styles below:
<style>
	.author-section {
		padding: 80px 0;
		margin-left: 12vw;  // margin left and right are different
		margin-right: 20vw; // to let section not centered but a bit left aligned
		max-width: 1150px;
               ^^^^ common width for a section or container
	}

	.author-container {
		display: flex;
		justify-content: space-between;
	}

	.author-container img {
		width: 40%;
		border: 6px solid black;
		box-shadow: 0 4px 24px rgba(0, 0, 0, 0.5);
	}

	.author-text {
		width: 55%;
	}
</style>

```
