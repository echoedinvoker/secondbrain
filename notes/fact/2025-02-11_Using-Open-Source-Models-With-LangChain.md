---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Using Open Source Models With LangChain

LangChain allows us to easily switch from using Tier one AI models to open source models.

```sh
$ pipenv install langchain-ollama
```

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
# from langchain_openai import ChatOpenAI
from langchain_ollama import ChatOllama # import ChatOllama from langchain_ollama

information = """ ... """

if __name__ == '__main__':
    summary_template = """
        given the information {information} about a person from I want you to create:
            1. a short summary
            2. two interesting facts about them
    """

    summary_prompt_template = PromptTemplate(input_variables=['information'], template=summary_template)
    # llm = ChatOpenAI(temperature=0, model="gpt-3.5-turbo")
    llm = ChatOllama(temperature=0, model="llama3") # use open source model `llama3` for now

    chain = summary_prompt_template | llm

    res = chain.invoke(input={"information": information})

    print(res)
```

Amended as above, our application now uses the open source model 'llama3' to generate summaries.

Before running `ice_breaker.py`, please make sure that the Ollama server is already started, and the model `llama3` has been downloaded.


> Basically, we will only use tier one AI models in our future courses, because using open source models requires more testing to verify if the responses from different models meet our needs. Using tier one AI models directly can save us these steps. Additionally, using open source models when constructing a reasoning agent may lead to some strange errors.






