---
date: 2025-02-18
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Chat With Your PDF: FAISS Local Vectorstore

We are going to work on a new project that involves loading PDFs, retrieving documents from a vector store, and answering questions based on these documents.

We'll use local vector stores FAISS instead of managed services like Pinecone.


## Initialize the Project

Let's initialize the project:

```bash
 mkdir vectorstor-in-memory
 cd vectorstor-in-memory/
 touch main.py
 pipenv shell
 pipenv install langchain pypdf langchain-openai langchain-community langchainhub faiss-cpu
 
 ```

 File: /home/matt/Projects/vectorstor-in-memory/.env
```sh
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
LANGSMITH_API_KEY="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"
LANGSMITH_PROJECT="vectorstore-in-memory"
INDEX_NAME="medium-blogs-embeddings-index"
```

File: /home/matt/Projects/vectorstor-in-memory/main.py
```python

if __name__ == "__main__":
    print("Hello, World!")
```

Download a PDF file to test the project:

```bash
 tree
.
├── main.py
├── Pipfile
├── Pipfile.lock
└── react.pdf  # Downloaded PDF file
```


## Load PDF File and Split into Chunks

Try to use PyPDFLoader to load the PDF file and split it into chunks:

```py
from langchain_community.document_loaders import PyPDFLoader # import 
from langchain_text_splitters import CharacterTextSplitter # import 

if __name__ == "__main__":
    pdf_path = "/home/matt/Projects/vectorstor-in-memory/react.pdf"
    loader = PyPDFLoader(file_path=pdf_path)
    documents = loader.load() # PyPDFLoader returns a list of Document objects, so it already splits the text into chunks, but not under our control
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=30, separator="\n") # we will use our own text splitter to split documents again
    docs = text_splitter.split_documents(documents=documents) # split documents into chunks under our control

```

## Prepare components of chain

```py
from langchain_community.document_loaders import PyPDFLoader
from langchain_core.runnables import RunnablePassthrough
from langchain_text_splitters import CharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain import hub


def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

if __name__ == "__main__":
    pdf_path = "/home/matt/Projects/vectorstor-in-memory/react.pdf"
    loader = PyPDFLoader(file_path=pdf_path)
    documents = loader.load()
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=30, separator="\n")
    docs = text_splitter.split_documents(documents=documents)

    llm = ChatOpenAI()
    prompt = hub.pull("langchain-ai/retrieval-qa-chat") # pull the prompt from the hub, you can check the github repo to see the prompt information
    retriever = FAISS.from_documents(docs, OpenAIEmbeddings()).as_retriever()
    #           ^^^^^ this is local vector store, total vector store will be put in the memory, so it is very fast

```

## Build the Chain

```py
from langchain_community.document_loaders import PyPDFLoader
from langchain_core.runnables import RunnablePassthrough
from langchain_text_splitters import CharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain import hub


def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

if __name__ == "__main__":
    pdf_path = "/home/matt/Projects/vectorstor-in-memory/react.pdf"
    loader = PyPDFLoader(file_path=pdf_path)
    documents = loader.load()
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=30, separator="\n")
    docs = text_splitter.split_documents(documents=documents)


    llm = ChatOpenAI()
    prompt = hub.pull("langchain-ai/retrieval-qa-chat")
    retriever = FAISS.from_documents(docs, OpenAIEmbeddings()).as_retriever()

    # Build the chain with LCEL
    rag_chain = (
        {
            "context": retriever | format_docs,
            "input": RunnablePassthrough()
        #   ^^^^^^^ not `question` here, this prompt parameters are `input` and `context`
        }
        | prompt
        | llm
        | StrOutputParser() # to take answer from the output of the llm directly
    )

```

## Run the Chain

```py
from langchain_community.document_loaders import PyPDFLoader
from langchain_core.runnables import RunnablePassthrough
from langchain_text_splitters import CharacterTextSplitter
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain import hub


def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

if __name__ == "__main__":
    pdf_path = "/home/matt/Projects/vectorstor-in-memory/react.pdf"
    loader = PyPDFLoader(file_path=pdf_path)
    documents = loader.load()
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=30, separator="\n")
    docs = text_splitter.split_documents(documents=documents)

    query = "What is ReAct in machine learning?" # query to ask

    llm = ChatOpenAI()
    prompt = hub.pull("langchain-ai/retrieval-qa-chat")
    retriever = FAISS.from_documents(docs, OpenAIEmbeddings()).as_retriever()

    rag_chain = (
        {
            "context": retriever | format_docs,
            "input": RunnablePassthrough()
        }
        | prompt
        | llm
        | StrOutputParser()
    )

    res = rag_chain.invoke(query) # invoke the chain with the query
    print(res) # print the result

```

Let's run the code:

```bash
 python main.py

/home/matt/.local/share/virtualenvs/vectorstor-in-memory-VerZKRxL/lib/python3.13/site-packages/langsmith/client.py:253: LangSmithMissingAPIKeyWarning: API key must be provided when using hosted
 LangSmith API
  warnings.warn(

ReAct in machine learning is an approach that involves synergizing reasoning and acting capabilities within a large language model. It allows the model to interact with external sources like kn
owledge bases or environments to gather additional information. ReAct is applied to various language and decision-making tasks, showing effectiveness over state-of-the-art baselines and improve
d human interpretability and trustworthiness. The approach involves designing prompts in a straightforward manner, working flexibly across diverse tasks with different action spaces and reasoni
ng needs, and demonstrating strong generalization to new task instances.
```




