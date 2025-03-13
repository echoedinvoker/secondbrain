---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Defining a Tool

We'll implement the [[2025-01-31_Understanding-ChatGPT-Functions|Understanding ChatGPT Functions]] in code with the LangChain class `Tool`. This class helps us define a tool easily and efficiently.

In the course video, `Tool.from_function()` is used to define a tool, but in the new version of LangChain, it is recommended to use the decorator `@tool` to define a tool. This can make the code more concise.

```sh
$ tree -I venv
.
├── db.sqlite # SQLite database with lots of fake data inside
├── main.py
└── tools
    └── sql.py # define a tool to run a sqlite query
```

```py
import sqlite3
from langchain_core.tools import tool

conn = sqlite3.connect('db.sqlite')

@tool # in new version of LangChain, use the decorator @tool to define a tool directly
def run_sqlite_query(query: str): # tool name is the function name by default
    """Run a sqlite query."""  # tool description is the docstring of the function by default
    cursor = conn.cursor()
    cursor.execute(query)
    return cursor.fetchall()
```

In this way, we have simply defined a tool. In the next topic, we will learn how to link it to the chain pipeline for use.
