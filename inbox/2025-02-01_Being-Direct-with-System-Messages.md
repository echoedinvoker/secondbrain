---
date: 2025-02-01
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Being Direct with System Messages

Although we provided the tool `describe_tables` in the [[2025-02-01_Adding-a-Table-Description-Tool|previous topic]] for LLM to view the schema of the table, LLM still assumes the schema of the table on its own and directly uses the tool `run_sqlite_query` to query the table.

```sh
 python main.py

Iteration 1:
# LLM assumes that the table has a column named `shipping_address` and queries it.
Executing query: SELECT COUNT(DISTINCT user_id) AS user_count FROM addresses WHERE shipping_address IS NOT NULL;
Query result: The following error occurred: no such column: shipping_address.

Iteration 2:
Describing tables: ['addresses']
Query result: CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    address TEXT
    )

Iteration 3:
Executing query: SELECT COUNT(DISTINCT user_id) AS user_count FROM addresses;
Query result: [(2000,)]

Iteration 4:
Final answer: There are 2000 users who provide a shipping address.
```

When our database schema is complex, the assumptions of LLM are likely to be wrong, so we can add more text to the system message to prevent LLM from making any assumptions.


## Pure LangChain application

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.sql import describe_tables, list_tables, run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query, describe_tables])

tables = list_tables.invoke("")

messages: List[BaseMessage] = [
    SystemMessage(content=(
        "You are an AI that has access to a SQLite database.\n"
        f"The database has tables of: {tables}.\n"
        # Add more text to the system message to prevent LLM from making any assumptions.
        "Do not make any assumptions about what tables exist "
        "or what columns exist. Instead, use the 'describe_tables' function"
    )),
    HumanMessage(content="How many users provide shipping address?")
]
max_iterations = 10

for i in range(max_iterations):
    print(f"\nIteration {i + 1}:")
    response = llm_with_tools.invoke(messages)
    messages.append(response)

    if isinstance(response, AIMessage) and response.tool_calls:
        for tool_call in response.tool_calls:
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
            print(f"Query result: {tool_output}")
    else:
        print("Final answer:", response.content)
        break
else:
    print("Maximum iterations reached without a final answer.")

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
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

tables = list_tables.invoke("")

def call_model(state: AgentState):
    messages = state['messages']

    last_message = messages[-1]
    if last_message.name in ["run_sqlite_query", "describe_tables"]:
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
        {
            "role": "system",
            "content": (
                "You are an AI that has access to a SQLite database.\n"
                f"The database has tables of: {tables}.\n"
                # Add more text to the system message to prevent LLM from making any assumptions.
                "Do not make any assumptions about what tables exist "
                "or what columns exist. Instead, use the 'describe_tables' function"
            )
        },
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


## Running the application

```sh
 python main1.py
Iteration 1:
# Now, LLM does not make any assumptions about the tables or columns.
# So, it directly uses the 'describe_tables' function at the beginning.
Describing tables: ['users', 'addresses']
Query result: CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    name TEXT,
    email TEXT UNIQUE,
    password TEXT
    )
CREATE TABLE addresses (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    address TEXT
    )


Iteration 2:
Executing query: SELECT COUNT(DISTINCT user_id) as user_count FROM addresses;
Query result: [[2000]]


There are 2,000 users who provide a shipping address.




