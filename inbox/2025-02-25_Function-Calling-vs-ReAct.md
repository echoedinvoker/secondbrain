---
date: 2025-02-25
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Function Calling vs ReAct

The key point is who is responsible for tool selection, similar to how in serverless computing we don't need to worry about server scaling, data backup, etc., but these issues have not disappeared, they have just shifted to the cloud provider. The advantage of this is that we can focus on our business logic, but the downside is that our control over these issues has weakened.


## Function calling

Function calling is to provide the function defined in the official document schema to LLM. This is equivalent to equipping LLM with this function. When it generates text, it will automatically choose whether to use this function to generate text.

Therefore, the responsibility of choosing the function lies with the `LLM vendor`. Although we also need to clearly define the function, ultimately it is up to the LLM vendor to decide whether to use this function to generate text, and we cannot exert too much control.


## ReAct

ReAct is a new way of thinking. We provide the function description in the prompt, and that prompt also turns LLM into a `reasoning agent` that has ability to select correct tools to solve the problem.

This way gives us complete control, we can fine-tune the prompt content to influence the logic of the LLM selection tool, so we can achieve the results we want.

However, this method places all responsibility on the developer. We need more time and knowledge to design prompts, and we need more testing to ensure the accuracy of prompts.

