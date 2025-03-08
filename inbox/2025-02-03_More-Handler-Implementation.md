---
date: 2025-02-03
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# More Handler Implementation

In [[2025-02-03_Implementing-a-Basic-Callback-Handler|Implementing a Basic Callback Handler]], we know callback handlers can access the `messages` which are passed to the model. So, we can use it to present the process instead of printing it manually.

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from handlers.chat_model_start import MessagePrintingHandler
from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query


llm = ChatOpenAI(
    model="gpt-4o-mini",
)
llm_with_tools = llm.bind_tools(
    [run_sqlite_query, describe_tables, write_report],
)

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
            config={"callbacks": [MessagePrintingHandler()]},
        )
        messages.append(response)

        if isinstance(response, AIMessage) and response.tool_calls:
            for tool_call in response.tool_calls:
                if tool_call['name'] == "run_sqlite_query":
                    # print(f"Executing query: {tool_call['args']['query']}")   # we'll handle this in callback handler
                    tool_output = run_sqlite_query.invoke(tool_call['args'])
                elif tool_call['name'] == "describe_tables":
                    # print(f"Describing tables: {tool_call['args']['table_names']}")  # we'll handle this in callback handler
                    tool_output = describe_tables.invoke(tool_call['args'])
                else:
                    # print("Write report...")   # we'll handle this in callback handler
                    tool_output = write_report.invoke(tool_call['args'])

                tool_message = ToolMessage(
                    content=str(tool_output),
                    tool_call_id=tool_call['id'],
                    name=tool_call['name']
                )
                messages.append(tool_message)
                # print(f"Query result: {tool_output}")   # we'll handle this in callback handler
        else:
            print("Final answer:", response.content) # final answer won't be passed to the model,
            break                                    # so it won't be accessible in the callback handler
    else:
        print("Maximum iterations reached without a final answer.")  # this message won't be passed to the model
                                                                     # so it won't be accessible in the callback handler

print("Thank you for using the AI assistant. Goodbye!")  # this message won't be passed to the model, so we need to print it manually
```

File: /home/matt/Projects/pycode/handlers/chat_model_start.py
```python
from langchain_core.callbacks import BaseCallbackHandler
from langchain_core.messages import BaseMessage

class MessagePrintingHandler(BaseCallbackHandler):
    def on_chat_model_start(
        self, 
        serialized, 
        messages: list[list[BaseMessage]], 
        **kwargs
    ) -> None:
        print(messages)  # we just print the raw messages at first to see what we have
```

```sh
Please enter your question (or 'quit' to exit): how many users in database

Iteration 1:
[
  [
    SystemMessage(
      content="You are an AI that has access to a SQLite database.\nThe database has tables of: users\naddresses\nproducts\ncarts\norders\norder_products.\nDo not make any assump
tions about what tables exist or what columns exist. Instead, use the 'describe_tables' function",
      additional_kwargs={}, response_metadata={}
    ),
    HumanMessage(content='how many users in database', additional_kwargs={}, response_metadata={})
  ]
]

Iteration 2:
[
  [
    SystemMessage(
      content="You are an AI that has access to a SQLite database.\nThe database has tables of: users\naddresses\nproducts\ncarts\norders\norder_products.\nDo not make any assump
tions about what tables exist or what columns exist. Instead, use the 'describe_tables' function",
      additional_kwargs={}, response_metadata={}
    ),
    HumanMessage( content='how many users in database', additional_kwargs={}, response_metadata={}),
    AIMessage(
      content='',
      additional_kwargs={
        'tool_calls': [
          {
            'id': 'call_EQWSNkrK28ZhzM6OARgL5EaP',
            'function': {
              'arguments': '{ "query":"SELECT COUNT(*) FROM users;" }',
              'name': 'run_sqlite_query'
            },
            'type':'function'
          }
        ],
        'refusal': None
      },
      response_metadata={ ... },
      id='run-f3bfd76c-afc7-44f4-b5f7-d952d43fdfa4-0',
      tool_calls=[
        {
          'name': 'run_sqlite_query',
          'args': { 'query': 'SELECT COUNT(*) FROM users;' },
          'id': 'call_EQWSNkrK28ZhzM6OARgL5EaP',
          'type': 'tool_call'
        }
      ],
      usage_metadata={
        'input_tokens': 164,
        'output_tokens': 22,
        'total_tokens': 186,
        'input_token_details': {'audio': 0, 'cache_read': 0},
        'output_token_details': {'audio': 0, 'reasoning': 0}
      }
    ),
    ToolMessage(content='[(2000,)]', name='run_sqlite_query', tool_call_id='call_EQWSNkrK28ZhzM6OARgL5EaP')]]

