---
date: 2025-02-25
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# New LangChain Memory Theory Deepdive (LangGraph)

Memory has three ways to handle previous conversations:

- Simply stuffing previous messages into a chat model prompt
- The above, but trimming old messages to reduce the amount of distracting information the model has to deal with.
- More complex modifications like synthesizing summaries for long running conversations.

Where are we going to save those messages and how we're going to persist them?

New way to do it with LangChain ecosystem is to use `LangGraph` where we have their `check pointers`, which are going to help use persist those messages.


