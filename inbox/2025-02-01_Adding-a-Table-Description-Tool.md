---
date: 2025-02-01
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Adding a Table Description Tool

In [[2025-02-01_Adding-Table-Context|Adding Table Context]], we only added a context about table names into system message. In this article, we will provide a tool to query table descriptions for LLMs.

First, add a new tool `describe_tables` and test it.

File: /home/matt/Projects/pycode/tools/sql.py
```python
import sqlite3
from langchain_core.tools import tool

@tool
def list_tables():
    """List all tables in the database."""
    # ...

@tool
def run_sqlite_query(query: str):
    """Run a sqlite query."""
    # ...

# New tool to describe tables
@tool
def describe_tables(table_names: list):
#                                ^^^^ this tool can describe multiple tables at once
    """Describe the columns of the specified tables."""
    conn = sqlite3.connect('db.sqlite')
    cursor = conn.cursor()
    tables = ', '.join("'" + table + "'" for table in table_names)
    try:
        cursor.execute(f"SELECT sql FROM sqlite_master WHERE type='table' AND name IN ({tables});")
        rows = cursor.fetchall()
        return "\n".join(row[0] for row in rows if row[0] is not None)
    except sqlite3.OperationalError as e:
        return f"The following error occurred: {str(e)}."
    finally:
        conn.close()
```

Then, we do a little test in main.py.

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.sql import describe_tables, list_tables, run_sqlite_query
#                     ^^^^^^^^^^^^^^^

# test describe_tables with a list of table names
response = describe_tables.invoke({ 'table_names': ['users', 'orders']})
print(response)

# CREATE TABLE users (
#     id INTEGER PRIMARY KEY,
#     name TEXT,
#     email TEXT UNIQUE,
#     password TEXT
#     )
# CREATE TABLE orders (
#     id INTEGER PRIMARY KEY,
#     user_id INTEGER,
#     created TEXT
#     )

# ...

```

Then, we can actually add the tool to our applications.


## Pure LangChain application

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.sql import describe_tables, list_tables, run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query, describe_tables])
#                                                  ^^^^^^^^^^^^^^^ add the new tool

tables = list_tables.invoke("")

messages: List[BaseMessage] = [
    SystemMessage(content=f"You are an AI that has access to a SQLite database.\n{tables}"),
    HumanMessage(content="How many users provide shipping address?")
]
max_iterations = 10

for i in range(max_iterations):
    print(f"\nIteration {i + 1}:")
    response = llm_with_tools.invoke(messages)
    messages.append(response)

    if isinstance(response, AIMessage) and response.tool_calls:
        for tool_call in response.tool_calls:
            # print(f"Executing query: {tool_call['args']['query']}")
            # tool_output = run_sqlite_query.invoke(tool_call['args'])

            # Above commented code assumes that all tool calls are run_sqlite_query
            # But now we have two tools, so we need to check the tool name
            # and handle the tool call accordingly
            if tool_call['name'] == "run_sqlite_query":
                print(f"Executing query: {tool_call['args']['query']}")
                tool_output = run_sqlite_query.invoke(tool_call['args'])
            else:
                print(f"Describing tables: {tool_call['args']['table_names']}")
                tool_output = describe_tables.invoke(tool_call['args'])

            tool_message = ToolMessage(
                content=str(tool_output),
                tool_call_id=tool_call['id'],
                name=tool_call['name']
            )
            messages.append(tool_message)
            print(f"Query result: {tool_output}") # no change here becuase both tools return a string and they are all query results
    else:
        print("Final answer:", response.content)
        break
else:
    print("Maximum iterations reached without a final answer.")

```

Let's run the application and see if it works.

```bash
 python main.py

Iteration 1:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses WHERE shipping_address = 1;
Query result: The following error occurred: no such column: shipping_address.

Iteration 2:
Describing tables: ['addresses']  # <--- new tool is called here, and LLM decides which tool to call on its own
Query result: CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    address TEXT
    )

Iteration 3:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses;
Query result: [(2000,)]

Iteration 4:
Final answer: There are 2,000 users who provide a shipping address.

```

## LangGraph application

File: /home/matt/Projects/pycode/main1.py
```python
from typing import Annotated, TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langgraph.graph.message import add_messages
from langchain_core.messages import AIMessage
from langgraph.errors import GraphRecursionError

from tools.sql import describe_tables, list_tables, run_sqlite_query

MAX_RECURSION_LIMIT = 3

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]
    recursion_count: int

tools = [run_sqlite_query, describe_tables]
#                          ^^^^^^^^^^^^^^^ add the new tool
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

tables = list_tables.invoke("")

def call_model(state: AgentState):
    messages = state['messages']

    last_message = messages[-1]
    if last_message.name in ["run_sqlite_query", "describe_tables"]:
    #                                            ^^^^^^^^^^^^^^^^^
        print('Query result:', last_message.content)
        print('\n')

    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

tool_node = ToolNode(tools)

def should_continue(state: AgentState):
    last_message = state['messages'][-1]
    if isinstance(last_message, AIMessage) and last_message.tool_calls:

        for tool_call in last_message.tool_calls:
            if tool_call['name'] == "run_sqlite_query":
                print(f"Iteration {state['recursion_count'] + 1}:")
                print(f"Executing query: {tool_call['args']['query']}")

            # handle the new tool call
            else:
                print(f"Iteration {state['recursion_count'] + 1}:")
                print(f"Describing tables: {tool_call['args']['table_names']}")


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

Let's run the application and see if it works.

```bash
 python main1.py
Iteration 1:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses WHERE shipping_address IS NOT NULL;
Query result: The following error occurred: no such column: shipping_address.


Iteration 2:
Describing tables: ['addresses'] # <--- new tool is called here, and LLM decides which tool to call on its own
Query result: CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    address TEXT
    )


Iteration 3:
Executing query: SELECT COUNT(DISTINCT user_id) FROM addresses;
Query result: [[2000]]


There are 2000 users who provide a shipping address.

```



