---
date: 2025-03-04
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Question Router Chain

We want to create a chain to route user question to the right flow.

```sh
 tree
.
├── graph
│   ├── chains
│   │   ├── answer_grader.py
│   │   ├── generation.py
│   │   ├── hallucination_grader.py
│   │   ├── __init__.py
│   │   ├── retrieval_grader.py
│   │   ├── router.py # new file, create a chain to route the question to the right flow
│   │   └── tests
│   │       ├── __init__.py
│   │       └── test_chains.py
│   ├── consts.py
│   ├── graph.py
│   ├── __init__.py
│   ├── nodes
│   │   ├── generate.py
│   │   ├── grade_documents.py
│   │   ├── __init__.py
│   │   ├── retrieve.py
│   │   └── web_search.py
│   └── state.py
├── ingestion.py
├── main.py
├── Pipfile
└── Pipfile.lock

```

```py
from typing import Literal # used to type a predefined set of values
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.pydantic_v1 import BaseModel, Field
from langchain_openai import ChatOpenAI


class RouteQuery(BaseModel):
    """Route a user query to the most relevant datasource."""

    datasource: Literal["vectorstore", "websearch"] = Field(
        ..., # when the value of Field is set to ... it means that the field is required when instantiating the class
        description="Given a user question choose to route it to websearch or vectorstore.",
    )

   
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
structured_llm_router = llm.with_structured_output(RouteQuery)

route_prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            # We mention what topics are covered by the vectorstore, so this chain don't need to really check the content of the vectorstore
            """You are an expert at routing a user question to a vectorstore or websearch.
            The vectorstore contains documents related to agents, prompt engineering, and adversarial attacks.
            Use the vectorstore for questions on these topics. For all else, use websearch."""
        ),
        ("human", "{question}"),
    ]
)

question_router = route_prompt | structured_llm_router

```

Write tests for the router chain.

```py
...

from graph.chains.router import question_router, RouteQuery

...


def test_question_router_vectorstore() -> None:
    question = "agent memory"
    res = question_router.invoke({"question": question})

    if isinstance(res, RouteQuery):
        assert res.datasource == "vectorstore"
    else:
        assert False

def test_question_router_websearch() -> None:
    question = "how to make a cake"
    res = question_router.invoke({"question": question})

    if isinstance(res, RouteQuery):
        assert res.datasource == "websearch"
    else:
        assert False

```

Run the tests.

```sh
 pytest . -s -v
====================================================================================== test session starts ======================================================================================
platform linux -- Python 3.13.1, pytest-8.3.4, pluggy-1.5.0 -- /home/matt/.local/share/virtualenvs/langgraph-course-uhZ6dcGU/bin/python
cachedir: .pytest_cache
rootdir: /home/matt/Projects/langgraph-course
plugins: langsmith-0.3.11, anyio-4.8.0
collected 2 items                                                                                                                                                                               

graph/chains/tests/test_chains.py::test_question_router_vectorstore PASSED
graph/chains/tests/test_chains.py::test_question_router_websearch PASSED

```
