---
date: 2025-03-02
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Corrective RAG flow

In the next couple of topics, we'll be implementing Zeewrap or correctly wrapped based on the Corrective-RAG research paper. This is an advanced wrap technique that is going to help us get more quality answer when performing augmentation generation

And the basic concept of zeewrap is pretty straightforward. We first want to start by taking our query, performing in the vector search, semantic search, and retrieving relevant documents from our vector store. After we have all those documents, we want to start in self-reflect to critique those documents, and to determine whether they are indeed relevant to our original query or not.

if all of the documents are relevant to our query, then this is a happy flow and we simply want to augment our original prompt and send everything to the end like we always do in RAG.

However, if we find documents that we find out that are not relevant to our query, of course we want to filter them out, and we also want to perform some external search on the internet to get to more information. And to augment our prompt with that real time information we get from online.

And this technique is going to give us much more quality responsive.

