---
date: 2025-02-26
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Project Setup

```sh
 mkdir reflection-agent
 cd reflection-agent/
 pipenv shell
 pipenv install python-dotenv black isort langchain langchain-openai langgraph # install dependencies, isort is for formatting
```

Copy .env from other AI projects, which has LangSmith API key.

File: /home/matt/Projects/reflection-agent/.env
```sh
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
LANGSMITH_API_KEY="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"
LANGSMITH_PROJECT="reflection-agent"
#                  ^^^^^^^^^^^^^^^^
```

Create a simple main.py file to test the setup.

File: /home/matt/Projects/reflection-agent/main.py
```python
from dotenv import load_dotenv

load_dotenv()


if __name__ == "__main__":
    print("Hello, World!")
```
