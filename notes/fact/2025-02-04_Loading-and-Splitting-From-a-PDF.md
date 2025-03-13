---
date: 2025-02-04
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Loading and Splitting From a PDF

In this topic, we want to implement the loading and splitting of a PDF file. We will implement it in function `create_embeddings_for_pdf` of module `app.chat`.

File: /home/matt/Projects/pdf/app/chat/create_embeddings.py
```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

def create_embeddings_for_pdf(pdf_id: str, pdf_path: str):
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=500,
        chunk_overlap=100
    )

    loader = PyPDFLoader(pdf_path)
    docs = loader.load_and_split(text_splitter)

```

Above, we have completed the part of loading PDF and splitting documents in the `create_embeddings_for_pdf` function, but this function is not yet complete. We will complete this function in the next topic.
