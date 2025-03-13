---
date: 2025-02-22
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Building an AI LangChain Chat Assistant: Memory

To integrate the frontend conversation to the backend core, we need to add a new session state for passing to the backend.

File: /home/matt/Github/documentation-helper/main.py
```python
from typing import Set
from backend.core import run_llm
import streamlit as st


st.header("LangChain Udemy Course- Documentation Healper Bot")

prompt = st.text_input("Prompt", placeholder="Enter your prompt here...")

if "user_promt_history" not in st.session_state:
    st.session_state.user_promt_history = []

if "chat_answers_history" not in st.session_state:
    st.session_state.chat_answers_history = []

# create new session state for memory (integrating to backend core)
if "chat_history" not in st.session_state:
    st.session_state.chat_history = []


def create_sources_string(source_urls: Set[str]) -> str:
    if not source_urls:
        return ""
    sources_list = list(source_urls)
    sources_list.sort()
    sources_string = "Sources: \n"
    for i, source in enumerate(sources_list):
        sources_string += f"{i+1}. {source} \n"
    return sources_string

if prompt:
    with st.spinner("Generating text..."):
        response = run_llm(query=prompt)
        sources = set([doc.metadata["source"] for doc in response["source_documents"]])
        formatted_response = f"{response['result']}\n {create_sources_string(sources)}"

    st.session_state.user_promt_history.append(prompt)
    st.session_state.chat_answers_history.append(formatted_response)

    # chat history will be used to remember the conversation
    st.session_state.chat_history.append(('human', prompt))
    st.session_state.chat_history.append(('ai', response['result']))
    #                                     ^^^^ this is that llm likes chat history format
    #                                          to be in the form of (role, message)

if st.session_state.chat_answers_history:
    for generated_response, user_query in zip(st.session_state.chat_answers_history, st.session_state.user_promt_history):
        st.chat_message("user").write(user_query)
        st.chat_message("bot").write(generated_response)
```

Then, we will pass chat history whenever we call the `run_llm` function of the backend core. This will allow the AI to remember the conversation and generate more contextually relevant responses. 
```python

# ...

if prompt:
    with st.spinner("Generating text..."):
        response = run_llm(
            query=prompt,
            chat_history=st.session_state.chat_history # pass chat history to the backend core
        )
        sources = set([doc.metadata["source"] for doc in response["source_documents"]])
        formatted_response = f"{response['result']}\n {create_sources_string(sources)}"

    st.session_state.user_promt_history.append(prompt)
    st.session_state.chat_answers_history.append(formatted_response)

# ...

```

File: /home/matt/Github/documentation-helper/backend/core.py
```python
import os
from typing import Any, Dict, List
from dotenv import load_dotenv
from langchain_core.prompts import PromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_pinecone import PineconeVectorStore
from langchain import hub
from langchain_core.output_parsers import StrOutputParser


load_dotenv()

source_documents = []

def format_docs(docs):
    source_documents.extend(docs)
    return "\n\n".join(doc.page_content for doc in docs)

# create a function to format chat history from list of tuple to string
def format_chat_history(chat_history):
    return "\n".join([f"{msg[0]}: {msg[1]}" for msg in chat_history])

def run_llm(query: str, chat_history: List[Dict[str, Any]] = []):
#                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ accept chat history as input from the frontend
    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")
    prompt = hub.pull("langchain-ai/retrieval-qa-chat")

    # course video pull hub "langchain-ai/chat-langchain-rephrase" but it doesn't work for me... so I write it manually
    rephrase_template = """
    Given the following conversation and a follow up question, rephrase the follow up question to be a standalone question and conversation history, if Chat History is provided.

    Chat History:
    {chat_history}
    Follow Up Input: {input}
    rephrased conversation and follow up question:"""
#   ^^^^^^^^^^^^^^^^^^^^^^^^^^ mainly add the conversation part 
#                              hub "langchain-ai/chat-langchain-rephrase" doesn't have this part, so LLM response only has the refined question

    # because what I wrote above is not prompt yet, I need to convert it to prompt with PromptTemplate
    rephrase_prompt = PromptTemplate(
        input_variables=["chat_history", "input"],
        template=rephrase_template,
    )

    retriever = PineconeVectorStore(
        index_name=os.environ["INDEX_NAME"],
        embedding=OpenAIEmbeddings(model="text-embedding-3-small"),
    ).as_retriever()

    # create another chain just for rephrasing from chat history and follow up question to a standalone string (which includes the rephrased question and the conversation)
    rephrase_chain = (
        rephrase_prompt
        | llm
        | StrOutputParser()
    )

    rag_chain = (
        {
            "context": retriever | format_docs,
            "input": RunnablePassthrough()
        }
        | prompt
        | llm
    )

    # invoke the rephrase chain with the input and chat history first
    rephrase = rephrase_chain.invoke({
        "input": query,
        "chat_history": format_chat_history(chat_history)
    })

    # then invoke the rag chain with the rephrased string
    res = rag_chain.invoke(rephrase)
    #                      ^^^^^^^^ replace `query` with `rephrase`

    return {
        "query": query,
        "result": res.content,
        "source_documents": source_documents
    }



if __name__ == "__main__":
    res = run_llm(
        query="What did I just ask?",
        chat_history=[
            {"role": "human", "message": "What is the capital of France?"},
            {"role": "ai", "message": "The capital of France is Paris."}
        ]
    )
    print(res)


```


The prompt for rephrasing the course video is to directly pull from the hub "langchain-ai/chat-langchain-rephrase" and implement it using create_history_aware_retriever. However, I find it too abstract, so I have rewritten it as above.

We can even group two chains together, so it only need to invoke once, which is more efficient. 

```python
    rephrase_chain = (
        rephrase_prompt
        | llm
        | StrOutputParser()
    )

    rag_chain = (
        rephrase_chain  # insert the rephrase chain here
        | {
            "context": retriever | format_docs,
            "input": RunnablePassthrough()
        }
        | prompt
        | llm
    )

    res = rag_chain.invoke({
#   ^^^ replace `rephrase` with `res` which used to be the output of run_llm()
        "input": query,
        "chat_history": format_chat_history(chat_history)
    })

    # res = rag_chain.invoke(rephrase)  # Now we only need to invoke once

```
