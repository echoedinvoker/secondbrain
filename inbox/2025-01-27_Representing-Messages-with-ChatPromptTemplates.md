---
date: 2025-01-27
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Representing Messages with ChatPromptTemplates

The key point of a chat style AI app is how to hold a list of messages with different types. This task mainly falls on the prompt template, so LangChain provides another prompt template, _ChatPromptTemplate_, to handle this issue.


![chain-using-chatprompttemplate.png](../assets/imgs/chain-using-chatprompttemplate.png)
Let's see how to use the _ChatPromptTemplate_ in the chain.


![dive-into-chatprompttemplate.png](../assets/imgs/dive-into-chatprompttemplate.png)

Compared to the previous PromptTemplate which only holds a single prompt string, ChatPromptTemplate will hold a list of messages, and each message will have its own type.

When sending a prompt to LLM, the placeholders in each message will be filled with corresponding values based on inputs before sending them all out.


## Let's actually using it

```py 
from langchain_core.prompts import ChatPromptTemplate

prompt_template = ChatPromptTemplate.from_messages([
    ("human", "{content}"),
])

result = prompt_template.invoke({ "content": "Hello, world!" })

print(result)
```

Let's run the code above. The output will be:

```bash
$ python main.py
messages=[HumanMessage(content='Hello, world!', additional_kwargs={}, response_metadata={})]
```

