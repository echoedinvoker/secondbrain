---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Recovering from Errors in Tools

We need to discuss the handling of errors that occur when using the call tool in this topic.

If you use LangGraph to implement an Agent with a tool like [[2025-01-31_Use-LangGraph-to-implement-Agent-with-tool|this]], `ToolNode` will wrap the error message in a `ToolMessage` and append it to the list of messages. So, you don't need to worry about handling errors in the tool functions themselves.

However, if you implement the logic similar to the AgentExecutor on your own without LangGraph like [[2025-01-31_Understanding-Agents-and-AgentExecutors|this]], you need to handle the errors in the tool functions. Here is an example of how you can do this:

File: /home/matt/Projects/pycode/tools/sql.py
```python
import sqlite3
from langchain_core.tools import tool

@tool
def run_sqlite_query(query: str):
    """Run a sqlite query."""
    conn = sqlite3.connect('db.sqlite')
    cursor = conn.cursor()
    try: # Add a try-except block to handle errors
        cursor.execute(query)
        return cursor.fetchall()
    except sqlite3.OperationalError as e: # Catch the specific error
        return f"The following error occurred: {str(e)}" # Wrap the error message in a string for main.py to append to the list of messages
    finally:
        conn.close()
```

In this case, when the tool encounters an error, it will package the error message into a string and return it to main.py to add to the message list sent to LLM, allowing LLM to determine whether to call the tool again to handle the error.

```sh
 python main.py

Iteration 1:
Executing query: SELECT COUNT(*) FROM users WHERE shipping_address IS NOT NULL;
Query result: The following error occurred: no such column: shipping_address

Iteration 2:
Executing query: SELECT COUNT(*) FROM users WHERE address IS NOT NULL;
Query result: The following error occurred: no such column: address

Iteration 3:
Executing query: SELECT name FROM sqlite_master WHERE type='table';
Query result: [('users',), ('addresses',), ('products',), ('carts',), ('orders',), ('order_products',)]

Iteration 4:
Executing query: SELECT COUNT(*) FROM addresses WHERE shipping_address IS NOT NULL;
Query result: The following error occurred: no such column: shipping_address

Iteration 5:
Executing query: SELECT * FROM addresses LIMIT 5;
Query result: [(1, 2, 'Unit 6404 Box 5297, DPO AE 13110'), (2, 2, '60141 Harrison Gardens Suite 626, East Jamesstad, FL 46023'), (3, 3, '41126 Angela Port, North Emilyport, OR 34162'), (4,
 4, 'Unit 7418 Box 9166, DPO AP 90017'), (5, 5, '782 Brent Junctions, North Brentbury, FM 84956')]
Maximum iterations reached without a final answer.

```

You can see that after LLM detects an error in the tool, it will decide on its own whether to call the tool again or give up, the decision is in the hands of LLM.


