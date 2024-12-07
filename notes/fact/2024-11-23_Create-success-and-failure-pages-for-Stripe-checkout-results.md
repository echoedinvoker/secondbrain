---
date: 2024-11-23
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create success and failure pages for Stripe checkout results

In [[2024-11-23_Pass-Stripe-session-from-backend-to-Stripe-and-return-session-id-to-frontend|backend Stripe obj settings]], we already tell Stripe where to redirect the user after the payment is successful or failed. Now we need to create those pages.

And instead of creating them from scratch, we can add some flexibility to [[2024-11-03_Hero-section-component-example|Hero section component]] and reuse it for both success and failure pages as below:

```svelte
<!-- src/lib/components/HeroSection.svelte  -->
<script lang="ts">
	import phoneCover from '$assets/phone_cover.png';
	import bookCover from '$assets/book_cover.png';
	import Button from './Button.svelte';
	import type { Snippet } from 'svelte';

  // make this component flexible to accept children prop
	interface HeroSectionProps {
		children?: Snippet;
	}

	let { children }: HeroSectionProps = $props(); // extract children prop
  
</script>

<section class="hero">
	<div class="hero-showcase text-center bold">
		<h1 class="purple">MOVE TO</h1>
		<h1 class="purple mb-xs">SPAIN</h1>
		<h4 class="italic semi-bold">There's no need to make the same mistakes I made.</h4>
		<img class="book-cover" src={bookCover} alt="" />
		<img class="phone-cover" src={phoneCover} alt="" />
	</div>
	<div class="hero-text white text-center">

    <!-- if children prop is passed, render it, or render default content -->
		{#if children}
			{@render children()}
		{:else}
			<h1>THIS EBOOK WILL</h1>
			<h1>SAVE YOUR MONEY,</h1>
			<h1 class="mb-m">TIME & NERVES</h1>
			<p class="light-grey mb-l">
				I moved to Spain in 2017 and I made a lot of mistakes. I lost a lot of money, time and
				nerves. I don't want you to make the same mistakes. That's why I wrote this ebook. It will
				save you a lot of money, time and nerves. It will help you to move to Spain smoothly and
				without any problems.
			</p>
			<Button>Get the ebook</Button>
		{/if}
	</div>
</section>

<!-- ...omit styles -->
```

Then, we can reuse this component for both success and failure pages:

```svelte
<!-- src/routes/checkout/success/+page.svelte  -->
<script lang="ts">
	import { HeroSection } from '$components';
</script>

<HeroSection>
	<h1>THANK YOU FOR</h1>
	<h1 class="mb-l">PURCHASING</h1>
	<p class="light-grey mb-s">
		You've made a great decision by choosing this guide to help you on your journey to Spain. We’re
		confident that the insights and advice contained in this ebook will save you time, money, and
		unnecessary stress as you plan your move.
	</p>
	<p class="light-grey mb-s">
		An email with your ebook is on its way to your inbox, along with a separate purchase
		confirmation. Please keep an eye out for the email. If you don’t see them within the next few
		minutes, be sure to check your spam or promotions folder, just in case.
	</p>
	<p class="light-grey">
		We're here to support you every step of the way. If you have any questions or need further
		assistance, don’t hesitate to reach out. Enjoy your read and best of luck with your move!
	</p>
</HeroSection>
```

```svelte
<!-- src/routes/checkout/failure/+page.svelte  -->
<script lang="ts">
	import { HeroSection, Button } from '$components';
</script>

<HeroSection>
	<h1>ERROR WHILE</h1>
	<h1 class="mb-l">PURCHASING</h1>
	<p class="light-grey mb-s">
		We’re sorry, but it looks like there was an issue with your purchase. Don’t worry—these things
		happen, and we're here to help.Please double-check your payment details and try again.
	</p>
	<p class="light-grey mb-s">
		We apologize for any inconvenience this may have caused and appreciate your patience. We’re
		committed to ensuring that you get the guide you need to make your move to Spain as smooth as
		possible.
	</p>
	<p class="light-grey mb-m">
		If you’d like to try again, please click the button below to return to the checkout page.
	</p>
	<Button>Try again</Button>
</HeroSection>
```
