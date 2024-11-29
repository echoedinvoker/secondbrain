---
date: 2024-11-22
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styles to FAQ item of FAQ accordion

After [[2024-11-21_HTML-struct-of-FAQ-section|HTML struct of FAQ section]] and [[2024-11-22_Styles-to-FAQ-section-of-landing-page|Styles to FAQ section of landing page]], we will now focus on the FAQ item to make it look like an accordion.

```svelte
<script>
	import chevronDown from '$assets/icons/chevron-down.svg';
</script>

<button class="container container-expanded">
                    <!-- ^^^^^^^^^^^^^^^^^^ only exists if the accordion item is expanded, we will make it dynamic later -->
	<div class="question-and-answer">
		<p class="question mb-s">What will I learn in this course?</p>
                  <!-- ^^^^ -->
		<p class="answer">
			This course will teach you how to build a Svelte app from scratch. You'll learn about the core
			concepts of Svelte, how to use its features, and how to deploy your app.
		</p>
	</div>
	<img src={chevronDown} class="chevron" alt="" />
</button>

<!-- add styles to the FAQ item below -->
<style>
	.container {
		display: flex;
		justify-content: space-between;
		align-items: flex-start;  /* let flex items start from top of flexbox */
		padding: 12px 0;
		border-bottom: 1px solid grey;  /* because we want to make it look like an accordion, so only bottom border */
		text-align: left;   /* for text content, better to align left */
	}

	.question-and-answer {
		padding-right: 150px;
		flex-grow: 1;
	}

	.answer {
		max-height: 0;   /* to hide the answer */
		overflow: hidden;  /* to hide the answer */
		opacity: 0;  /* better use it for transition */

    /* we can simply use transition: all 0.3s ease-out; but it's better to specify the properties, which is more readable */
		transition:
			max-height 0.3s ease-out,
			opacity 0.3s ease-out;
	}

	button img {
		width: 24px;
		height: 24px;
		transition: transform 0.3s linear;  /* because we want flip effect on chevron */
	}

	.question {
		font-weight: bold;
	}

  /* below styles are for expanded state */
	.container-expanded img {
		transform: rotate(180deg);  /* to flip the chevron when expanded */
	}

	.container-expanded .answer {
		max-height: unset;  /* unset means to use default value again, so it will be auto */
		opacity: 1;
	}
</style>

```

