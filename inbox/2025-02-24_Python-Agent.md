---
date: 2025-02-24
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Python Agent

The core of the Python Agent is the ReAct Agent, which is just a tool that includes a Python REPL.

The course video uses `create_react_agent` and `AgentExecutor` to achieve the goal, but I find it too abstract. I will implement it using the same way as topic: [[2025-02-15_ReAct-prompt,-LLM-Reasoning-Engine,-Output-Parsing-and-Tool-Execution|ReAct prompt, LLM Reasoning Engine, Output Parsing and Tool Execution]].

I just copy previous project `react-langchain` and modify it to fit the new requirements.

```python
from dotenv import load_dotenv
from langchain.prompts import PromptTemplate
from langchain_core.tools import render_text_description
from langchain.agents.format_scratchpad import format_log_to_str
from langchain_openai import ChatOpenAI
from langchain.agents.output_parsers import ReActSingleInputOutputParser
from langchain.schema import AgentAction, AgentFinish
from typing import List, Optional, Union
from langchain.tools import BaseTool
from langchain.agents import tool
from callbacks import AgentCallBackHandler
import io
import sys

load_dotenv()

# Define the tool to execute Python code, course video use PythonREPLTool but it's not fit LECL style app
@tool
def execute_python(code: str) -> str:
    """Executes Python code and returns the output, including the value of the last expression. Do not use code block markers."""
    #                                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    #                                                these pluses are to let LLM know more about the tool, or it will assume its default behavior
    old_stdout = sys.stdout
    sys.stdout = mystdout = io.StringIO()

    try:
        tree = ast.parse(code)

        last_expr = None
        if tree.body and isinstance(tree.body[-1], ast.Expr):
            last_expr = ast.Expression(tree.body.pop().value)
            last_expr = compile(last_expr, '<string>', 'eval')

        exec_code = compile(tree, '<string>', 'exec')

        local_vars = {}

        exec(exec_code, None, local_vars)

        if last_expr:
            result = eval(last_expr, None, local_vars)
            print(repr(result))

        output = mystdout.getvalue()
    except Exception as e:
        output = f"Error: {str(e)}"
    finally:
        sys.stdout = old_stdout

    return output.strip()

def find_tool_by_name(tools: List[BaseTool], tool_name: str) -> BaseTool:
    for tool in tools:
        if tool.name == tool_name:
            return tool
    raise ValueError(f"Tool with name {tool_name} not found in tools list")


def main():

    # the first paragraph of the template is different from before,
    # it is a reminder to the agent that it can only use Python REPL to answer the question
    # and always use it to get the answer even if it knows the answer without running any code
    template = """
    You are an agent designed to write and execute python code to answer questions.
    You have access to a python REPL, which you can use to execute python code.
    If you get an error, debug your code and try again.
    Only use the output of your code to answer the question.
    You might know the answer without running any code, but you should still run the code to get the answer.
    If it does not seen like you can write code to answer the question, just return "I don't know" as the answer. (need to fit the following format: "Final Answer: I don't know")

    You have access to the following tools (and only these tools):

    {tools}

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [{tool_names}]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: {input}
    Thought: {agent_scratch}
    """

    tools = [execute_python]
    #        ^^^^^^^^^^^^^^

    prompt = PromptTemplate.from_template(template=template).partial(
        tools=render_text_description(tools),
        tool_names=", ".join([t.name for t in tools]),
    )

    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini", callbacks=[AgentCallBackHandler()])
    llm = llm.bind(stop=["\nObservation"])

    agent = (
        {
            "input": lambda x: x['input'],
            "agent_scratch": lambda x: format_log_to_str(x['agent_scratch']),
        }
        | prompt
        | llm
        | ReActSingleInputOutputParser()
    )

    intermediate_steps = []

    agent_step: Optional[Union[AgentAction, AgentFinish]] = None

    while agent_step is None or isinstance(agent_step, AgentAction):
        agent_step = agent.invoke({
            "input": "generate and save in current working directory 15 QRcodes that point to www.udemy.com/course/langchain, you have qrcode package installed already.",
            #        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
            "agent_scratch": intermediate_steps
        })

        if isinstance(agent_step, AgentAction):
            tool_name = agent_step.tool
            tool_to_use = find_tool_by_name(tools, tool_name)
            tool_input = agent_step.tool_input

            observation = tool_to_use.invoke({"code": tool_input})
            #                                 ^^^^^^ need to be equal to the input of the tool

            intermediate_steps.append((agent_step, str(observation)))


    if isinstance(agent_step, AgentFinish):
        print(agent_step.return_values)


if __name__ == "__main__":
    print("Start...")
    main()
```

Let's run the code and see the result.

