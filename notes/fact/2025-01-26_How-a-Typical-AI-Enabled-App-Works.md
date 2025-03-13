---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# How a Typical AI Enabled App Works

Assuming we want to create a web app, this app allows users to upload documents and use AI to answer users' questions based on the content of the documents. We may have two approaches:


## Option 1: Take all the text from the documents + the question and send it to the AI model

- Probably a bad idea.

- Can only send a limited amount of text because of the token limit of the model.

- Even if we could send an unlimited amount, AI models usually don't do well with long text.

- More text = more $ to run the request.


## Option 2 is more complex, below is the step-by-step process of how it works:

First, let's summarize the steps:

*1. When a user uploads a PDF, extract all the text and divide it into chunks.*

*2. Store a summary of what each chunk is talking about.* 

*3. Later, when a user asks a question, find the chunk of text that is most relevant to the user's question.*

*4. Send the most relevant chunk of text along with the user's question to AI model.*

The steps 1-2 are done when the user uploads the document. The steps 3-4 are done when the user asks a question.

Let's break down each step in more detail:


### 1. When a user uploads a PDF, extract all the text and divide it into chunks.

Extracting text from a PDF.

![extract-doc-to-md.png](../assets/imgs/extract-doc-to-md.png)

Splitting the text into chunks.

![split-md-to-chunks.png](../assets/imgs/split-md-to-chunks.png)


### 2. Store a summary of what each chunk is talking about.

Convert each chunk to an embedding with _embedding creation algorithm_.

![convert-chunk-to-embedding.png](../assets/imgs/convert-chunk-to-embedding.png)

![string-to-array-of-numbers.png](../assets/imgs/string-to-array-of-numbers.png)

Embedding is an array of numbers that represents the raw essence of the text. Each number is from -1 to 1.

![embeddings.png](../assets/imgs/embeddings.png)

![always-output-array-of-numbers.png](../assets/imgs/always-output-array-of-numbers.png)

No matter how long the text is, the output is always an array of 1536 numbers.

And we store these embeddings in a vector store, which is based on any traditional database but optimized for storing and searching vectors.

![store-in-vector-store.png](../assets/imgs/store-in-vector-store.png)


### 3. Later, when a user asks a question, find the chunk of text that is most relevant to the user's question.

When a user asks a question, convert the question to an embedding as well.

![user-question-to-embedding.png](../assets/imgs/user-question-to-embedding.png)

Then, find the most relevant chunk by comparing the user's question embedding with the embeddings of the chunks.

![find-most-relavant-chunk.png](../assets/imgs/find-most-relavant-chunk.png)

So, we find the super relevant text chunk.

![super-relevant-text.png](../assets/imgs/super-relevant-text.png)


### 4. Send the most relevant chunk of text along with the user's question to AI model.

Finally, we send the most relevant chunk of text along with the user's question to AI model.

![send-to-ai-model.png](../assets/imgs/send-to-ai-model.png)


