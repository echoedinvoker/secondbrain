---
date: 2025-02-03
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Implementing a Basic Callback Handler

In [[2025-02-03_Understanding-Callbacks|Understanding Callbacks]], we've learned that there is a handler class that is responsible for managing the callbacks of some specific events.

In new LangChain, this class is still usefull, but official documentation recommends to pass it into Runnable config instead of class constructor.


File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.callbacks import BaseCallbackHandler # import the base class for callback handlers
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query

# Define a callback handler class (you can put it in different file)
class MessagePrintingHandler(BaseCallbackHandler):
    def on_chat_model_start(
        self, 
        serialized,
        messages: list[list[BaseMessage]], # actual messages sent to the model, usually we only care about this
        **kwargs
    ) -> None:
        print("\n--- Sending Messages to AI Model ---")
        for msg_list in messages:
            for msg in msg_list:
                print(f"{msg.type.capitalize()} Message: {msg.content}")
        print("------------------------------------\n")

llm = ChatOpenAI(
    model="gpt-4o-mini",
    # callbacks=[MessagePrintingHandler()] # this is not recommended
)
llm_with_tools = llm.bind_tools([run_sqlite_query, describe_tables, write_report])

tables = list_tables.invoke("")

messages: List[BaseMessage] = [
    SystemMessage(content=(
        "You are an AI that has access to a SQLite database.\n"
        f"The database has tables of: {tables}.\n"
        "Do not make any assumptions about what tables exist "
        "or what columns exist. Instead, use the 'describe_tables' function"
    )),
]

while True:
    user_input = input("\n\nPlease enter your question (or 'quit' to exit): ")
    if user_input.lower() == "quit":
        break
    messages.append(HumanMessage(content=user_input))
    max_iterations = 10

    for i in range(max_iterations):
        print(f"\nIteration {i + 1}:")
        response = llm_with_tools.invoke(
            messages,
            config={"callbacks": [MessagePrintingHandler()]}, # this is recommended
        )
        messages.append(response)

        if isinstance(response, AIMessage) and response.tool_calls:
            for tool_call in response.tool_calls:
                if tool_call['name'] == "run_sqlite_query":
                    print(f"Executing query: {tool_call['args']['query']}")
                    tool_output = run_sqlite_query.invoke(tool_call['args'])
                elif tool_call['name'] == "describe_tables":
                    print(f"Describing tables: {tool_call['args']['table_names']}")
                    tool_output = describe_tables.invoke(tool_call['args'])
                else:
                    print("Write report...")
                    tool_output = write_report.invoke(tool_call['args'])

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

print("Thank you for using the AI assistant. Goodbye!")
```

Let's run the code and see how it works.

```sh
Please enter your question (or 'quit' to exit): how many users

Iteration 1:

--- Sending Messages to AI Model ---  # this is the callback handler output
System Message: You are an AI that has access to a SQLite database.
The database has tables of: users
addresses
products
carts
orders
order_products.
Do not make any assumptions about what tables exist or what columns exist. Instead,
 use the 'describe_tables' function
Human Message: how many users
------------------------------------

Executing query: SELECT COUNT(*) FROM users;
Query result: [(2000,)]

Iteration 2:

--- Sending Messages to AI Model --- # this is the callback handler output
System Message: You are an AI that has access to a SQLite database.
The database has tables of: users
addresses
products
carts
orders
order_products.
Do not make any assumptions about what tables exist or what columns exist. Instead,
 use the 'describe_tables' function
Human Message: how many users
Ai Message: 
Tool Message: [(2000,)]
------------------------------------

Final answer: There are 2,000 users in the database.
```


