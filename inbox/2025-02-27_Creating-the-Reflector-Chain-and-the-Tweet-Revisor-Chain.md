---
date: 2025-02-27
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Creating the Reflector Chain and the Tweet Revisor Chain

In this topic, we need to prepare some chains to use in the graph.

We create them in the new file `chains.py`:

```py
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain_openai import ChatOpenAI


# create prompt for reflection chain, which to give critique and recommendations to the input tweet
reflection_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        "You are a viral twitter influencer grading a tweet. Generate critique and recommendations for the user's tweet. Always provide detailed recommendations, including requests for length, virality, style, etc."
    ),
    MessagesPlaceholder(variable_name="messages") # this object will replace by list of history messages
])

# create prompt for generation chain, which to generate the best tweet possible for the user's request
generation_prompt = ChatPromptTemplate.from_messages([
    (
        "system",
        "You are a twitter influencer assistant tasked with writing excellent tweets. Generate the best twitter post possible for the user's request. If the user provides critique, respond with a revised version of your previous attempts."
    ),
    MessagesPlaceholder(variable_name="messages")
])

llm = ChatOpenAI(model="gpt-4o-mini")

## compose the prompts and llm to chains which can be used in the graph directly
generate_chain = generation_prompt | llm
reflect_chain = reflection_prompt | llm

```
