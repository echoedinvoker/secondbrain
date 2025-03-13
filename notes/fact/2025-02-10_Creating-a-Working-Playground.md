---
date: 2025-02-10
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Creating a Working Playground

To test the LLM streaming mode, we created a standalone running app as follows:

File: /home/matt/Projects/pdf/test.py
```python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

chat = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_messages([("human", "{question}")])

query = (
    prompt
    | chat
    | StrOutputParser()
)

response = query.invoke({"question": "What is the capital of France?"})
print(response)

```

Let's run the test:

```sh
 python test.py
The capital of France is Paris.
```

It is obvious that this app is running normally, but the response from LLM is printed out once, not streaming. We will refactor it in the next topic.


