---
date: 2025-02-28
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Tool Executor Agent (Part B)

File: /home/matt/Projects/reflexion-agent/chains.py
```python
...

from langchain_core.output_parsers.openai_tools import JsonOutputToolsParser

parser = JsonOutputToolsParser(return_id=True) # Create a parser to parse the tool call message from LLM

...
```

Then, we can use the parser in the `tool_executor.py` file to parse the output of the responder agent, which is the last message of the state to the node `Tool Execute`:

```py
...

from chains import parser

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:
    tool_invocation: AIMessage = state[-1]
    parsed_tool_calls = parser.invoke(tool_invocation) # to parse the tool call message from LLM


if __name__ == "__main__":
    print("Tool Executor Enter")

    human_message = HumanMessage(
        content="Write about AI-Powered SOC /autonomous SOC problem domain, list startups that do that and raised capital."
    )

    answer = AnswerQuestion(
        answer="",
        reflection=Reflection(missing="", superfluous=""),
        search_queries=[
            "AI-Powered SOC startups funding",
            "AI SOC problem domain specifics",
            "Technologies used by AI-Powered SOC startups",
        ],
        id="call_KpYHichFFEmLitHFvFhKy1Ra",
    )

    raw_res = execute_tools(
        state=[
            human_message,
            AIMessage( # this is the last message of the state to the node `Tool Execute`, which is parsed by the parser
                content="",
                tool_calls=[
                    {
                        "name": AnswerQuestion.__name__,
                        "args": answer.dict(),
                        "id": "call_KpYHichFFEmLitHFvFhKy1Ra",
                    }
                ]
            )
        ]
    )
```

We can run the `tool_executor.py` file to see the input and output of the parser on the LangSmith:

```sh
input:
  content: ""
  additional_kwargs: {}
  response_metadata: {}
  type: ai
  example: false
  tool_calls:
    - name: AnswerQuestion
      args:
        answer: ""
        reflection:
          missing: ""
          superfluous: ""
        search_queries:
          - AI-Powered SOC startups funding
          - AI SOC problem domain specifics
          - Technologies used by AI-Powered SOC startups
      id: call_KpYHichFFEmLitHFvFhKy1Ra
      type: tool_call
  invalid_tool_calls: []


output:
  - args:
      answer: ""
      reflection:
        missing: ""
        superfluous: ""
      search_queries:
        - AI-Powered SOC startups funding
        - AI SOC problem domain specifics
        - Technologies used by AI-Powered SOC startups
    id: call_KpYHichFFEmLitHFvFhKy1Ra
    type: AnswerQuestion

```

The parser extracts the tool call part out of the AI message and returns it as a dictionary.

Then, we will loop the search_queries to get the invocations and the ids for the tool calls:

```py

...

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:
    tool_invocation: AIMessage = state[-1]
    parsed_tool_calls = parser.invoke(tool_invocation)

    ids = []
    tool_invocations = []

    for parsed_call in parsed_tool_calls:
        for query in parsed_call["args"]["search_queries"]:
            tool_invocations.append(ToolInvocation(
                tool="tavily_search_results_json", # tool name, we'll create this tool in the next step
                tool_input=query, # query string, which extracted from the search_queries
            ))
            ids.append(parsed_call["id"]) # each tool invocation need an id, here we just used the parsed id,
                                          # so all the invocations are corresponding to the same id


if __name__ == "__main__": ...

```

We have everything ready for all the invoke tools above, but we have not implemented the tool yet, so next we need to implement the tool.

```py

... 

import os
from langchain_community.tools.tavily_search import TavilySearchResults
from langchain_community.utilities.tavily_search import TavilySearchAPIWrapper
from langgraph.prebuilt import ToolInvocation, ToolExecutor


load_dotenv()


search = TavilySearchAPIWrapper(tavily_api_key=os.environ["TAVILY_API_KEY"]) # wrap the Tavily API
tavily_tool = TavilySearchResults(api_wrapper=search, max_results=5) # make the Tavily wrapper a tool
tool_executor = ToolExecutor([tavily_tool]) # create a tool executor which has the method `batch` to execute the tools in parallel

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:
    tool_invocation: AIMessage = state[-1]
    parsed_tool_calls = parser.invoke(tool_invocation)

    ids = []
    tool_invocations = []

    for parsed_call in parsed_tool_calls:
        for query in parsed_call["args"]["search_queries"]:
            tool_invocations.append(ToolInvocation(
                tool="tavily_search_results_json",
                tool_input=query,
            ))
            ids.append(parsed_call["id"])

    # here we use the tool executor to execute the tool with multiple invocations in parallel
    outputs = tool_executor.batch(tool_invocations)


if __name__ == "__main__":
    print("Tool Executor Enter")

    human_message = HumanMessage(
        content="Write about AI-Powered SOC /autonomous SOC problem domain, list startups that do that and raised capital."
    )

    answer = AnswerQuestion(
        answer="",
        reflection=Reflection(missing="", superfluous=""),
        search_queries=[
            "AI-Powered SOC startups funding",
            "AI SOC problem domain specifics",
            "Technologies used by AI-Powered SOC startups",
        ],
        id="call_KpYHichFFEmLitHFvFhKy1Ra",
    )

    raw_res = execute_tools(
        state=[
            human_message,
            AIMessage(
                content="",
                tool_calls=[
                    {
                        "name": AnswerQuestion.__name__,
                        "args": answer.dict(),
                        "id": "call_KpYHichFFEmLitHFvFhKy1Ra",
                    }
                ]
            )
        ]
    )
