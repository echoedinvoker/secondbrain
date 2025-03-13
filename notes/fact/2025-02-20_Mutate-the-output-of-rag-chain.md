---
date: 2025-02-20
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Mutate the output of rag chain

In order for the output of the function run_llm created in [[2025-02-20_Retrieval-+-Augmentation-+-Generation-=-RAG|the previous topic]] to match the requirements of the frontend UI part that we are going to work on next, we need to make some mutations to the output of run_llm.

```py
import os
from dotenv import load_dotenv
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_pinecone import PineconeVectorStore
from langchain import hub


load_dotenv()

source_documents = [] # create a variable to store the retrieved documents

def format_docs(docs):
    source_documents.extend(docs) # store the retrieved documents
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

    # rebuild the returned object to fit the frontend UI requirements
    return {
        "query": query,
        "result": res.content,
        "source_documents": source_documents
    }



if __name__ == "__main__":
    res = run_llm("What is a LangChain chain?")
    print(res)

```

Let's run this package and see the output. 

```sh
 python -m backend.core

{
    'query': 'What is a LangChain chain?',
    'result': 'A LangChain chain is a sequence of operations or processes that are linked together to perform a specif ic task, such as question answering or data processing. Chains can combine multiple components, such as language models and tools, to achieve a desired ou tcome. For example, a question-answering chain may involve loading a language model, processing input documents, and generating responses based on the inf ormation retrieved. Chains are designed to facilitate complex workflows by integrating various functionalities in a structured manner.',
    'source_documents ': [Document(id='bed408e9-2d77-42d9-b099-7f10ba72eda7', metadata={'source': 'https://api.python.langchain.com/en/latest/community_api_reference.html'}, page_content='langchain_commun...
    ...
}
