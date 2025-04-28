---
date: 2025-03-06
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Install and setup LangGraph Studio to specific project

```sh
cd /path/to/your/project
pipenv shell
pipenv install "langgraph-cli[inmem]"
pip install "langgraph-cli[inmem]"

```

Create a `langgraph.json` file to the root directory of your project.


File: /home/matt/Projects/langgraph-course/langgraph.json
```json
{
  "graphs": {
    "agent": "./graph/graph.py:graph"  <--- target to the variable which contains the graph
  },
  "env": ".env",  <--- path to the .env file
  "dependencies": ["."]
}

```
Then, you can lauch the LangGraph Studio with the following command:

```sh
langgraph dev
```

It'll automatically open the browser with the LangSmith login page. You can login with your credentials and then the LangGraph Studio will be opened.
