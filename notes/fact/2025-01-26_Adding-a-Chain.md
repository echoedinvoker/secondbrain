---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Adding a Chain

In [[2025-01-26_Introducing-Chains|Introducing Chains]], we learned about the concept of chains. However, in new LangChain version, the chain is not a class anymore. Instead, it is a pipeline of several Runnable objects.

And not only PromtTemplate and LLM, it adds a output parser in the end of the chain.

Let's create a simple chain for example:

```py
from langchain_openai import OpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate

# Create a prompt template, curly braces are placeholders for the values that will be passed to the prompt.
prompt = ChatPromptTemplate.from_template("Write a very short {language} function that will {task}.")

model = OpenAI() # We generate the text with this directly before,
                 # but it is actually a Runnable object.

# pipeline of Runnable objects to build the chain
chain = prompt | model | StrOutputParser()
#                        ^^^^^^^^^^^^^^^^^ this is new thing we've not talked about before

# invoke the chain with the input values that will be passed to the prompt
result = chain.invoke({ "language": "Python", "task": "add two numbers" })
print(result)
```

Then, you can run the script:

```bash
$ python main.py

def add(x, y):
    return x + y
```

You can see that the output of the chain is just a generated text by the model instead of dictionary that we mentioned in the previous topic.

If we remove the `StrOutputParser` from the chain:

```bash
$ python main.py


def add(x, y):
  return x + y
```

The output is almost the same, just indentation is a bit different. So, we understand that the output parse just be used to parse the output of the model to a specific format. Not necessary for all chains.

No matter with or without the output parser, the chain only outputs the generated text by the model now, not including the prompt inputs anymore in the new version.




