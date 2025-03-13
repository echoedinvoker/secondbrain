---
date: 2025-02-25
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Few Shot Prompt

We can provide some response examples in the prompt for the LLM to understand the answers we want.


## Example of Zero-Shot Prompt

Describe a image about a flying pig in the sky:


## Example of One-Shot Prompt

Describe a image about a flying pig in the sky:
pink pig, blue sky, clouds, wings, tail          <--- example

Describe a image about a flying pig in the sky:  <--- stop


## Example of Few-Shot Prompt

Describe a image about a flying pig in the sky:
pink pig, blue sky, clouds, wings, tail          <--- example

Describe a image about a flying pig in the sky:
brown pig, grey sky, rain, wings                 <--- example

Describe a image about a flying pig in the sky:
black pig, red sky, fire, wings, tail, horns     <--- example

Describe a image about a flying pig in the sky:  <--- stop



## Real test result

I found that only in complete mode, the response of LLM will be affected by examples, but in chat mode, it will not be affected by examples.




