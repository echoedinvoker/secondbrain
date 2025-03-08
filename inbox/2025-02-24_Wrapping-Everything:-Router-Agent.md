---
date: 2025-02-24
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Wrapping Everything: Router Agent

Router Agent is a type of agent that simply passes user questions to the appropriate agent for processing and obtaining results, also known as a grand agent.

So I need to change the file structure as follows:

```sh
 tree
.
├── callbacks.py
├── csv_agent.py
├── episode_info.csv
├── main.py # create new main.py for the router agent
├── Pipfile
├── Pipfile.lock
├── python_agent.py # rename original `main.py` to `python_agent.py`
└── qrcodes
```

In order for the Python Agent Executor and CSV Agent Executor to be used as tools by the Router Agent, we need to adjust the content of these two files.


File: /home/matt/Projects/code-interpreter/csv_agent.py
```python
from langchain_openai import ChatOpenAI
from langchain_experimental.agents import create_csv_agent


def csv_agent_executor(input: str) -> str: # wrap with function which has simple input and output signature
#                      ^^^^^ need parameter to pass user question from router agent
    csv_agent = create_csv_agent(
        llm=ChatOpenAI(temperature=0, model="gpt-4o"),
        path="episode_info.csv",
        verbose=True,
        allow_dangerous_code=True
    )

    res = csv_agent.invoke(
        input={"input": input},
        #               ^^^^^
    )

    return res['output'] # because res is a dictionary, we need to conver it to string to fill the prompt after `observation:`
```

File: /home/matt/Projects/code-interpreter/python_agent.py
```python
...

@tool
def execute_python(code: str) -> str: ...

def find_tool_by_name(tools: List[BaseTool], tool_name: str) -> BaseTool: ...

def python_agent_executor(input: str) -> str:
#                         ^^^^^^^^^^^^^^^^^^ customize input and output signature, so it can be used by router agent
#                                            to pass user question to this agent

    template = """ ... """
    tools = [execute_python]
    prompt = PromptTemplate.from_template(template=template).partial(
        tools=render_text_description(tools),
        tool_names=", ".join([t.name for t in tools]),
    )
    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")#, callbacks=[AgentCallBackHandler()])
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
            "input": input,
            #        ^^^^^
            "agent_scratch": intermediate_steps
        })

        if isinstance(agent_step, AgentAction):
            tool_name = agent_step.tool
            tool_to_use = find_tool_by_name(tools, tool_name)
            tool_input = agent_step.tool_input

            observation = tool_to_use.invoke({"code": tool_input})

            intermediate_steps.append((agent_step, str(observation)))


    if isinstance(agent_step, AgentFinish):
        res = agent_step.return_values
        return res['output']
    #   ^^^^^^^^^^^^^^^^^^^^ return final answer as string
```


Then, we can start to build the Router Agent in the `main.py` file.

File: /home/matt/Projects/code-interpreter/main.py
```python
from langchain.agents import AgentExecutor, create_react_agent
from langchain.tools import Tool
from langchain import hub
from langchain_openai import ChatOpenAI

from python_agent import python_agent_executor
from csv_agent import csv_agent_executor


def main():
    tools = [
        Tool(
    #   ^^^^^ convert function of each agent executor to Tool object
            name="Python Agent",
            func=python_agent_executor,
            description="""useful when you need to transform natural language to python and execute the python code, returning the results of the code execution
            DOES NOT ACCEPT CODE AS INPUT""",
          # ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ sometime route agent will generate codes and pass it to python agent, but that's not what we want
          #                               route agent only pass user question to the appropriate agent
        ),
        Tool(
            name="CSV Agent",
            func=csv_agent_executor,
            description="""useful when you need to answer question over episode_info.csv file.
            takes an input the entire question and returns the answer after running pandas calculation"""
        ),
    ]

    prompt = hub.pull("langchain-ai/react-agent-template").partial(instructions="")
    #        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ route agent also use ReAct agent prompt, so it's also ReAct agent

    grand_agent = create_react_agent(
        prompt=prompt,
        llm=ChatOpenAI(temperature=0, model="gpt-4o-mini"),
        tools=tools,
    )

    grand_agent_executor = AgentExecutor(agent=grand_agent, tools=tools, verbose=True)

    print(
        grand_agent_executor(
            {
                "input": "which season has the most episodes?",
            }
        )
    )


if __name__ == "__main__":
    main()
```


Let's run the `main.py` file to see if the Router Agent works as expected.

```sh
 python main.py
/home/matt/Projects/code-interpreter/main.py:36: LangChainDeprecationWarning: The method `Chain.__call__` was deprecated in langchain 0.1.0 and will b
moved in 1.0. Use :meth:`~invoke` instead.
  grand_agent_executor(


> Entering new AgentExecutor chain...
Thought: Do I need to use a tool? Yes
Action: CSV Agent
Action Input: which season has the most episodes?

> Entering new AgentExecutor chain...
Thought: To determine which season has the most episodes, I need to count the number of episodes for each season in the dataframe and then identify th
ason with the highest count.
Action: python_repl_ast
Action Input: df['Season'].value_counts()Season
4    24
7    24
3    23
8    22
6    22
9    21
5    21
2    12
1     5
Name: count, dtype: int64I now know the final answer. The seasons with the most episodes are Season 4 and Season 7, each having 24 episodes. 

Final Answer: Seasons 4 and 7 have the most episodes, with 24 episodes each.

> Finished chain.
Seasons 4 and 7 have the most episodes, with 24 episodes each.Do I need to use a tool? No
Final Answer: Seasons 4 and 7 have the most episodes, with 24 episodes each.

> Finished chain.
{'input': 'which season has the most episodes?', 'output': 'Seasons 4 and 7 have the most episodes, with 24 episodes each.'}
```

Above, route agent choose to pass the user question to the CSV Agent, CSV Agent got final answer and return it to the route agent. Then, route agent known he has enough information to answer the user question, so he return the final answer to the user.


Let's try another question.

```sh
 python main.py
/home/matt/Projects/code-interpreter/main.py:36: LangChainDeprecationWarning: The method `Chain.__call__` was deprecated in langchain 0.1.0 and will b
e removed in 1.0. Use :meth:`~invoke` instead.
  grand_agent_executor(


> Entering new AgentExecutor chain...

Thought: Do I need to use a tool? Yes
Action: Python Agent
Action Input: "2 + 2"4Do I need to use a tool? No
Final Answer: 4

> Finished chain.
{'input': '2 + 2 =?', 'output': '4'}
```

This time, route agent choose to pass the user question to the Python Agent, Python Agent got final answer and return it to the route agent. Then, route agent known he has enough information to answer the user question, so he return the final answer to the user.
