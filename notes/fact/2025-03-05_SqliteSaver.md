---
date: 2025-03-05
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# SqliteSaver

This topic, we want to change memory saver to sqlite saver. We need to install `langgraph-checkpoint-sqlite` package first.

```sh
 pipenv install langgraph-checkpoint-sqlite
```

Then, we can modify the codes in the file `main.py`:

```python
from dotenv import load_dotenv
load_dotenv()
from typing import TypedDict
from langchain_core.runnables import RunnableConfig
from langgraph.graph import StateGraph, END

import sqlite3 # we want this to build connection to sqlite
from langgraph.checkpoint.sqlite import SqliteSaver # to integrate sqlite with langgraph as a memory saver


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

# build connection to sqlite with a local file named "checkpoints.sqlite"
conn = sqlite3.connect("checkpoints.sqlite", check_same_thread=False)
#                                            ^^^^^^^^^^^^^^^^^^^^^^^ this is important because we may run the graph in multiple threads
#                                                                    if this is True, the error will be raised
memory = SqliteSaver(conn) # instead of MemorySaver, we use SqliteSaver to integrate sqlite with langgraph as a memory saver

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

    # for event in graph.stream(None, config, stream_mode="values"):  # comment the codes out to test the sqlite memory saver
    #     print(event)

```

Run the codes, and you will see the app exits after you input the feedback.

```sh
 python main.py
{'input': 'Hello world'}
--- Step 1 ---
Tell me how you want to update the state: sqlite

```

Then, you can check the sqlite file `checkpoints.sqlite` to see the data saved in the file.

```py
...

if __name__ == "__main__":
    initial_input = {"input": "Hello world"}

    config: RunnableConfig = {"configurable": {"thread_id": "1"}}

    # for event in graph.stream(initial_input, config, stream_mode="values"):
    #     print(event)
    #
    # user_input = input("Tell me how you want to update the state: ")
    #
    # graph.update_state(config, {"user_feedback": user_input}, as_node="human_feedback")

    for event in graph.stream(None, config, stream_mode="values"):
        print(event)

```

Execute the codes above, and you will see the graph start with the node `human_feedback` with user feedback `sqlite` even the app is restarted.

```sh
 python main.py
{'input': 'Hello world', 'user_feedback': 'sqlite'}
--- Step 3 ---

```

That's it! You have successfully integrated sqlite with langgraph as a memory saver.
