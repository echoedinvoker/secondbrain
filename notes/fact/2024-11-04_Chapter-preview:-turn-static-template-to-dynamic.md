---
date: 2024-11-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Chapter preview: turn static template to dynamic

Following [[2024-11-04_Chapter-preview-styling|chapter preview styling]], now we add data in script tag, let's try to make the template dynamic by using [[2024-11-02_Usefull-blocks|svelte blocks]], [[2024-11-01_Using-state-rune-to-create-reactive-state|state rune]] and [[2024-11-01_derive-state-from-existing-state|derive rune]].


```svelte src/lib/components/ChapterPreview.svelte

<script lang="ts">
  const chapters = [
    {
      number: 1,
      title: 'Navigating the Spanish Bureaucracy',
      strapline: 'Understanding Visas, Residency, and Legal Essentials',
      excerpt: `Moving to Spain involves navigating a labyrinth of bureaucratic processes that can feel overwhelming at first. This chapter breaks down the essential steps required to secure your visa, establish residency, and handle other legal form alities. Whether you’re planning a short-term stay or seeking permanent residency, understanding Spain’s legal framework is crucial. We’ll explore the different types of visas available, from student to work visas, and provide tips on how to streamline your application process. Additionally, this chapter covers essential documentation, such as obtaining a Número de Identidad de Extranjero (NIE), which is your key to unlocking many services in Spain. We’ll also delve into the process of registering with the local town hall, known as empadronamiento, and the importance of understanding the tax implications of your move. Whether you’re a retiree, a digital nomad, or someone relocating for work, this chapter will guide you through the maze of paperwork and official procedures with practical advice and insider tips, ensuring that you’re legally prepared for your new life in Spain.`
    },
    {
      number: 2,
      title: 'Finding Your Dream Home in Spain',
      strapline: 'From Coastal Villas to Urban Apartments: Where and How to Live',
      excerpt: `
