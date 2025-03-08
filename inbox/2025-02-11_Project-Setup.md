---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Project Setup

```sh
$ git clone git@github.com:emarco177/ice_breaker.git

$ cd ice_breaker

# then switch to branch `1-start-here`

$ pipenv --python 3.13 # create a virtual environment

$ pipenv shell # activate the virtual environment

$ pipenv install langchain # langchain core

$ pipenv install langchain-openai # openai llm model wrapped with Langchain

$ pipenv install langchain-community

$ pipenv install langchainhub # download prompts dynamically

```

Create a file `ice_breaker.py` and add the following code:

```py
if __name__ == '__main__':
    print("Hello, World!")
```

Set the runner (only for pyCharm users).

Install formatter:
```sh
$ pipenv install black
```

Then, you can use it to format your code:
```sh
$ black .
```



