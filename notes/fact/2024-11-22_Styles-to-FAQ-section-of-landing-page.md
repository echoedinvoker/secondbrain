---
date: 2024-11-22
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Styles to FAQ section of landing page

After [[2024-11-21_HTML-struct-of-FAQ-section|HTML struct of FAQ section]], we need to add some styles to make it look good.

```svelte
<!-- src/lib/components/FaqSection.svelte -->
<script>
	import { Button, FaqItem } from '$components';
	import twitterIcon from '$assets/icons/twitter.webp';
</script>

<section class="faq-section">
	<h2 class="mb-l">Frequently asked questions</h2>
<!-- ^^^^^^^^^^^^ using class utility to add margin bottom is a good idea -->
	<div class="faq-container mb-m">
                       <!-- ^^^^ using class utility to add margin bottom is a good idea -->
		<FaqItem />
<!-- ^^^^^^^ we will make it like accordion item by styling it in other topic -->
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

<!-- Add styles to the section below -->
<style>
  /* section styles are repeated in every section files, so we can use a global style file to store all the section styles */
	.faq-section {
		padding: 80px 0;
		margin-left: 12vw;
		margin-right: 20vw;
		max-width: 1150px;
	}

  /* if we want to center the content which includes multiple elements, we can use flexbox as shown below */
	.additional-info {
		display: flex;
		flex-direction: column;
		align-items: center;
		color: grey;
	}

	.twitter-icon {
		width: 16px;
		height: 16px;
	}
	.additional-info a {
		text-decoration: underline;
	}
</style>
```
```css
/* src/app.css */

/* ... */

/* convert .faq-section to a global class to remove repetition */
.landing-page-section {
  padding: 80px 0;
  margin-left: 12vw;
  margin-right: 20vw;
  max-width: 1150px;
}

```

```svelte
<script>
	import { Button, FaqItem } from '$components';
	import twitterIcon from '$assets/icons/twitter.webp';
</script>

<section class="landing-page-section">
           <!-- ^^^^^^^^^^^^^^^^^^^^ -->
	<h2 class="mb-l">Frequently asked questions</h2>
	<div class="faq-container mb-m">
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

<!-- remove .faq-section styles from here -->
<style> 
	.additional-info {
		display: flex;
		flex-direction: column;
		align-items: center;
		color: grey;
	}
	.twitter-icon {
		width: 16px;
		height: 16px;
	}
	.additional-info a {
		text-decoration: underline;
	}
</style>
```

We still need to style the `FaqItem` component to make it look like an accordion item. We will do that in the [[2024-11-22_Styles-to-FAQ-item-of-FAQ-accordion|this topic]].
