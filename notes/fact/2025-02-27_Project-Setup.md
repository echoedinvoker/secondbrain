---
date: 2025-02-27
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Project Setup

```sh
 mkdir reflexion-agent
 cd reflexion-agent/
 pipenv shell
 pipenv install python-dotenv black isort langchain langchain-openai langgraph langchain-community # we need langchain-community this time ._.

# copy .env from other project which has Langsmith API key
 cat .env
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
LANGSMITH_API_KEY="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"
LANGSMITH_PROJECT="reflexion-agent"

# create a simple main.py for testing
 cat main.py 
from dotenv import load_dotenv


load_dotenv()


if __name__ == '__main__':
    print('Hello, world!')

```
