---
date: 2025-03-05
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Debug with LangSmith

For this topic, we use LangSmith to see the process of the graph running in [[2025-03-05_Stop-and-resume-graph|the previous topic]].

Copy .env file from other project to this project and change the project name:

File: /home/matt/Projects/human-in-the-loop-memory/.env
```sh
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT="https://api.smith.langchain.com"
LANGSMITH_API_KEY="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"
LANGSMITH_PROJECT="human-interrupt-demo"
#                  ^^^^^^^^^^^^^^^^^^^^ change this
```

Add above variables into the environment in the file `main.py`:

```py
from dotenv import load_dotenv
load_dotenv()
...

```

Then, we run the graph again and see the process in LangSmith:

![check-human-interrupt-on-langsmith.png](../assets/imgs/check-human-interrupt-on-langsmith.png)

We can see LangGraph execution being split into two executions by the LangGraphUpdateState in the middle.




