---
date: 2025-03-08
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# LangGraph VS CrewAI main difference


## Both are multi-agent architectures

Both LangGraph and CrewAI are framworks for developing generative AI agents and specifically multi-agent architectures.


## What is multi-agent architecture I'm referring to?

And it's important to note that when I refer to a *multi-agent architecture*, I refer to an architecture where every agent is basically either a fancy prompt with some character.

We can have a critique character, which its job is to give us a very concise criticism over a draft artical we're writing, or an agent with external tools like web search or database access, etc.

And those agents can interact between them so they can send one message to another. Each output of an agent can be the input of another agent.


## LangGraph implements FLOW ENGINEERING

LangGraph is a framework that implements a flow engineering, where we as developers have total control of the flow of our agents so we can control which agent is talking to who. What is the state between them and what is the flow of our program is going to be.

This is very important because it gives us a lot of control, which is super important when implementing agents because if our agents get too autonomous, then they will simply scatter arround and won't really work.

I think what demonstrates this kind of behavior is the *auto-GPT project*, which is an amazing project, btw. It is super innovating and pushes the boundaries of what we can archive, but can't really be used in production system. And the main reason for this is that the agent has too much freedom.

And LangGraph addresses this freedom versus control trade-off with flow engineering techniques.


## CrewAI hides this logic

On the other hand, CrewAI hides this logic from the developer, we don't have as much control of this flow logic as we have in LangGraph.


## Conclusion

So, I think CrewAI is a nice framework, but I think if we want to have a production grade application that is going to be actually usable by users, then I think LangGraph is the way to go simply because of the control it gives.




