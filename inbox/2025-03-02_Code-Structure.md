---
date: 2025-03-02
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Code Structure

```sh
 tree
.
├── graph
│   ├── chains # write chains and each chain will be eventually used in the node's function
│   │   ├── __init__.py
│   │   └── tests # we'll use pytest to test chains
│   │       ├── __init__.py
│   │       └── test_chains.py
│   │       #   ^^^^ the test file name's prefix and its directory name are important
│   │       #        because pytest will automatically discover the tests directory and run all the tests in the files that start with test_
│   ├── consts.py # define constants
│   ├── graph.py # we'll define the nodes and edges of the graph here
│   ├── __init__.py
│   ├── nodes # write nodes' functions
│   │   └── __init__.py
│   └── state.py # define the state schema of the graph
├── ingestion.py # download information and index it to the vector storage
└── main.py

```

## Write a dummy test

File: /home/matt/Projects/langgraph-course/graph/chains/tests/test_chains.py
```python
def test_foo() -> None:
    assert True
```

Let's run the test with pytest:

```sh
 pytest . -s -v
# . means discover tests from the current directory
# -s means print the output of the tests to the stdout
# -v means `verbose` mode which prints the test names and their results

=============================== test session starts ================================
platform linux -- Python 3.13.1, pytest-8.3.4, pluggy-1.5.0 -- /home/matt/.local/share/virtualenvs/langgraph-course-uhZ6dcGU/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/langgraph-course
plugins: langsmith-0.3.11, anyio-4.8.0
collected 1 item                                                                   

graph/chains/tests/test_chains.py::test_foo PASSED # our dummy test passed :D

================================ 1 passed in 0.01s =================================

```