Spain offers a diverse array of living options, from picturesque coastal towns to bustling city centers. This chapter is your guide to finding the perfect home that suits your lifestyle and budget. We’ll start by exploring Spain’s most popular regions for expats, highlighting the unique qualities of each. Whether you’re drawn to the cosmopolitan vibe of Madrid and Barcelona, the relaxed atmosphere of the Costa del Sol, or the cultural richness of Andalusia, understanding the local property market is key. We’ll discuss the pros and cons of renting versus buying, and provide practical advice on how to navigate Spain’s real estate market. You’ll learn about the different types of properties available, from modern apartments to traditional fincas, and the steps involved in securing a lease or purchase. We’ll also cover important considerations such as proximity to amenities, transportation, and local schools. This chapter will equip you with the knowledge to make an informed decision, ensuring that your new home in Spain meets all your expectations and provides a comfortable base for your new life.`
    },
    {
      number: 3,
      title: 'Mastering the Spanish Job Market',
      strapline: 'Strategies for Employment and Freelancing in a New Culture',
      excerpt: `Finding a job in Spain can be a challenging but rewarding experience, especially if you’re not familiar with the local job market and work culture. This chapter provides a comprehensive overview of the employment landscape in Spain, offering strategies to help you secure a job or establish a successful freelance career. We’ll start by examining the sectors with the most opportunities for expats, such as tourism, education, technology, and healthcare. You’ll learn how to tailor your CV and cover letter to meet Spanish expectations, and we’ll provide tips on how to network effectively in a culture where personal connections often play a crucial role in job hunting. For those considering freelancing, we’ll explore the legal requirements for becoming autónomo, Spain’s version of self-employment, including tax obligations and social security contributions. Additionally, we’ll discuss the importance of learning Spanish to enhance your job prospects and integrate into the workplace. Whether you’re looking to climb the corporate ladder, start a business, or work remotely, this chapter offers valuable insights to help you navigate the Spanish job market successfully.`
    },
    {
      number: 4,
      title: 'embracing the spanish lifestyle',
      strapline: 'cultural norms, social etiquette, and making new connections',
      excerpt: `moving to spain isn’t just about changing your location; it’s about immersing yourself in a new way of life. this chapter explores the vibrant spanish lifestyle, offering insights into cultural norms, social etiquette, and how to make meaningful connections in your new home. we’ll begin by discussing the importance of family and community in spain, where relationships are often built around shared meals, festivals, and local traditions. you’ll learn about the spanish concept of mañana, and how the relaxed approach to time influences daily life. we’ll also delve into social etiquette, from greeting people with a kiss on the cheek to navigating the intricacies of dining out. whether you’re attending a local fiesta or joining a conversation at the neighborhood café, understanding these cultural nuances will help you integrate more easily. additionally, we’ll offer tips on how to build a social network, whether through language classes, expat groups, or engaging in local hobbies and sports. by the end of this chapter, you’ll be well-prepared to embrace the rich, communal, and vibrant lifestyle that makes Spain so unique.`
    },
    {
      number: 5,
      title: 'Budgeting and Finances in Spain',
      strapline: 'Managing Costs, Taxes, and Banking in Your New Home',
      excerpt: `Successfully managing your finances is crucial to enjoying a stress-free life in Spain. This chapter provides practical guidance on how to budget for your new life, manage your expenses, and navigate Spain’s financial systems. We’ll start by discussing the cost of living in different regions of Spain, from the more affordable countryside to the pricier urban centers. You’ll learn how to create a realistic budget that covers essentials such as housing, utilities, groceries, and healthcare, while also allowing for the enjoyment of Spain’s culinary delights and cultural activities. We’ll also delve into the Spanish banking system, offering tips on how to open a bank account, choose the right financial products, and understand local banking fees. Taxation is another key area we’ll cover, with explanations of how to file taxes as a resident or non-resident, and the implications of Spain’s tax treaties with other countries. Whether you’re planning to retire, work, or invest in Spain, this chapter will equip you with the financial knowledge to make the most of your resources and enjoy a comfortable life in your new home.`
    }
  ];
</script>

<section class="chapter-preview">
  <h2 class="mb-l">What you're getting</h2>
  <div class="chapter-container">
    <ul>

      <!-- repeat block -->
      <li>
        <button
          class="chapter-title selected-chapter-title"
          <!--                 ^^^^^^^^^^^^^^^^^^^^^^ this cannot directly get from data, so we should create a state to track current selected chapter -->
          aria-controls="chapter-info-1"
          aria-expanded="true"
          <!--           ^^^^                         same, need a state to track the current chapter -->
        >
          <h3>Chapter 1: Navigting the Spanish bureaucracy</h3>
        </button>
      </li>
      <!-- end of repeat block, we can use #each to iterate the same block with data, if block is complex, we can use #snippet also -->

      <li>
        <button class="chapter-title" aria-controls="chapter-info-2" aria-expanded="false">
          <h3>Chapter 2: Legal requirements for moving to Spain</h3>
        </button>
      </li>
      <li>
        <button class="chapter-title" aria-controls="chapter-info-3" aria-expanded="false">
          <h3>Chapter 3: Finding a place to live in Spain</h3>
        </button>
      </li>
    </ul>
    <div class="chapter-info">
      <h3 class="chapter-strapline italic mb-s">
        Understanding Visas, Residencia, and Legal Essentials
      </h3>
      <p>
        Moving to Spain is a dream for many, but the reality of navigating the Spanish bureaucracy
        can be a nightmare. In this chapter, we'll walk you through the essential legal requirements
        for moving to Spain, from visas to residencia and everything in between so you can start
        your new life in Spain with confidence. We'll also share some tips and tricks for making the
        process as smooth as possible. Moving to Spain is a dream for many, but the reality of
        navigating the Spanish bureaucracy can be a nightmare. In this chapter, we'll walk you
        through the essential legal requirements for moving to Spain, from visas to residencia and
        everything in between so you can start your new life in Spain with confidence. We'll also
        share some tips and tricks for making the process as smooth as possible.
  <!--  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ when we have state to track current selected chapter number, we can use $derive to get more informatio about the selected chapter -->
      </p>
    </div>
  </div>
</section>

```

Turn to dynamic template as below:

```svelte src/lib/components/ChapterPreview.svelte

<script lang="ts">
  interface Chapter {
    number: number;
    title: string;
    strapline: string;
    excerpt: string;
  }

  const chapters = [
    //...
  ];

  let selectedChapterNumber = $state(1);
  let selectedExcerpt = $derived(chapters.find((chapter) => chapter.number === selectedChapterNumber)?.excerpt || '');
</script>

{#snippet chapterList(chapter: Chapter)}
  <li>
    <button
      class="chapter-title"
      class:selected-chapter-title={chapter.number === selectedChapterNumber}
      aria-controls={`chapter-info-${chapter.number}`}
      aria-expanded={`${chapter.number === selectedChapterNumber}`}
      onclick={() => (selectedChapterNumber = chapter.number)}
    >
      <h3>Chapter {chapter.number}: {chapter.title}</h3>
    </button>
  </li>
{/snippet}

<section class="chapter-preview">
  <h2 class="mb-l">What you're getting</h2>
  <div class="chapter-container">
    <ul>
      {#each chapters as chapter}
        {@render chapterList(chapter)}
      {/each}
    </ul>
    <div class="chapter-info">
      <h3 class="chapter-strapline italic mb-s">
        Understanding Visas, Residencia, and Legal Essentials
      </h3>
      <p>{selectedExcerpt}</p>
    </div>
  </div>
</section>

```

Besides runes and blocks, above I also use `class:` directive to write conditional classes more clearly. You can check the details [[2024-11-04_Better-writing-for-conditional-classes-in-Svelte|here]].
```svelte