```bash
 python main.py
Start...
***Prompt to LLM was:***
Human:
    You are an agent designed to write and execute python code to answer questions.
    You have access to a python REPL, which you can use to execute python code.
    If you get an error, debug your code and try again.
    Only use the output of your code to answer the question.
    You might know the answer without running any code, but you should still run the code to get the answer.
    If it does not seen like you can write code to answer the question, just return "I don't know" as the answer. (need to fit the following format: "Final Answer: I don't know")

    You have access to the following tools (and only these tools):

    execute_python(code: str) -> str - Executes Python code and returns the output, including the value of the last expression. Do not use code block markers.

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [execute_python]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: generate and save in current working directory 15 QRcodes that point to www.udemy.com/course/langchain, you have qrcode package installed already.
    Thought:

*************************************
***LLM Response:***
I need to generate 15 QR codes that point to the specified URL and save them in the current working directory. I'll use the `qrcode` package to accomplish this.

Action: execute_python
Action Input: """
import qrcode
import os

# URL to encode
url = "www.udemy.com/course/langchain"

# Create a directory to save QR codes if it doesn't exist
os.makedirs("qrcodes", exist_ok=True)

# Generate and save 15 QR codes
for i in range(15):
    qr = qrcode.QRCode(version=1, box_size=10, border=5)
    qr.add_data(url)
    qr.make(fit=True)

    img = qr.make_image(fill='black', back_color='white')
    img.save(f"qrcodes/qr_code_{i+1}.png")
"""
*************************************
***Prompt to LLM was:***
Human:
    You are an agent designed to write and execute python code to answer questions.
    You have access to a python REPL, which you can use to execute python code.
    If you get an error, debug your code and try again.
    Only use the output of your code to answer the question.
    You might know the answer without running any code, but you should still run the code to get the answer.
    If it does not seen like you can write code to answer the question, just return "I don't know" as the answer. (need to fit the following format: "Final Answer: I don't know")

    You have access to the following tools (and only these tools):

    execute_python(code: str) -> str - Executes Python code and returns the output, including the value of the last expression. Do not use code block markers.

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [execute_python]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: generate and save in current working directory 15 QRcodes that point to www.udemy.com/course/langchain, you have qrcode package installed already.
    Thought: I need to generate 15 QR codes that point to the specified URL and save them in the current working directory. I'll use the `qrcode` package to accomplish this.

Action: execute_python
Action Input: """
import qrcode
import os

# URL to encode
url = "www.udemy.com/course/langchain"

# Create a directory to save QR codes if it doesn't exist
os.makedirs("qrcodes", exist_ok=True)

# Generate and save 15 QR codes
for i in range(15):
    qr = qrcode.QRCode(version=1, box_size=10, border=5)
    qr.add_data(url)
    qr.make(fit=True)

    img = qr.make_image(fill='black', back_color='white')
    img.save(f"qrcodes/qr_code_{i+1}.png")
"""
Observation:
Thought:

*************************************
***LLM Response:***
Thought: I have executed the code to generate and save 15 QR codes pointing to the specified URL. Now I need to confirm that the QR codes have been created successfully in the "qrcodes" directo
ry. I will check the contents of the directory to see if the files are present.

Action: execute_python
Action Input: """
import os

# List the contents of the "qrcodes" directory
os.listdir("qrcodes")
"""
*************************************
***Prompt to LLM was:***
Human:
    You are an agent designed to write and execute python code to answer questions.
    You have access to a python REPL, which you can use to execute python code.
    If you get an error, debug your code and try again.
    Only use the output of your code to answer the question.
    You might know the answer without running any code, but you should still run the code to get the answer.
    If it does not seen like you can write code to answer the question, just return "I don't know" as the answer. (need to fit the following format: "Final Answer: I don't know")

    You have access to the following tools (and only these tools):

    execute_python(code: str) -> str - Executes Python code and returns the output, including the value of the last expression. Do not use code block markers.

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [execute_python]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: generate and save in current working directory 15 QRcodes that point to www.udemy.com/course/langchain, you have qrcode package installed already.
    Thought: I need to generate 15 QR codes that point to the specified URL and save them in the current working directory. I'll use the `qrcode` package to accomplish this.

Action: execute_python
Action Input: """
import qrcode
import os

# URL to encode
url = "www.udemy.com/course/langchain"

# Create a directory to save QR codes if it doesn't exist
os.makedirs("qrcodes", exist_ok=True)

# Generate and save 15 QR codes
for i in range(15):
    qr = qrcode.QRCode(version=1, box_size=10, border=5)
    qr.add_data(url)
    qr.make(fit=True)

    img = qr.make_image(fill='black', back_color='white')
    img.save(f"qrcodes/qr_code_{i+1}.png")
"""
Observation:
Thought: Thought: I have executed the code to generate and save 15 QR codes pointing to the specified URL. Now I need to confirm that the QR codes have been created successfully in the "qrcodes
" directory. I will check the contents of the directory to see if the files are present.

Action: execute_python
Action Input: """
import os

# List the contents of the "qrcodes" directory
os.listdir("qrcodes")
"""
Observation: ['qr_code_1.png', 'qr_code_2.png', 'qr_code_3.png', 'qr_code_4.png', 'qr_code_5.png', 'qr_code_6.png', 'qr_code_7.png', 'qr_code_8.png', 'qr_code_9.png', 'qr_code_10.png', 'qr_code
_11.png', 'qr_code_12.png', 'qr_code_13.png', 'qr_code_14.png', 'qr_code_15.png']
Thought:

*************************************
***LLM Response:***
I now know the final answer
Final Answer: The 15 QR codes have been successfully generated and saved in the "qrcodes" directory.
*************************************
{'output': 'The 15 QR codes have been successfully generated and saved in the "qrcodes" directory.'}
```

## Be careful to use this agent

Because we have given LLM the ability to execute any Python code, it actually allows users to remotely operate our server to issue commands, which is a high-risk behavior, so caution should be exercised when using it.
