---
date: 2025-02-24
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Project Setup

```sh
 mkdir code-interpreter
 cd code-interpreter/
 touch main.py
 pipenv shell
 pipenv install langchain python-dotenv black langchainhub langchain-openai langchain-experimental qrcode

```

langchain-experimental contains some features that the LangChain team considers risky, as we want to enable LLM to execute any code. These features may have security issues.

Therefore, it is not recommended to use this project in a production environment. If necessary, some security checks must be done.


Copy `.env` file from other project:

File: /home/matt/Projects/code-interpreter/.env
```sh
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
LANGSMITH_API_KEY="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"
LANGSMITH_PROJECT="Code-Interpreter" # change project name
```

Create a simple test content in `main.py`:

File: /home/matt/Projects/code-interpreter/main.py
```python
from dotenv import load_dotenv


load_dotenv()

def main():
    print("Hello, world!")


if __name__ == "__main__":
    main()
```
