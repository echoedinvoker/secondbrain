---
date: 2025-01-31
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Shortcomings in ChatGPTs Assumptions

If we change the user's input slightly in the app of [[2025-01-31_Use-LangGraph-to-implement-Agent-with-tool|Use LangGraph to implement Agent with tool]]:


```python

# ...

initial_input = {
    "messages": [
        {
            "role": "human",
            "content": "How many users provide shipping address?"
            #           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        }
    ]
}
result = app.invoke(initial_input)

print(result['messages'][-1].content)

```

Let's see what happens when we run this code:

```sh
$ python main1.py
It seems that the `users` table does not have a column for `shipping_address`. Therefore, I cannot determine how many users provide a shipping address
.

If you have a different table that contains shipping address information or other relevant information, please let me know!
```

The AI model made a bold assumption that the `users` table has a `shipping_address` column, but this assumption is incorrect, leading to the inability to answer the question.

Therefore, instead of letting the AI model assume our database schema, we should actively inform the AI model of our schema so that the AI model can provide the correct SQL query and obtain the correct answers.

We'll learn how to do this in the next topic.






