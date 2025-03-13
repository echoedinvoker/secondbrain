---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Create react agent and execut it

File: /home/matt/Github/ice_breaker/agents/linkedin_lookup_agent.py
```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate
from langchain_core.tools import Tool
from langchain.agents import (
    create_react_agent,
    AgentExecutor,
)
from langchain import hub

def lookup(name: str) -> str:
    # create llm
    llm = ChatOpenAI(
        temperature=0,
        model="gpt-4o-mini"
    )

    # create prompt for invoking the agent
    template = """given the full name {name_of_person} I want you to get it me a link to their linkedin profile page.
                        You answer should contain only a URL"""
                    #   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                    # this is output indicator, which is very important to ensure the agent knows what to return, in this case, we just want a URL without any fluff                    # if you want more definitive output, you can use the output parser, we'll cover that in the future

    prompt_template = PromptTemplate(
        template=template, input_variables=["name_of_person"]
    )

    # create tools for the agent to use
    tools_for_agent = [
        Tool(
            name="Crawl Google 4 linkedin profile page",
            # func is actual function that will be called by the agent
            # we use ? as a placeholder for the actual function
            # and we'll replace it with the actual function later
            func="?",
            # description is extremely important for the react agent to know what the tool does, it should be both concise and informative
            description="useful for wen you need get the linkedin Page URL",
        )
    ]

    # pull pre-made react prompt for react agent with tools parameter
    react_prompt = hub.pull("hwchase17/react")

    # create agent with llm, tools (for insert to react prompt), and prompt(react)
    agent = create_react_agent(llm=llm, tools=tools_for_agent, prompt=react_prompt)

    # create agent executor with agent and tools again (for actuall use)
    agent_executor = AgentExecutor(agent=agent, tools=tools_for_agent, verbose=True)
    

    # invoke the agent through the agent executor with the prompt template we created at the start
    result = agent_executor.invoke(
        input={"input": prompt_template.format_prompt(name_of_person=name)}
    )

    # return the output key of the result
    linked_profile_url = result["output"]
    return linked_profile_url
```


