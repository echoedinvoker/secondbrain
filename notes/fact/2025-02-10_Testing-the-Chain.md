---
date: 2025-02-10
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Testing the Chain

The course video demonstrates the simple use of ConversationalBuffChain to connect the memory and retriever implemented in previous topics and return them to app.web.

However, the new version of LangChain does not have the ConversationalBuffChain class. It is recommended to use LangGraph to implement the same functionality.

File: /home/matt/Projects/pdf/app/chat/chat.py
```python
from langchain_core.messages import HumanMessage
from app.chat.callbacks.answer_generate import answer_generate
from app.chat.callbacks.query_transform import transform_query
from app.chat.graph_states.graph_state import GraphState
from app.chat.llms.chatopenai import build_llm
from app.chat.models import ChatArgs
from app.chat.vector_stores.pinecone import build_retriever
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver


def build_chat(chat_args: ChatArgs):
    retriever = build_retriever(chat_args)
    llm = build_llm(chat_args)

    workflow = StateGraph(GraphState)

    workflow.add_node("transform_query", transform_query(retriever, llm, chat_args))
    workflow.add_node("generate_answer", answer_generate(llm, chat_args))

    workflow.add_edge(START, "transform_query")
    workflow.add_edge("transform_query", "generate_answer")
    workflow.add_edge("generate_answer", END)

    memory = MemorySaver() # use simple memory, I choose not to use it to handle sqlite database

    graph = workflow.compile(checkpointer=memory)

    def _chat(input):
        response = graph.invoke(
            {"messages": [HumanMessage(content=input)]},
            config={"configurable": {
                "thread_id": chat_args.conversation_id,
            }}
        )
        messages = response.get("messages")
        if messages:
            return messages[-1].content
        return None

    return _chat
```

The above is the main graph construction code. The return _chat is for meeting the needs of app.web usage, so that in app.web, you can simply pass input and get AI response.


File: /home/matt/Projects/pdf/app/chat/llms/chatopenai.py
```python
from langchain_openai import ChatOpenAI

def build_llm(chat_args):
   return ChatOpenAI(model="gpt-4o-mini")


```

File: /home/matt/Projects/pdf/app/chat/callbacks/query_transform.py
```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

from app.chat.models import ChatArgs
from app.web.api import add_message_to_conversation, get_messages_by_conversation_id

query_transform_prompt = ChatPromptTemplate.from_messages([
    ("system", "Given the chat history and the latest user question, "
               "rephrase the question to be a standalone query that can be used for retrieval."),
    ("placeholder", "{chat_history}"),
    ("human", "{question}")
])


def transform_query(retriever, llm, chat_args: ChatArgs):
#                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use return function way to pass retriever, llm and chat_args

    query_transformer = query_transform_prompt | llm | StrOutputParser()

    def _transform_query(state):
        # add input to sqlite database with app.web api
        add_message_to_conversation(
            chat_args.conversation_id,
            role="human",
            content=state.get("messages")[-1].content
        )

        # query chat history from sqlite database with app.web api
        history = get_messages_by_conversation_id(chat_args.conversation_id)

        # if database already has history, history should > 1
        # if history was empty, it should be only one message in the database which above added
        if len(history) > 1:
            latest_question = state.get("messages")[-1].content
            
            # only if there is history, we need to transform new input with history to a single new input
            standalone_query = query_transformer.invoke({
                "chat_history": history, 
                "question": latest_question
            })

            # using computed new input to retrieve docs
            context_docs = retriever.invoke(standalone_query)
            context = "\n\n".join([doc.page_content for doc in context_docs])

            return {"context": context, "messages": state.get("messages")}
        else:
            # if there is no history, directly retrieve docs with input
            context_docs = retriever.invoke(state.get("messages")[-1].content)
            context = "\n\n".join([doc.page_content for doc in context_docs])
            
            return {"context": context, "messages": state.get("messages")}
    return _transform_query
```

File: /home/matt/Projects/pdf/app/chat/callbacks/answer_generate.py
```python
from langchain_core.prompts import ChatPromptTemplate

from app.chat.models import ChatArgs
from app.web.api import add_message_to_conversation

qa_prompt = ChatPromptTemplate.from_messages([
    ("system", "Answer the question based only on the following context:\n{context}"),
    ("human", "{question}")
])

def answer_generate(llm, chat_args: ChatArgs):
#                   ^^^^^^^^^^^^^^^^^^^^^^^^ use return function way to pass llm and chat_args

    # pass retrieved docs and user question to AI model to generate answer
    def _generate_answer(state):
        response = llm.invoke(
            qa_prompt.invoke({
                "context": state.get("context"),
                "question": state.get("messages")[-1].content
            })
        )

        # add AI model response to sqlite database with app.web api
        add_message_to_conversation(
            chat_args.conversation_id,
            role="assistant",
            content=response.content
        )
        
        return {"messages": response}
    return _generate_answer
```

The details of the above method may not be very appropriate. I think the `GraphState` part should need more fields to handle data of different properties, but this example has already shown using `return function` to adapt to the app.web module.
