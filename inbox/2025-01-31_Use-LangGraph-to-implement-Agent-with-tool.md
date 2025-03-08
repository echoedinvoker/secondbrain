---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Use LangGraph to implement Agent with tool

In [[2025-01-31_Understanding-Agents-and-AgentExecutors|Understanding Agents and AgentExecutors]], we learned how to let the LLM decide when to call tools. In this article, we will use LangGraph to implement an Agent with a tool.

File: /home/matt/Projects/pycode/tools/sql.py
```python
import sqlite3
from langchain_core.tools import tool

@tool
def run_sqlite_query(query: str):
    """Run a sqlite query."""
    conn = sqlite3.connect('db.sqlite') # must move this line into the function, otherwise the multi-threading will cause an error
    cursor = conn.cursor()
    cursor.execute(query)
    return cursor.fetchall()
```

File: /home/matt/Projects/pycode/main1.py
```python
from typing import Annotated, TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langgraph.graph.message import add_messages
from langchain_core.messages import AIMessage, ToolMessage

from tools.sql import run_sqlite_query


class AgentState(TypedDict):
    messages: Annotated[list, add_messages]
#   ^^^^^^^^  ^^^^^^^^^       ^^^^^^^^^^^^ 
#   must have a key named `messages` because ToolNode will look for this key to do the tool calls and append the return values to the list named `messages`
#             this is important to let all return values be appended to the list automatically

tools = [run_sqlite_query]
llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools(tools)

def call_model(state: AgentState):
    messages = state['messages']
    response = llm_with_tools.invoke(messages)
    return {"messages": [response]}

tool_node = ToolNode(tools) # it will look for the key `messages` in the state and do the tool calls and append the return values to the list named `messages`

def should_continue(state: AgentState):
    last_message = state['messages'][-1]
    if isinstance(last_message, AIMessage) and last_message.tool_calls:
        return "tools"
    return END

workflow = StateGraph(AgentState)
workflow.add_node("model", call_model)
workflow.add_node("tools", tool_node)

workflow.set_entry_point("model")
workflow.add_conditional_edges(
    "model", 
    should_continue, 
    {
        "tools": "tools",
        END: END
    }
)
workflow.add_edge("tools", "model")

app = workflow.compile()

initial_input = {"messages": [{"role": "human", "content": "How many users are in the database?"}]}
#                             ^^^^^^^^^^^^^^^^^ here we use dict type instead of HumanMessage, it's fine
#                                               but it'll be convert to HumanMessage when it's passed to .invoke() method
result = app.invoke(initial_input)

print(result['messages'][-1].content)

```



