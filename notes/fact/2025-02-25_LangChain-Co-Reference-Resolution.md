---
date: 2025-02-25
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# LangChain Co Reference Resolution

## LLM is stateless

LLM is stateless, meaning that it does not store any information about the previous sentences. So it won't remember the past conversations by itself.


## Co-reference resolution

"I voted for **Nader** because **he** was most aligned with **my** values." **she** said.

Understanding the person, thing, or object that each pronoun refers to in the example sentences above is called `Co-reference resolution`. Because LLM is stateless, it will have difficulty resolving this issue without providing context.


## Token limit when conversation too long

We have previously done a project that adds the history of conversations to each prompt, so the LLM can solve the problem of co-reference resolution. However, when the conversation is too long, the number of tokens will exceed the limit.


## LangChain offers a solution: Memory

LangChain offers a solution to this problem by providing a memory feature. We'll see how this feature works in the following topics.
```

