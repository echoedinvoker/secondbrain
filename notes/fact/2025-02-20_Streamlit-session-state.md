---
date: 2025-02-20
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Streamlit session state

Streamlit actually constantly listens to user operations and will re-run the entire code each time the user operates. This feature will cause our variables to be constantly reset.

So in order to maintain the state of variables, we need to use `st.session_state` to save variables. It is similar to a python dictionary, but the value of each key underneath it will be saved in the session and not reset.

```py
from typing import Set
from backend.core import run_llm
import streamlit as st


st.header("LangChain Udemy Course- Documentation Healper Bot")

prompt = st.text_input("Prompt", placeholder="Enter your prompt here...")

## Initialize session state variables
if "user_promt_history" not in st.session_state:
    st.session_state.user_promt_history = [] # list to store user prompts, initialized as empty list

if "chat_answers_history" not in st.session_state:
    st.session_state.chat_answers_history = [] # list to store chat answers, initialized as empty list


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

    ## whenever llm generates a response, we append the prompt and response to the session state variables
    st.session_state.user_promt_history.append(prompt)
    st.session_state.chat_answers_history.append(formatted_response)

## if there are any chat history, we display them
if st.session_state.chat_answers_history:
    for generated_response, user_query in zip(st.session_state.chat_answers_history, st.session_state.user_promt_history):
        st.chat_message("user").write(user_query)
        st.chat_message("bot").write(generated_response)

```

Now, we can directly see the output of the backend on the web page.

![stream-session-state-output.png](../assets/imgs/stream-session-state-output.png)


But it is obvious that when we ask the second question, the app has already forgotten the answer to the first question, because we did not integrate the content saved in the session state into the backend rag chain. We will implement this in the next topic.