Final answer: There are 2000 users in the database.

Please enter your question (or 'quit' to exit): 

```

Then, we can isntall `pyboxen` to make the messages more readable.

```sh
pip install pyboxen
```

File: /home/matt/Projects/pycode/handlers/chat_model_start.py
```python
from langchain_core.callbacks import BaseCallbackHandler
from langchain_core.messages import AIMessage, BaseMessage, HumanMessage, SystemMessage, ToolMessage
from pyboxen import boxen # new import

# use boxen to print messages
def boxen_print(*args, **kwargs):
    print(boxen(*args, **kwargs))

class MessagePrintingHandler(BaseCallbackHandler):
    def on_chat_model_start(
        self, 
        serialized, 
        messages: list[list[BaseMessage]], 
        **kwargs
    ) -> None:
        # loop each message
        for message in messages[0]:
            # different type of messages, different color and title with boxen print
            if isinstance(message, SystemMessage):
                boxen_print(message.content, title="System Message", color="red")
            elif isinstance(message, HumanMessage):
                boxen_print(message.content, title="Human Message", color="green")
            elif isinstance(message, AIMessage):
                if message.tool_calls:
                    for tool_call in message.tool_calls:
                        boxen_print(
                            f"{tool_call['name']}({tool_call['args']})",
                            title="Tool Call",
                            color="blue")
                else:
                    boxen_print(message.content, title="AI Message", color="cyan")
            elif isinstance(message, ToolMessage):
                boxen_print(message.content, title="Tool Message", color="yellow")
            else:
                boxen_print(message.content, title="Unknown Message", color="magenta")
```

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from handlers.chat_model_start import MessagePrintingHandler, boxen_print
from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query


llm = ChatOpenAI(
    model="gpt-4o-mini",
)
llm_with_tools = llm.bind_tools(
    [run_sqlite_query, describe_tables, write_report],
)

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
            config={"callbacks": [MessagePrintingHandler()]},
        )
        messages.append(response)

        if isinstance(response, AIMessage) and response.tool_calls:
            for tool_call in response.tool_calls:
                if tool_call['name'] == "run_sqlite_query":
                    tool_output = run_sqlite_query.invoke(tool_call['args'])
                elif tool_call['name'] == "describe_tables":
                    tool_output = describe_tables.invoke(tool_call['args'])
                else:
                    tool_output = write_report.invoke(tool_call['args'])

                tool_message = ToolMessage(
                    content=str(tool_output),
                    tool_call_id=tool_call['id'],
                    name=tool_call['name']
                )
                messages.append(tool_message)
        else:
            # also use boxen print to represent the final answer
            boxen_print(response.content, title="Final Answer", color="cyan")
            break
    else:
        print("Maximum iterations reached without a final answer.")

print("Thank you for using the AI assistant. Goodbye!")
```

Let's run the code again.

