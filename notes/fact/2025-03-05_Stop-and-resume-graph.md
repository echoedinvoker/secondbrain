---
date: 2025-03-05
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Stop and resume graph

```python
from typing import TypedDict
from langchain_core.runnables import RunnableConfig
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver


class State(TypedDict):
    input: str
    user_feedback: str

def stept_1(state: State) -> None:
    print("--- Step 1 ---")

def human_feedback(state: State) -> None:
    print("--- Human Feedback ---")

def stept_3(state: State) -> None:
    print("--- Step 3 ---")

builder = StateGraph(State)

builder.add_node('step_1', stept_1)
builder.add_node('human_feedback', human_feedback)
builder.add_node('step_3', stept_3)

builder.set_entry_point('step_1')
builder.add_edge('step_1', 'human_feedback')
builder.add_edge('human_feedback', 'step_3')
builder.add_edge('step_3', END)

memory = MemorySaver()

graph = builder.compile(
    checkpointer=memory,
    interrupt_before=["human_feedback"]
)

# start to test the graph
if __name__ == "__main__":
    initial_input = {"input": "Hello world"}
    config: RunnableConfig = {"configurable": {"thread_id": "1"}} # when we use the checkpoint, we need to set the thread_id when we run the graph

    # .stream() will invoke the graph and return something after each node is executed
    for event in graph.stream(initial_input, config, stream_mode="values"):
    #                                                ^^^^^^^^^^^^^^^^^^^^ means return complete state after each node is executed
        print(event)
        #     ^^^^^ this will print the complete state after each node is executed

```

Run the code above, you will see the output like this:

```sh
 python main.py
{'input': 'Hello world'}
--- Step 1 ---

```
We can see that the graph stops after the first node is executed. Because we set the checkpoint, the graph remembers the state and the next node which should be executed. We can write some codes in this interrupt. 

```python
from typing import TypedDict
from langchain_core.runnables import RunnableConfig
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver


class State(TypedDict):
    input: str
    user_feedback: str

def stept_1(state: State) -> None:
    print("--- Step 1 ---")

def human_feedback(state: State) -> None:
    print("--- Human Feedback ---")

def stept_3(state: State) -> None:
    print("--- Step 3 ---")

builder = StateGraph(State)

builder.add_node('step_1', stept_1)
builder.add_node('human_feedback', human_feedback)
builder.add_node('step_3', stept_3)

builder.set_entry_point('step_1')
builder.add_edge('step_1', 'human_feedback')
builder.add_edge('human_feedback', 'step_3')
builder.add_edge('step_3', END)

memory = MemorySaver()

graph = builder.compile(
    checkpointer=memory,
    interrupt_before=["human_feedback"]
)

if __name__ == "__main__":
    initial_input = {"input": "Hello world"}

    config: RunnableConfig = {"configurable": {"thread_id": "1"}}

    for event in graph.stream(initial_input, config, stream_mode="values"):
        print(event)

    # graph stops after the node `step_1` is executed, but we can still write some codes to update the state
    user_input = input("Tell me how you want to update the state: ") # take the user input
    graph.update_state(config, {"user_feedback": user_input}, as_node="human_feedback") # update the state with the user input
    #                                                         ^^^^^^^^^^^^^^^^^^^^^^^^ This means that we want the update to be considered as an update made by the node `human_feedback` rather than being modified from outside.

    print(graph.get_state(config)) # print the updated state for testing

```

Run the code above, you will see the output like this:

```sh
 python main.py
{'input': 'Hello world'}
--- Step 1 ---

Tell me how you want to update the state: test me  # user input `test me`

# below is the updated state
StateSnapshot(
    values={
        'input': 'Hello world',
        'user_feedback': 'test me'
        #                ^^^^^^^^^ the state is updated with the user input by method `graph.update_state`
    },
    next=('step_3',), # information about the next node, this is important when resuming the graph
    config={
        'configurable': {
            'thread_id': '1',
            'checkpoint_ns': '',
            'checkpoint_id': '1eff9836-2e14-61f0-8002-d13be7d262e6'
        }
    },
    metadata={ ...},
    created_at='2025-03-05T05:33:36.673621+00:00',
    parent_config={ ...},
    tasks=(...)
)

```

Then, we can keep running the graph by calling `graph.stream` again. The graph will continue from the node `human_feedback` and execute the next node `step_3`.

```python
from typing import TypedDict
from langchain_core.runnables import RunnableConfig
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver


class State(TypedDict):
    input: str
    user_feedback: str

def stept_1(state: State) -> None:
    print("--- Step 1 ---")

def human_feedback(state: State) -> None:
    print("--- Human Feedback ---")

def stept_3(state: State) -> None:
    print("--- Step 3 ---")

builder = StateGraph(State)

builder.add_node('step_1', stept_1)
builder.add_node('human_feedback', human_feedback)
builder.add_node('step_3', stept_3)

builder.set_entry_point('step_1')
builder.add_edge('step_1', 'human_feedback')
builder.add_edge('human_feedback', 'step_3')
builder.add_edge('step_3', END)

memory = MemorySaver()

graph = builder.compile(
    checkpointer=memory,
    interrupt_before=["human_feedback"]
)

if __name__ == "__main__":
    initial_input = {"input": "Hello world"}

    config: RunnableConfig = {"configurable": {"thread_id": "1"}}

    for event in graph.stream(initial_input, config, stream_mode="values"):
        print(event)

    user_input = input("Tell me how you want to update the state: ")

    graph.update_state(config, {"user_feedback": user_input}, as_node="human_feedback")

    # calling `graph.stream` again to continue the graph
    for event in graph.stream(None, config, stream_mode="values"):
        print(event)

```

Let's run the code above, you will see the output like this:

```sh
 python main.py
{'input': 'Hello world'}
--- Step 1 ---
# the graph stops after the node `step_1` is executed here

# when the graph stops, we take the user input and update the state
Tell me how you want to update the state: test
#                                         ^^^^ user input `test`

# after the state is updated, we call `graph.stream` again to continue the graph with the updated state
{'input': 'Hello world', 'user_feedback': 'test'}
--- Step 3 ---

```


