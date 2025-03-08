---
date: 2025-02-20
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Retrieval + Augmentation + Generation = RAG

For this topic, we need to embed user queries to retrieve the most similar documents from Pinecone, then augment the query with these documents to generate a prompt to send to the LLM for generating answers.

```sh
 tree
.
├── backend # create a package `backend`
│   ├── core.py # we'll put augmentations and generation logic here
│   └── __init__.py
├── ingestion.py
├── langchain-docs
├── LICENSE
├── Pipfile
├── Pipfile.lock
└── README.md

```

Referring to [[2025-02-18_Chat-With-Your-PDF:-FAISS-Local-Vectorstore|the examples]] from the previous topic, we can easily create a RAG chain to address this issue.


```py
import os
from dotenv import load_dotenv
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_pinecone import PineconeVectorStore
from langchain import hub


load_dotenv()

def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

def run_llm(query: str):
    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")
    prompt = hub.pull("langchain-ai/retrieval-qa-chat")
    retriever = PineconeVectorStore(
        index_name=os.environ["INDEX_NAME"],
        embedding=OpenAIEmbeddings(model="text-embedding-3-small"),
    ).as_retriever()

    rag_chain = (
        {
            "context": retriever | format_docs,
            "input": RunnablePassthrough()
        }
        | prompt
        | llm
    )

    res = rag_chain.invoke(query)
    return res



if __name__ == "__main__":
    res = run_llm("What is a LangChain chain?") # return an AIMessage object
    print(res.content)

```

The course is done using compose chains (create_stuff_documents_chain, create_retrieval_chain), but I feel that this approach in the course is too abstract and difficult to understand. So I use the LCEL method to do it, which is easier to understand and easier to debug.

It should be noted that the method of the course returns results including retrieved documents, query, and answer, but our method only returns the response of the LLM (AIMessage object) in the end, so it is important to pay attention to this difference.






