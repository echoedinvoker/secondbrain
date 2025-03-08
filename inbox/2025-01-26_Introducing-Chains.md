---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Introducing Chains

Assuming we want to create an app that allows users to input a *language* and *task*, and then receive the corresponding *code*.

![get-task-code.png](../assets/imgs/get-task-code.png)

In the above framework, OpenAI's AI model is used, but sometimes there are problems, so the ability to switch models flexibly is needed.

![flexibly-switch-models.png](../assets/imgs/flexibly-switch-models.png)
If I need to add some processes after obtaining the code, such as checking for errors, we can easily extend it.

![extend-the-process-tasks.png](../assets/imgs/extend-the-process-tasks.png)

We can see some repeated patterns from the above figure.

![repeat-pattern.png](../assets/imgs/repeat-pattern.png)

Based on the above analysis, the Chain class is proposed. There are two big goals for it:

**1. Provide tools to automate each step of a text generation pipeline**
**2. Make it easy to connect tools together**

![chain-layout.png](../assets/imgs/chain-layout.png)

- We use Chains to make reusable text-generation pipelines. 
- Chains can be connected together to make a more complex pipeline.
- A chain wraps a *PromptTemplate* and an *LLM*.

PromptTemplate is a class to generate prompts.

![prompt-template.png](../assets/imgs/prompt-template.png)

LLM is also a class to access the language model.

![language-model.png](../assets/imgs/language-model.png)

Chains have Inputs and Outputs before and after, Inputs provide data to PromptTemplate to generate prompts, Outputs then return the generated text and the content of Inputs.

![input-output-of-chain.png](../assets/imgs/input-output-of-chain.png)
Inputs and Outputs are both dictionaries.

Genrated text is under the key "text", and "text" just is default key, you can change it.

We use the above layout to draw out the front extended app as follows:

![extended-app-chains-layout.png](../assets/imgs/extended-app-chains-layout.png)
Let's review LangChain's two big goals.
**1. Provide tools to automate each step of a text generation pipeline**
**2. Make it easy to connect tools together**

The design of the Chain class is to achieve these two goals, so it is one of the most essential classes in LangChain.


