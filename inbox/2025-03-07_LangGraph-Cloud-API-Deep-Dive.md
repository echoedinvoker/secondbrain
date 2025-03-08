---
date: 2025-03-07
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# LangGraph Cloud API Deep Dive

The LangGraph Cloud API mainly consists of three core concepts: assistant, thread, and run, and is provided to users in the form of an API. This is a great reference for developers who provide front-end APIs.


## Asistant

Assistant is an abstraction layer based on a graph. We can put things like config, metadata, graph, etc. in the concept of assistant, so we can use the same assistant to change the graph or config at any time.

![create-assistant-on-langgraph.png](../assets/imgs/create-assistant-on-langgraph.png)
![create-assistant-and-copy-id.png](../assets/imgs/create-assistant-and-copy-id.png)

![get-assistant-doc.png](../assets/imgs/get-assistant-doc.png)

![get-assistant-info.png](../assets/imgs/get-assistant-info.png)


## Thread

You can think of a thread as an environment where you can execute your code and store your state, so you can continue using this state the next time you run it.

![create-thread-doc.png](../assets/imgs/create-thread-doc.png)

![create-a-thread-on-langgraph.png](../assets/imgs/create-a-thread-on-langgraph.png)


## Run

"run" means to actually execute an assistant and run it in a thread.

![run-doc-langgraph.png](../assets/imgs/run-doc-langgraph.png)

![create-a-run.png](../assets/imgs/create-a-run.png)

![send-run-and-respond.png](../assets/imgs/send-run-and-respond.png)


## Where to find the result state??

It is important to note that the result (state) of a run is saved in the thread, not in the run itself.

![check-run-on-langgraph.png](../assets/imgs/check-run-on-langgraph.png)

![find-run-by-thread-id.png](../assets/imgs/find-run-by-thread-id.png)

![get-thread-state-doc.png](../assets/imgs/get-thread-state-doc.png)

![find-out-state-in-thread.png](../assets/imgs/find-out-state-in-thread.png)