```sh
Please enter your question (or 'quit' to exit): how many users

Iteration 1:
╭─ System Message ─────────────────────────────────────────────────────────────────────────────────────────────────────╮                                                                         
│You are an AI that has access to a SQLite database.                                                                   │                                                                         
│The database has tables of: users                                                                                     │                                                                         
│addresses                                                                                                             │                                                                         
│products                                                                                                              │                                                                         
│carts                                                                                                                 │                                                                         
│orders                                                                                                                │                                                                         
│order_products.                                                                                                       │                                                                         
│Do not make any assumptions about what tables exist or what columns exist. Instead, use the 'describe_tables' function│                                                                         
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯                                                                         

╭─ Human Messag─╮                                                                                                                                                                                
│how many users │                                                                                                                                                                                
╰───────────────╯                                                                                                                                                                                


Iteration 2: # first two messages are duplicated, we'll fix it later
╭─ System Message ─────────────────────────────────────────────────────────────────────────────────────────────────────╮                                                                         
│You are an AI that has access to a SQLite database.                                                                   │                                                                         
│The database has tables of: users                                                                                     │                                                                         
│addresses                                                                                                             │                                                                         
│products                                                                                                              │                                                                         
│carts                                                                                                                 │                                                                         
│orders                                                                                                                │                                                                         
│order_products.                                                                                                       │                                                                         
│Do not make any assumptions about what tables exist or what columns exist. Instead, use the 'describe_tables' function│                                                                         
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯                                                                         

╭─ Human Messag─╮                                                                                                                                                                                
│how many users │                                                                                                                                                                                
╰───────────────╯                                                                                                                                                                                

╭─ Tool Call ────────────────────────────────────────────────────────────╮                                                                                                                       
│run_sqlite_query({'query': 'SELECT COUNT(*) AS user_count FROM users;'})│                                                                                                                       
╰────────────────────────────────────────────────────────────────────────╯                                                                                                                       

╭─ Tool Messag─╮                                                                                                                                                                                 
│  [(2000,)]   │                                                                                                                                                                                 
╰──────────────╯                                                                                                                                                                                 

╭─ Final Answer ──────────────────────╮                                                                                                                                                          
│There are 2000 users in the database.│                                                                                                                                                          
╰─────────────────────────────────────╯                                                                                                                                                          
```

## Remove duplicated messages between different iterations

File: /home/matt/Projects/pycode/handlers/chat_model_start.py
```python
from langchain_core.callbacks import BaseCallbackHandler
from langchain_core.messages import AIMessage, BaseMessage, HumanMessage, SystemMessage, ToolMessage
from pyboxen import boxen

def boxen_print(*args, **kwargs):
    print(boxen(*args, **kwargs))

class MessagePrintingHandler(BaseCallbackHandler):
    # add a new field to indicate where of the index of the messages to start to print
    def __init__(self):
        super().__init__()
        self.index = 0

    def on_chat_model_start(
        self, 
        serialized, 
        messages: list[list[BaseMessage]], 
        **kwargs
    ) -> None:
        for message in messages[0][self.index:]:
        #                         ^^^^^^^^^^^^^
            if isinstance(message, SystemMessage):
                boxen_print(message.content, title="System Message", color="red")
            elif isinstance(message, HumanMessage):
                boxen_print(message.content, title="Human Message", color="green")
            elif isinstance(message, AIMessage):
                if message.tool_calls:
                    for tool_call in message.tool_calls:
                        boxen_print(
                            f"{tool_call['name']}({tool_call['args']})",
                            title="Tool Call",
                            color="blue")
                else:
                    boxen_print(message.content, title="AI Message", color="cyan")
            elif isinstance(message, ToolMessage):
                boxen_print(message.content, title="Tool Message", color="yellow")
            else:
                boxen_print(message.content, title="Unknown Message", color="magenta")
        # update the index to the end of the messages, so we can skip the duplicated messages nexttime
        self.index = len(messages[0])
```

File: /home/matt/Projects/pycode/main.py
```python
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from handlers.chat_model_start import MessagePrintingHandler, boxen_print
from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query


llm = ChatOpenAI(
    model="gpt-4o-mini",
)
llm_with_tools = llm.bind_tools(
    [run_sqlite_query, describe_tables, write_report],
)

tables = list_tables.invoke("")

messages: List[BaseMessage] = [
    SystemMessage(content=(
        "You are an AI that has access to a SQLite database.\n"
        f"The database has tables of: {tables}.\n"
        "Do not make any assumptions about what tables exist "
        "or what columns exist. Instead, use the 'describe_tables' function"
    )),
]

handler = MessagePrintingHandler() # we must use the same instance of the handler

while True:
    user_input = input("\n\nPlease enter your question (or 'quit' to exit): ")
    if user_input.lower() == "quit":
        break
    messages.append(HumanMessage(content=user_input))
    max_iterations = 10

    for i in range(max_iterations):
        # print(f"\nIteration {i + 1}:") # I think this is a bit redundant
        response = llm_with_tools.invoke(
            messages,
            config={"callbacks": [handler]},
            #                     ^^^^^^^
        )
        messages.append(response)

        if isinstance(response, AIMessage) and response.tool_calls:
            for tool_call in response.tool_calls:
                if tool_call['name'] == "run_sqlite_query":
                    tool_output = run_sqlite_query.invoke(tool_call['args'])
                elif tool_call['name'] == "describe_tables":
                    tool_output = describe_tables.invoke(tool_call['args'])
                else:
                    tool_output = write_report.invoke(tool_call['args'])

                tool_message = ToolMessage(
                    content=str(tool_output),
                    tool_call_id=tool_call['id'],
                    name=tool_call['name']
                )
                messages.append(tool_message)
        else:
            boxen_print(response.content, title="Final Answer", color="cyan")
            handler.index += 1 # because the final answer won't be passed to the model until next question, so we need to update the index manually, or it'll be printed at the start of the next question, it's not what we want
            break
    else:
        print("Maximum iterations reached without a final answer.")

print("Thank you for using the AI assistant. Goodbye!")
```

