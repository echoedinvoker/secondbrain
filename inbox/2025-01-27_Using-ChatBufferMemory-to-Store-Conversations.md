---
date: 2025-01-27
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Using ChatBufferMemory to Store Conversations

The course uses `ConversationBufferMemory` and `MessagesPlaceholder` to solve the issues in [[2025-01-27_Implementing-a-Chat-Chain|Implementing a Chat Chain]].

However, in the new version of LangChain, it is recommended to use LangGraph directly to handle this issue.

```bash
pip install langgraph
```

```py
import uuid
from langchain_core.runnables import RunnableConfig
from langchain_openai import ChatOpenAI
from langgraph.checkpoint.memory import MemorySaver # stores the conversation in the memory
from langgraph.graph import START, MessagesState, StateGraph # START is the starting node of the graph
                                                             # MessagesState is the state schema of the graph
                                                             # StateGraph is the graph class to create a graph

chat_model = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7
)

# create a graph with the state schema MessagesState
workflow = StateGraph(state_schema=MessagesState)

def call_model(state: MessagesState):
    print(state) # debug purpose, just check the structure of MessagesState
    response = chat_model.invoke(state["messages"])
    return {"messages": response} # response is AIMessage, we put it under the key "messages"
                                  # so that it will be push to the state schema MessagesState key "messages"

# Graph structure building
workflow.add_edge(START, "model") # START -> `model`, `model` here just a node name
workflow.add_node("model", call_model) # create a node `model` with the function `call_model`

# Just set the memory type to save the state
memory = MemorySaver()
app = workflow.compile(checkpointer=memory)

# create a unique id for the thread
thread_id = uuid.uuid4()
config = RunnableConfig( # currently, graph config must be passed as a RunnableConfig
    configurable={
        "thread_id": thread_id
    }
)


while True:
    content = input(">> ")

    # to invoke the graph, START --> `model` and call the function `call_model` with {"messages": [{"role": "human", "content": content}]}
    result = app.invoke(
        {"messages": [{"role": "human", "content": content}]},
        config=config
    )

    # `call_model` function will return AIMessage and graph will push it to the state schema MessagesState key "messages"
    # so we can print the last message content as AI response to the user
    print(result["messages"][-1].content)
```

Let's run the code above and see the output.

```bash
$ python main.py

>> 2 + 3

# print `state` in `call_model` function
{
  'messages': [
    HumanMessage(
      content='2 + 3',
      additional_kwargs={},
      response_metadata={},
      id='e6a2adb4-d4ba-416a-9521-60717133c885')
  ]
}

# print `result["messages"][-1].content`
2 + 3 equals 5.

>> add 5 more

# print `state` in `call_model` function the second time
{
  'messages': [
    HumanMessage(
      content='2 + 3',
      additional_kwargs={},
      response_metadata={},
      id='e6a2adb4-d4ba-416a-9521-60717133c885'
    ),
    AIMessage(  # `call_model` function return AIMessage last time is already stored in the state schema MessagesState key "messages"
      content='2 + 3 equals 5.',
      additional_kwargs={'refusal': None},
      response_metadata={ ... },
      id='run-6416cc73-1700-42ae-bf42-b3a8da6c9180-0',
      usage_metadata={
        'input_tokens': 11,
        'output_tokens': 9,
        'total_tokens': 20,
        'input_tok en_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}}
    ),
    HumanMessage( # the new message from the user, which will be pushed to the state schema MessagesState key "messages"
      content='add 5 more',
      additional_kwargs={},
      response_metadata={},
      id='664cf1c0-3910-4a87-8949-10cb3e676b62'
    )
  ]
}
# the whole state schema MessagesState key "messages" will be passed to the `call_model` function as the prompt to LLM

# print `result["messages"][-1].content` the second time
5 + 5 equals 10.
>>
```

Compared to the past way of using LangChain, using LangGraph can more conveniently handle the storage issues of conversations. This method can better manage the state of conversations and can more flexibly expand the functionality of conversations.



