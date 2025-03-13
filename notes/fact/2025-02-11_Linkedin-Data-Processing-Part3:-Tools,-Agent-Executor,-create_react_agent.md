---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Linkedin Data Processing Part3: Tools, Agent Executor, create_react_agent

We want to create an agent that can accept a person's name and use a tool to search the internet to find that person's LinkedIn URL.

```sh
 tree
.
├── agents # create this module
│   ├── __init__.py
│   └── linkedin_lookup_agent.py # we'll create the agent that will use the tool to find the LinkedIn URL here
├── ice_breaker.py
├── LICENSE
├── Pipfile
├── Pipfile.lock
├── README.md
└── third_parties
    ├── __init__.py
    └── linkedin.py
```

File: /home/matt/Github/ice_breaker/agents/linkedin_lookup_agent.py
```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate
from langchain_core.tools import Tool # this will turn any callable function to a tool, which can be used by an agent, chain, or LLM
from langchain.agents import (
    create_react_agent, # this function will create an agent with `react` algorithm, which is a popular algorithm for agents
    AgentExecutor, # this is runtime for agents, which means it will execute the agent and return the result
)

from langchain import hub # hub is used to pull pre-made prompts from the hub, these prompts are made by prompt engineers

def lookup(name: str) -> str:
    return "https://www.linkedin.com/in/eden-marco/"
```

