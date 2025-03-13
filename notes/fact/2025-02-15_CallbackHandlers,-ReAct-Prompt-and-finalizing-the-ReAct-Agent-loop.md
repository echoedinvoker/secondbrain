---
date: 2025-02-15
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# CallbackHandlers, ReAct Prompt and finalizing the ReAct Agent loop


## Custom Callback Handlers

If we want to see how the each input and output of llm in [[2025-02-15_AgentAction,-AgentFinish,-ReAct-Loop|the previous topic]] are done, we can create a custom callback handler to observe.

Custom callback handlers must inherit from the `BaseCallbackHandler` class and it provides lots of methods to override. Each method is called when the corresponding event occurs.

Here is the official documentation of the `BaseCallbackHandler` class: [link](https://python.langchain.com/v0.1/docs/modules/callbacks/)
Just copy some of the methods that you want to override and paste them into your custom callback handler class.

File: /home/matt/Projects/react-langchain/callbacks.py (new file)
```python
from typing import Any, Dict, List
from langchain.schema import LLMResult
from langchain_core.callbacks.base import BaseCallbackHandler


class AgentCallBackHandler(BaseCallbackHandler):
#                          ^^^^^^^^^^^^^^^^^^^ inherits from BaseCallbackHandler

    # copy and paste the methods you want to override from the BaseCallbackHandler class
    def on_llm_start(
            self, serialized: Dict[str, Any], prompts: List[str], **kwargs: Any
        ) -> Any:
            """Run when LLM starts running."""

    def on_llm_end(self, response: LLMResult, **kwargs: Any) -> Any:
            """Run when LLM ends running."""
```

Then, we can write the content of the methods we want to override.

```py
from typing import Any, Dict, List
from langchain.schema import LLMResult
from langchain_core.callbacks.base import BaseCallbackHandler


class AgentCallBackHandler(BaseCallbackHandler):
    def on_llm_start(
            self, serialized: Dict[str, Any], prompts: List[str], **kwargs: Any
        ) -> Any:
            """Run when LLM starts running."""
            # override the on_llm_start method to see the prompt to llm
            print(f"***Prompt to LLM was:***\n{prompts[0]}")
            print("*************************************")

    def on_llm_end(self, response: LLMResult, **kwargs: Any) -> Any:
            """Run when LLM ends running."""
            # override the on_llm_end method to see the response of llm
            print(f"***LLM Response:***\n{response.generations[0][0].text}")
            print("*************************************")
```

Now, we can insert the custom callback handler into the llm object.

File: /home/matt/Projects/react-langchain/main.py
```python
from dotenv import load_dotenv
...
from callbacks import AgentCallBackHandler # import the custom callback handler
load_dotenv()


@ tool
def get_text_length(text: str) -> int:...

def find_tool_by_name(tools: List[BaseTool], tool_name: str) -> BaseTool:...

if __name__ == "__main__":
    print("Hello ReAct LangChain!")

    template = """..."""

    tools = [get_text_length]

    prompt = PromptTemplate.from_template(template=template).partial(
        tools=render_text_description(tools),
        tool_names=", ".join([t.name for t in tools]),
    )

    llm = ChatOpenAI(
        model="gpt-4o-mini",
        temperature=0,
        callbacks=[AgentCallBackHandler()], # insert the custom callback handler into the llm object
        #         ^^^^^^^^^^^^^^^^^^^^^^^^ this parameter accepts a list of callback handlers
        #                                  and don't forget to instantiate the custom callback handler class
    )
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

    agent_step: Union[AgentAction, AgentFinish] = agent.invoke({
        "input": "What is the length of the text 'DOG'?",
        "agent_scratch": intermediate_steps
    })

    if isinstance(agent_step, AgentAction):
        tool_name = agent_step.tool
        tool_to_use = find_tool_by_name(tools, tool_name)
        tool_input = agent_step.tool_input

        observation = tool_to_use.invoke({"text": tool_input})

        intermediate_steps.append((agent_step, str(observation)))

    agent_step: Union[AgentAction, AgentFinish] = agent.invoke({
        "input": "What is the length of the text 'DOG'?",
        "agent_scratch": intermediate_steps
    })

    if isinstance(agent_step, AgentFinish):
        print(agent_step.return_values)
```

Let's run the code and see the output.

```sh
 python main.py

Hello ReAct LangChain!

# below is the output of the method `on_llm_start` in the custom callback handler
***Prompt to LLM was:***
Human: 
    Answer the following questions as best you can. You have access to the following tools:

    get_text_length(text: str) -> int - Returns the length of the text by characters

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [get_text_length]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: What is the length of the text 'DOG'?
    Thought: 
    
*************************************

# below is the output of the method `on_llm_end` in the custom callback handler
***LLM Response:***
I need to find the length of the text 'DOG' in characters. 

Action: get_text_length  
Action Input: 'DOG'  

# below is the output of the method `on_llm_start` in the custom callback handler for the second invoking of the agent
*************************************
***Prompt to LLM was:***
Human: 
    Answer the following questions as best you can. You have access to the following tools:

    get_text_length(text: str) -> int - Returns the length of the text by characters

    Use the following format:

    Question: the input question you must answer
    Thought: you should always think about what to do
    Action: the action to take, should be one of [get_text_length]
    Action Input: the input to the action
    Observation: the result of the action
    ... (this Thought/Action/Action Input/Observation can repeat N times)
    Thought: I now know the final answer
    Final Answer: the final answer to the original input question

    Begin!

    Question: What is the length of the text 'DOG'?
    Thought: I need to find the length of the text 'DOG' in characters. 

Action: get_text_length  
Action Input: 'DOG'  
Observation: 3
Thought: 
    
*************************************

# below is the output of the method `on_llm_end` in the custom callback handler for the second invoking of the agent
***LLM Response:***
I now know the final answer.  
Final Answer: 3
*************************************

{'output': '3'}

```
Or you can just check LangSmith dashboard to see any detailed logs. @_@


## Refactor the ReAct Agent loop

So far, we have not truly used a loop to execute the ReAct Agent, instead manually invoking the agent twice. We can now replace these two invocations with a loop.


```py
...
from typing import List, Optional, Union
...
load_dotenv()


@ tool
def get_text_length(text: str) -> int:...

def find_tool_by_name(tools: List[BaseTool], tool_name: str) -> BaseTool:...

if __name__ == "__main__":
    print("Hello ReAct LangChain!")

    template = """..."""

    tools = [get_text_length]

    prompt = PromptTemplate.from_template(template=template).partial(
        tools=render_text_description(tools),
        tool_names=", ".join([t.name for t in tools]),
    )

    llm = ChatOpenAI(
        model="gpt-4o-mini",
        temperature=0,
        callbacks=[AgentCallBackHandler()],
    )
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

    agent_step: Optional[Union[AgentAction, AgentFinish]] = None # declare the agent_step variable before the loop
    #           ^^^^^^^^ shorthand for Union[T, None]

    while agent_step is None or isinstance(agent_step, AgentAction): # loop until the agent_step is an instance of AgentFinish
        agent_step = agent.invoke({
        #         ^ because we already declared the agent_step variable before the loop, we don't need define the type of the variable again
            "input": "What is the length of the text 'DOG'?",
            "agent_scratch": intermediate_steps
        })

        if isinstance(agent_step, AgentAction):
            tool_name = agent_step.tool
            tool_to_use = find_tool_by_name(tools, tool_name)
            tool_input = agent_step.tool_input

            observation = tool_to_use.invoke({"text": tool_input})

            intermediate_steps.append((agent_step, str(observation)))

    # agent_step: Union[AgentAction, AgentFinish] = agent.invoke({   <-- remove redundant code
    #     "input": "What is the length of the text 'DOG'?",
    #     "agent_scratch": intermediate_steps
    # })

    # when the loop ends, the agent_step will be an instance of AgentFinish, and it'll be handled here
    if isinstance(agent_step, AgentFinish):
        print(agent_step.return_values)
```




