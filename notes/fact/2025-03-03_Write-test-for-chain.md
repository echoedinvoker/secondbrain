---
date: 2025-03-03
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Write test for chain

Because chain uses LLM as its core, testing it is different from testing regular functions, mainly there will be three main issues.

1. LLM's response is uncertain.
2. LLM is a third-party service provider, so there may be many errors coming from third parties. Such as network errors, server internal errors, token limit errors, etc.
3. LLM is cost, so we need to consider the cost of testing.

Based on the three problems mentioned above, we may not be able to incorporate the testing of the chain into CICD, but we can still run the test manually. Better than no test at all.

Let's put above issues aside, and write some test to the chain we made in [[2025-03-03_retrieva_grader-chain|the previous topic]]:

```sh
 tree
.
├── graph
│   ├── chains
│   │   ├── __init__.py
│   │   ├── retrieval_grader.py
│   │   └── tests
│   │       ├── __init__.py
│   │       └── test_chains.py # write tests here
│   ├── consts.py
│   ├── graph.py
│   ├── __init__.py
│   ├── nodes
│   │   ├── __init__.py
│   │   └── retrieve.py
│   └── state.py
├── ingestion.py
└── main.py
```
```py
from dotenv import load_dotenv
from graph.chains.retrieval_grader import GradeDocument, retrieval_grader
from ingestion import retriever


load_dotenv()


def test_retrival_grader_answer_yes() -> None:
    question = "agent memory" # a question that should has relevant documents in the vector storage
    docs = retriever.similarity_search(question)
    res: GradeDocument = retrieval_grader.invoke(
        {
            "question": question,
            "document": docs[0].page_content
        }
    )
    assert res.binary_score == "yes" # binary score should be `yes`

def test_retrival_grader_answer_no() -> None:
    question = "how to make a cake" # a question that should has NO relevant documents in the vector storage
    docs = retriever.similarity_search(question)
    res: GradeDocument = retrieval_grader.invoke(
        {
            "question": question,
            "document": docs[0].page_content
        }
    )
    assert res.binary_score == "no" # binary score should be `no`

```

Let's run the test with `pytest` command:

```sh
 pytest . -s -v
================================================================== test session starts ===================================================================
platform linux -- Python 3.13.1, pytest-8.3.4, pluggy-1.5.0 -- /home/matt/.local/share/virtualenvs/langgraph-course-uhZ6dcGU/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/langgraph-course
plugins: langsmith-0.3.11, anyio-4.8.0
collected 2 items                                                                                                                                        

graph/chains/tests/test_chains.py::test_retrival_grader_answer_yes PASSED  
graph/chains/tests/test_chains.py::test_retrival_grader_answer_no PASSED
# tests should all pass                                           ^^^^^^

```

We can modify the codes to remove diagnostics issue:

```py
from dotenv import load_dotenv
from graph.chains.retrieval_grader import GradeDocument, retrieval_grader
from ingestion import retriever


load_dotenv()


def test_retrival_grader_answer_yes() -> None:
    question = "agent memory"
    docs = retriever.similarity_search(question)
    res = retrieval_grader.invoke(
    #  ^ instead of put `GradeDocument` here, we verify the type of the invoke result below
        {
            "question": question,
            "document": docs[0].page_content
        }
    )

    # verify the type of the result, it should be `GradeDocument`, if not, the test also failed
    if isinstance(res, GradeDocument):
        assert res.binary_score == "yes"
    else:
        assert False

def test_retrival_grader_answer_no() -> None:
    question = "how to make a cake"
    docs = retriever.similarity_search(question)
    res = retrieval_grader.invoke(
    #  ^ instead of put `GradeDocument` here, we verify the type of the invoke result below
        {
            "question": question,
            "document": docs[0].page_content
        }
    )

    # verify the type of the result, it should be `GradeDocument`, if not, the test also failed
    if isinstance(res, GradeDocument):
        assert res.binary_score == "no"
    else:
        assert False
