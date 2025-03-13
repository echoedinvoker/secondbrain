---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Using LangChain the Simple Way

```bash
$ mkdir pycode
$ cd pycode
$ python -m venv venv
$ source venv/bin/activate.fish
$ pip install -U langchain-openai
$ nvim main.py
```

```python
from langchain_openai import OpenAI

llm = OpenAI()  # create a obeject that can be used to generate text using the model
                # you must store your OpenAI API key in the environment variable OPENAI_API_KEY

result = llm("Write a very short story") # give the model a prompt and get the result
print(result)
```

```bash
$ python main.py
/home/matt/Projects/pycode/main.py:7: LangChainDeprecationWarning: The method `BaseLLM.__call__` was deprecated in langchain-core 0.1.7 and will be removed in 1.0. Use :meth:`~invoke` instead.
  result = llm("Write a very short story")

Once upon a time, there was a little girl named Lily who loved to explore the forest near her house. One day, while she was wandering through the trees, she stumbled upon a magical fairy. The fairy granted her three wishes, but warned her to use them wisely. Lily thought long and hard about what she wanted and finally asked for a beautiful garden, a loyal pet unicorn, and the ability to fly. The fairy granted her wishes and from that day on, Lily spent her days playing in her magical garden, riding her unicorn, and soaring through the sky. She never forgot the lesson the fairy taught her about making wise choices, and she lived happily ever after.
```

We have demonstrated a simple example of using LangChain above, which is not typically the way it is used in actual projects.

However, the purpose of LangChain is to provide interchangeable tools, so we start with simple learning and then gradually expand to more complex usage.




