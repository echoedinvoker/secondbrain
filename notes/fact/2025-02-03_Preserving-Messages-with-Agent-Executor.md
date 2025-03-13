---
date: 2025-02-03
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Preserving Messages with Agent Executor

The course video introduces how to add memory to the agent so that it can remember the information from the previous question, but the new version of LangChain no longer uses the AgentExecutor class, so there is no need to use memory.

However, we still want the app to be able to remember the content of the last question and answer, so that the conversation can be more coherent.

We'll modify the codes of [[2025-02-01_Tools-with-Multiple-Arguments|Tools with Multiple Arguments]], make the app allow users ask multiple questions and AI model can remember the history of the conversation.



## Pure LangChain application
```py
from typing import List
from langchain_core.messages import AIMessage, HumanMessage, SystemMessage, ToolMessage, BaseMessage
from langchain_openai import ChatOpenAI

from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query

llm = ChatOpenAI(model="gpt-4o-mini")
llm_with_tools = llm.bind_tools([
    run_sqlite_query,
    describe_tables,
    write_report
])

tables = list_tables.invoke("")

messages: List[BaseMessage] = [
    SystemMessage(content=(
        "You are an AI that has access to a SQLite database.\n"
        f"The database has tables of: {tables}.\n"
        "Do not make any assumptions about what tables exist "
        "or what columns exist. Instead, use the 'describe_tables' function"
    )),
    # move HumanMessage into while loop
]

# wrap the for loop in a while loop
while True:
    # get user input question and append to messages list
    user_input = input("\n\nPlease enter your question (or 'quit' to exit): ")
    if user_input.lower() == "quit":
        break
    messages.append(HumanMessage(content=user_input))

    max_iterations = 10 # whenever start a new question, reset the max_iterations

    for i in range(max_iterations):
        print(f"\nIteration {i + 1}:")
        response = llm_with_tools.invoke(messages)
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

The above code will automatically remember all conversation history because all messages are saved in the variable `messages`, continuously appending new conversation content without deleting any conversation content, so the AI model will remember all conversation content, even previous questions.

Let's run the code and see the result.

```sh
$ python main.py


Please enter your question (or 'quit' to exit): How many users in the database? Write an HTML report for me.

Iteration 1:
Executing query: SELECT COUNT(*) AS user_count FROM users;
Query result: [(2000,)]

Iteration 2:
Write report...
Query result: Report written to user_count_report.html.

Iteration 3:
Final answer: I have generated the HTML report containing the total number of users in the database, which is 2000. You can download the report [here]
dbox:/user_count_report.html).


# the second question, user assumes that the agent has the memory of the previous question, and it does
Please enter your question (or 'quit' to exit): Do the exact same thing about orders.

Iteration 1:
Executing query: SELECT COUNT(*) AS order_count FROM orders;
Query result: [(1500,)]

Iteration 2:
Write report...
Query result: Report written to order_count_report.html.

# AI model actually remembers the previous question and answer, so it can repeat the exact same process from users to orders

Iteration 3:
Final answer: I have generated the HTML report for the total number of orders in the database, which is 1500. You can download the report [here](sandb
order_count_report.html).
Please enter your question (or 'quit' to exit): 
```


## LangGraph application
```py
from typing import Annotated, TypedDict
from langchain_openai import ChatOpenAI
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolNode
from langgraph.graph.message import add_messages
from langchain_core.messages import AIMessage, HumanMessage
from langgraph.errors import GraphRecursionError

from tools.report import write_report
from tools.sql import describe_tables, list_tables, run_sqlite_query

MAX_RECURSION_LIMIT = 10

class AgentState(TypedDict):
    messages: Annotated[list, add_messages]
    recursion_count: int
    user_input_quit: bool # add a new key to store user input quit status

tools = [run_sqlite_query, describe_tables, write_report]
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
    print(response.content)
    return {"messages": [response]}

tool_node = ToolNode(tools)

def should_continue(state: AgentState):
    last_message = state['messages'][-1]
    if isinstance(last_message, AIMessage) and last_message.tool_calls:
        print(f"Iteration {state['recursion_count'] + 1}:")
        for tool_call in last_message.tool_calls:
            if tool_call['name'] == "run_sqlite_query":
                print(f"Executing query: {tool_call['args']['query']}")
            elif tool_call['name'] == "describe_tables":
                print(f"Describing tables: {tool_call['args']['table_names']}")
            else:
                print("Write report...")

        return "tools"
    return "user_input"
    #      ^^^^^^^^^^^^ replace END with "user_input"

# add a new function to check if user input quit for decision of next node from user_input
def should_quit(state: AgentState):
    return "quite" if state['user_input_quit'] else "model"

def update_recursion_count(state: AgentState):
    current_count = state.get('recursion_count', 0)

    if current_count >= MAX_RECURSION_LIMIT:
        raise GraphRecursionError(f"Maximum recursion limit of {MAX_RECURSION_LIMIT} reached")

    return {"recursion_count": current_count + 1}

# add a new node to get user input as initial human message (question)
def get_user_input(state: AgentState):
    user_input = input("\n\nPlease enter your question (or 'quit' to exit): ")
    if user_input.lower() == "quit":
        return {"user_input_quit": True} # if user input quit, set user_input_quit of state to True

    return {"messages": [HumanMessage(content=user_input)]}

workflow = StateGraph(AgentState)
workflow.add_node("model", call_model)
workflow.add_node("tools", tool_node)
workflow.add_node("increment_recursion", update_recursion_count)
workflow.add_node("user_input", get_user_input) # add a new node for user input

workflow.set_entry_point("user_input") # start from users asking questions
workflow.add_conditional_edges( # if user input quit, go to "quite" node, else go to "model" node
    "user_input",
    should_quit,
    {
        "quite": END,
        "model": "model"
    }
)
workflow.add_conditional_edges(
    "model", 
    should_continue, 
    {
        "tools": "increment_recursion",
        "user_input": "user_input" # after final answer, go back to user input node
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
                "Do not make any assumptions about what tables exist "
                "or what columns exist. Instead, use the 'describe_tables' function"
            )
            # remove hard-coded human message question
        },
    ],
    "recursion_count": 0,
    "user_input_quit": False # init user_input_quit to False
}

try:
    result = app.invoke(initial_input)
    print(result['messages'][-1].content)

except GraphRecursionError:
    print("Maximum recursion limit reached.")

```

```sh
 python main1.py


Please enter your question (or 'quit' to exit): how many users in db. write an html report 

Iteration 1:
Executing query: SELECT COUNT(*) AS user_count FROM users;
Query result: [[2000]]



Iteration 2:
Write report...
The HTML report has been successfully created, containing the count of users in the database. Th
ere are a total of **2000 users**. You can download the report [here](sandbox:/user_count_report
.html).


Please enter your question (or 'quit' to exit): do the exact thing about orders

Iteration 3:
Executing query: SELECT COUNT(*) AS order_count FROM orders;
Query result: [[1500]]



Iteration 4:
Write report...
The HTML report for the total number of orders has been successfully created. There are a total 
of **1500 orders** in the database. You can download the report [here](sandbox:/order_count_repo
rt.html).


Please enter your question (or 'quit' to exit): quit
The HTML report for the total number of orders has been successfully created. There are a total 
of **1500 orders** in the database. You can download the report [here](sandbox:/order_count_repo
rt.html).
```
