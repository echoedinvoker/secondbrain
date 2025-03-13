---
date: 2025-02-26
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# ReAct agents are TOO flexible!

In Agent, we usually use the ReAct agent to make decisions, determining which Tool (step) to use.

![ReAct-agent-decide-next.png](../assets/imgs/ReAct-agent-decide-next.png)

The figure assumes there is a ReAct agent that can use two tools or end directly. We can see that the decision of the ReAct agent is very flexible.

But because it is too flexible, it can lead to the following situations:

![always-tool1-no-end.png](../assets/imgs/always-tool1-no-end.png)
![always-same-tool-in-code.png](../assets/imgs/always-same-tool-in-code.png)

There are many reasons for the issues mentioned above, such as unclear tool descriptions, choosing the wrong tool for ReAct agent, or using a tool that does not exist at all...

![often-caused-by-mistool.png](../assets/imgs/often-caused-by-mistool.png)
Our ultimate goal is to have a flexible and reliable method to implement the LLM application.

![final-goal-to-have-both.png](../assets/imgs/final-goal-to-have-both.png)





