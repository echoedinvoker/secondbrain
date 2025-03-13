---
date: 2025-02-01
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Adding Better Descriptions for Tool Arguments

In the course video, it is explained that when defining a tool using `Tool.from_function`, although LLM does receive the name and description of the tool, the description of the parameters is actually incomplete. Therefore, we need to define the description of the parameters using the `args_schema` parameter of `Tool.from_function`.

In new version of LangChain, decorator `@tool` already takes care of this. It automatically generates the description of the parameters based on the function signature. It saves us a lot of time and effort. 

