---
date: 2024-11-22
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Make FAQ section of landing page dynamic

After [[2024-11-22_Styles-to-FAQ-section-of-landing-page|styles to FAQ section of landing page]] and [[2024-11-22_Styles-to-FAQ-item-of-FAQ-accordion|styles to FAQ item of FAQ accordion]], we should make the FAQ section of the landing page dynamic.

```svelte
<!-- src/lib/components/FaqSection.svelte -->
<script lang="ts">
	import { Button, FaqItem } from '$components';
	import twitterIcon from '$assets/icons/twitter.webp';

  // add faqs data
	const faqs = [
		{
			question: 'What is the best thing about the product?',
			answer:
				'The best thing about the product is that it is very easy to use and it is very affordable.'
		},
		{
			question: 'How long does it take to get the product?',
			answer: 'It usually takes 2-3 days to get the product.'
		},
		{
			question: 'What is the return policy?',
			answer:
				'The return policy is very simple. If you are not satisfied with the product, you can return it within 30 days of purchase.'
		},
		{
			question: 'How can I contact customer service?',
			answer: 'You can contact customer service by phone, email, or chat.'
		},
		{
			question: 'What is the warranty on the product?',
			answer: 'The product comes with a 1-year warranty.'
		}
	];

  // state to track which faq item is expanded, -1 means none item expanded
	let currentExpandedIndex = $state(-1);

  // toggle the expanded state of faq item by mutating currentExpandedIndex
	function onclick(index: number) {
		if (currentExpandedIndex === index) {
			currentExpandedIndex = -1;
		} else {
			currentExpandedIndex = index;
		}
	}
</script>

<section class="landing-page-section">
	<h2 class="mb-l">Frequently asked questions</h2>
	<div class="faq-container mb-m">
		{#each faqs as faq, index}
<!-- ^^^^^^^^^^^^^^^^^^^^^^^^ use #each to iterate faqs data, index is used to identify which faq item is expanded -->
			<FaqItem {faq} isExpanded={currentExpandedIndex === index} onclick={() => onclick(index)} />

		{/each}
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

<!-- ...omit styles part -->
```

```svelte
<script lang="ts">
	import chevronDown from '$assets/icons/chevron-down.svg';

  // define FaqItemProps interface
	interface FaqItemProps {
		faq: {
			question: string;
			answer: string;
		};
		isExpanded: boolean;
		[key: string]: any;  // allow any other props
	}

  // get faq and isExpanded from props
	let { faq, isExpanded, ...props }: FaqItemProps = $props();
  //                     ^^^^^^^^ get any other props
</script>

<button
  class="container"
  class:container-expanded={isExpanded}   // make class `container-expanded` dynamic based on isExpanded props
  {...props}    // any other props are spread to the root element here, it make onclick event handler work on the parent 
>
	<div class="question-and-answer">
		<p class="question mb-s">{faq.question}</p>
                         <!-- ^^^^^^^^^^^^ data from props -->
		<p class="answer">{faq.answer}</p>
                 <!-- ^^^^^^^^^^^^ data from props -->
                        
	</div>
	<img src={chevronDown} class="chevron" alt="" />
</button>

<!-- ...omit styles part -->
```

So the state that controls the expansion status of FAQ Item is `currentExpandedIndex`, which is used to generate the `isExpanded` prop passed to FaqItem. FaqItem then uses this prop to determine if it should expand. We choose to make state in the parent component `FaqSection` because it make sure only one item can be expanded at a time.
