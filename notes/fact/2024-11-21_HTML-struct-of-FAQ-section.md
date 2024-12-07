---
date: 2024-11-21
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# HTML struct of FAQ section

```svelte
<!-- src/lib/components/FaqSection.svelte -->
<script>
	import { Button, FaqItem } from '$components';
	import twitterIcon from '$assets/icons/twitter.webp';
</script>

<section class="faq-section">
	<h2>Frequently asked questions</h2>
	<div class="faq-container"> // there are multiple faq items inside
		<FaqItem />  // better use another component for each faq item
		<FaqItem />
		<FaqItem />
		<FaqItem />
		<FaqItem />
	</div>
	<div class="additional-info">
		<Button>BUY NOW</Button>
		<p>Any other questions?</p>
		<a href="https://x.com/MATT">
			<p>
				Hit me up on twitter <img src={twitterIcon} class="twitter-icon" alt="" />
			</p>
		</a>
	</div>
</section>

<!-- src/lib/components/FaqItem.svelte -->
<script>
	import chevronDown from '$assets/icons/chevron-down.svg';
</script>

<button class="container">
	<div class="question-and-answer">
		<p class="question">What will I learn in this course?</p>
		<p class="answer">
			This course will teach you how to build a Svelte app from scratch. You'll learn about the core
			concepts of Svelte, how to use its features, and how to deploy your app.
		</p>
	</div>
	<img src={chevronDown} class="chevron" alt="" />
</button>

```

