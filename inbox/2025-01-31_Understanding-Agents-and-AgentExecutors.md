---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Understanding Agents and AgentExecutors

## Course video content

Course video introduction to the concepts of Agent and AgentExecutor:

**Agent**:
Almost the same as Chain but with the added functionality of using tools.

**AgentExecutor**:
Basically, it is a While loop that continuously executes the Agent until the Agent's task is completed (No call tools any more).


## New version LangChain

However, in the new LangChain, we will no longer use AgentExecutor and Agent classes. We will continue to complete the app using the codes from the [[2025-01-31_Defining-an-Agent-and-AgentExecutor|Defining an Agent and AgentExecutor]].

```py
from langchain_openai import ChatOpenAI

from tools.sql import run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query])

response = llm_with_tools.invoke("How many users are in the database?")

# content=''  # when llm decides to call tools, `content` is empty string
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

## Write the logic similar to the AgentExecutor on our own ._.

if response.tool_calls:
    tool_outputs = []
    for tool_call in response.tool_calls:
        tool_output = run_sqlite_query.invoke(tool_call)
        tool_outputs.append(tool_output)

    # tool_outputs = [
    #     ToolMessage(
    #         content='[[2000]]',
    #         name='run_sqlite_query',
    #         tool_call_id='call_OCeg1ZBeRlwdYfzGifzjV3QS'
    #     )
    # ]


    final_response = llm_with_tools.invoke([
        response, # don't forget to include the original response, LLM don't hold the history of the previous calls
        *[
            {
                "tool_call_id": call['id'],
                "role": "tool",
                "content": str(output)
            } for call, output in zip(response.tool_calls, tool_outputs)
        ]
    ])

    print(final_response.content)
else:
    print(response.content)
```

The writing of the app above did not consider sending the results of calling tools back to llm, and llm may call tools again. To handle this situation, it may be necessary to consider using a while loop.

Modify the code above to handle the situation where llm calls tools again with the results of the previous tool calls.

```py
from langchain_openai import ChatOpenAI

from tools.sql import run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query])


# create a variable to store the all messages
messages = [llm_with_tools.invoke("How many users are in the database?")]

# create a while loop to handle the situation where llm calls tools again with the results of the previous tool calls
# the loop will break when llm does not call tools anymore and print the final response content
while True:

    if isinstance(messages[-1], AIMessage) and messages[-1].tool_calls:
    #  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ type check to avoid Diagnostic error
        tool_outputs = []
        for tool_call in messages[-1].tool_calls:
            tool_output = run_sqlite_query.invoke(tool_call)
            tool_outputs.append(tool_output)

        final_response = llm_with_tools.invoke([
            *messages,
            *[
                {
                    "tool_call_id": call['id'],
                    "role": "tool",
                    "content": str(output)
                } for call, output in zip(messages[-1].tool_calls, tool_outputs)
            ]
        ])

        messages.append(final_response)

    else:
        print(messages[-1].content)
        break
```

The app above may cause an infinite loop because it uses a while loop. You can set a maximum number of iterations to avoid this issue.

You can see that we keep updating the content of `messages` throughout the process, which is equivalent to the concept of `State` in LangGraph. Therefore, if we use LangGraph to implement this feature, it will be more convenient and easier to expand into more complex processes.



## Correcting the code

The above code only processes the result of the last tool call, not all tool calls, so some modifications need to be made. In addition, the issue of an infinite loop should also be considered, so a limit on the maximum number of iterations should be added.

```py
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.sql import run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([run_sqlite_query])

messages: List[BaseMessage] = [HumanMessage(content="How many users provide shipping address?")]
max_iterations = 5

for i in range(max_iterations):
    print(f"\nIteration {i + 1}:")
    response = llm_with_tools.invoke(messages)
    messages.append(response)

    if isinstance(response, AIMessage) and response.tool_calls:
        for tool_call in response.tool_calls:
            print(f"Executing query: {tool_call['args']['query']}")
            tool_output = run_sqlite_query.invoke(tool_call['args'])
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
