---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Defining an Agent and AgentExecutor

In the course video, `OpenAIFunctionsAgent` and `AgentExecutor` are used to allow LLM to call tools to accomplish tasks, but the new version of LangChain no longer recommends doing this.

In new versions of LangChain, you have two options for letting LLM call tools to accomplish tasks:

**1. Use `LangGraph`**

```py
from langgraph.prebuilt import create_react_agent

agent_executor = create_react_agent(llm, tools, prompt)

# ...

```

Suitable for requiring more flexible operation senarios.

**2. Use method `.bind_tools` of model object**

```py
from langchain_openai import ChatOpenAI

from tools.sql import run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query])
#                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ helps us building functions list when .invoke() is called
response = llm_with_tools.invoke("How many users are in the database?") # thie request includes tools' informations automatically

print(response)


# $ python main.py
# content=''
# additional_kwargs={ ... }
# response_metadata={ ... }
# id='run-821a4405-8602-4bd0-886a-f41621888bc3-0'
# tool_calls=[
#     {
#         'name': 'run_sqlite_query',
#         'args': {
#             'query': 'SELECT COUNT(*) AS user_count FROM users;'
#         },
#         'id': 'call_2jrYyAMuPZTIrqJJVnX4jdFP',
#         'type': 'tool_call'
#     }
# ]
# usage_metadata={ ... }
```

From the response above, it can be seen that `.bind_tools` does not do everything for us like `OpenAIFunctionsAgent` and `AgentExecutor`. We still need to write the logic to use this tool to get the data and send it to LLM.

We'll complete this in the next topic.