Ok, let's run the code again.

```sh
 python main.py


Please enter your question (or 'quit' to exit): how many types of product?
╭─ System Message ─────────────────────────────────────────────────────────────────────────────────────────────────────╮                                                                         
│You are an AI that has access to a SQLite database.                                                                   │                                                                         
│The database has tables of: users                                                                                     │                                                                         
│addresses                                                                                                             │                                                                         
│products                                                                                                              │                                                                         
│carts                                                                                                                 │                                                                         
│orders                                                                                                                │                                                                         
│order_products.                                                                                                       │                                                                         
│Do not make any assumptions about what tables exist or what columns exist. Instead, use the 'describe_tables' function│                                                                         
╰──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯                                                                         

╭─ Human Message ──────────╮                                                                                                                                                                     
│how many types of product?│                                                                                                                                                                     
╰──────────────────────────╯                                                                                                                                                                     

╭─ Tool Call ──────────────────────────────────╮                                                                                                                                                 
│describe_tables({'table_names': ['products']})│                                                                                                                                                 
╰──────────────────────────────────────────────╯                                                                                                                                                 

╭─ Tool Message ────────────╮                                                                                                                                                                    
│CREATE TABLE products (    │                                                                                                                                                                    
│    id INTEGER PRIMARY KEY,│                                                                                                                                                                    
│    name TEXT,             │                                                                                                                                                                    
│    price REAL             │                                                                                                                                                                    
│    )                      │                                                                                                                                                                    
╰───────────────────────────╯                                                                                                                                                                    

╭─ Tool Call ──────────────────────────────────────────────────────────────────────────────╮                                                                                                     
│run_sqlite_query({'query': 'SELECT COUNT(DISTINCT name) AS product_types FROM products;'})│                                                                                                     
╰──────────────────────────────────────────────────────────────────────────────────────────╯                                                                                                     

╭─ Tool Messag─╮                                                                                                                                                                                 
│  [(1646,)]   │                                                                                                                                                                                 
╰──────────────╯                                                                                                                                                                                 

╭─ Final Answer ───────────────────────────────────╮                                                                                                                                             
│There are 1,646 types of products in the database.│                                                                                                                                             
╰──────────────────────────────────────────────────╯                                                                                                                                             



Please enter your question (or 'quit' to exit): which is the most popular?
╭─ Human Message ──────────╮                                                                                                                                                                     
│which is the most popular?│                                                                                                                                                                     
╰──────────────────────────╯                                                                                                                                                                     

╭─ Tool Call ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│    run_sqlite_query({'query': 'SELECT p.name, COUNT(op.product_id) AS order_count\nFROM order_products op\nJOIN products p ON op.product_id = p.id\nGROUP BY p.name\nORDER BY order_count     │
│    DESC\nLIMIT 1;'})                                                                                                                                                                          │
╰───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯

╭─ Tool Message─╮                                                                                                                                                                                
│[('Soap', 111)]│                                                                                                                                                                                
╰───────────────╯                                                                                                                                                                                

╭─ Final Answer ──────────────────────────────────────────────────────╮                                                                                                                          
│The most popular product is "Soap," which has been ordered 111 times.│                                                                                                                          
╰─────────────────────────────────────────────────────────────────────╯                                                                                                                          
```

Perfect!!




