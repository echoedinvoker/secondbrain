---
date: 2025-02-01
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Optimize LangGraph application

In [[2025-02-01_Adding-Table-Context|Adding Table Context]], we saw that the LangGraph application lacked of iteration limit and not to print query scripts and results, which made it difficult to debug and might cause infinite loops. In this article, we will optimize the LangGraph application by adding iteration limit and print query scripts and results.


## Adding iteration limit
```py
from typing import Annotated, TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langgraph.graph.message import add_messages
from langchain_core.messages import AIMessage
from langgraph.errors import GraphRecursionError  # 新增的錯誤處理模組

from tools.sql import list_tables, run_sqlite_query

# Define the maximum recursion limit (set to 3 times here)
MAX_RECURSION_LIMIT = 3

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]
    # Add a field to track the recursion count
    recursion_count: int

tools = [run_sqlite_query]
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

tables = list_tables.invoke("")

def call_model(state: AgentState):
    messages = state['messages']
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

tool_node = ToolNode(tools)

def should_continue(state: AgentState):
    last_message = state['messages'][-1]
    if isinstance(last_message, AIMessage) and last_message.tool_calls:
        return "tools"
    return END

def update_recursion_count(state: AgentState):
    # Increment the recursion count
    current_count = state.get('recursion_count', 0)

    # Check if the recursion limit is reached, if so, raise an error GraphRecursionError
    if current_count >= MAX_RECURSION_LIMIT:
        raise GraphRecursionError(f"Maximum recursion limit of {MAX_RECURSION_LIMIT} reached")

    # if not, increment the recursion count
    return {"recursion_count": current_count + 1}

workflow = StateGraph(AgentState)
workflow.add_node("model", call_model)
workflow.add_node("tools", tool_node)
# Add a node to update the recursion count
workflow.add_node("increment_recursion", update_recursion_count)

workflow.set_entry_point("model")
workflow.add_conditional_edges(
    "model", 
    should_continue, 
    {
        "tools": "increment_recursion",  # Update the recursion count first
        END: END
    }
)
# Ensure that the increment_recursion node is executed before the tools node
workflow.add_edge("increment_recursion", "tools")
workflow.add_edge("tools", "model")

app = workflow.compile()

initial_input = {
    "messages": [
        {"role": "system", "content": f"You are an AI that has access to a SQLite database.\n{tables}"},
        {"role": "human", "content": "How many users provide shipping address?"}
    ],
    # Initialize the recursion count
    "recursion_count": 0
}

try:
    result = app.invoke(initial_input)
    print(result['messages'][-1].content)

# Catch the GraphRecursionError and print a message
except GraphRecursionError:
    print("Maximum recursion limit reached.")
```


In the code above, we added a new field `recursion_count` to the `AgentState` type to track the number of recursive calls. We also defined a constant `MAX_RECURSION_LIMIT` to set the maximum number of recursive calls allowed. In the `should_continue` function, we check if the `recursion_count` exceeds the limit and return `END` if it does. We also added a new node `increment_recursion` to increment the `recursion_count` before calling the tools node. Finally, we added a try-except block to catch the `GraphRecursionError` and print a message when the maximum recursion limit is reached.

Next, let's add the functionality to print the query scripts and results.


## Print query scripts and results

```py
from typing import Annotated, TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langgraph.graph.message import add_messages
from langchain_core.messages import AIMessage
from langgraph.errors import GraphRecursionError

from tools.sql import list_tables, run_sqlite_query

MAX_RECURSION_LIMIT = 3

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]
    recursion_count: int

tools = [run_sqlite_query]
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

tables = list_tables.invoke("")

def call_model(state: AgentState):
    messages = state['messages']

    # Check if the last message was a query, if so print the query result
    last_message = messages[-1]
    if last_message.name == "run_sqlite_query":
        print('Query result:', last_message.content)
        print('\n')

    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

tool_node = ToolNode(tools)

def should_continue(state: AgentState):
    last_message = state['messages'][-1]
    if isinstance(last_message, AIMessage) and last_message.tool_calls:

        # [{'name': 'run_sqlite_query', 'args': {'query': 'SELECT COUNT(DISTINCT user_id) FROM addresses WHERE address IS NOT NULL;'}, 'id': 'call_JteHmFlu550VATV7E2hdO4o3', 'type': 'tool_call'}]
        # Print the query that was executed
        for tool_call in last_message.tool_calls:
            if tool_call['name'] == "run_sqlite_query":
                print(f"Iteration {state['recursion_count'] + 1}:")
                print(f"Executing query: {tool_call['args']['query']}")

        return "tools"
    return END

def update_recursion_count(state: AgentState):
    current_count = state.get('recursion_count', 0)

    if current_count >= MAX_RECURSION_LIMIT:
        raise GraphRecursionError(f"Maximum recursion limit of {MAX_RECURSION_LIMIT} reached")

    return {"recursion_count": current_count + 1}

workflow = StateGraph(AgentState)
workflow.add_node("model", call_model)
workflow.add_node("tools", tool_node)
workflow.add_node("increment_recursion", update_recursion_count)

workflow.set_entry_point("model")
workflow.add_conditional_edges(
    "model", 
    should_continue, 
    {
        "tools": "increment_recursion",
        END: END
    }
)
workflow.add_edge("increment_recursion", "tools")
workflow.add_edge("tools", "model")

app = workflow.compile()

initial_input = {
    "messages": [
        {"role": "system", "content": f"You are an AI that has access to a SQLite database.\n{tables}"},
        {"role": "human", "content": "How many users provide shipping address?"}
    ],
    "recursion_count": 0
}

try:
    result = app.invoke(initial_input)
    print(result['messages'][-1].content)

except GraphRecursionError:
    print("Maximum recursion limit reached.")

```

In the code above, we added a check in the `call_model` function to print the query result if the last message was a query. We also added a loop in the `should_continue` function to print the query that was executed before calling the tools node. Now, when you run the application, it will print the query scripts and results for each iteration.

Let's run the application to see the output:

```sh
 python main1.py
Iteration 1:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses WHERE shipping_address IS NOT NULL;
Query result: The following error occurred: no such column: shipping_address.


Iteration 2:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses;
Query result: [[2000]]


There are 2,000 users who provide shipping addresses.

```
