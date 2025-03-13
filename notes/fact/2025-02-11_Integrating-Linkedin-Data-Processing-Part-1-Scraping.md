---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Integrating Linkedin Data Processing Part 1 Scraping

Instead of hardcoding the information about Elon Musk, we can scrape the information from a LinkedIn profile with third-party services like [scrapin.io](https://scrapin.io/). In this tutorial, I'll use a mock data from a gist to simulate the scraping process.

```sh
 tree
.
├── ice_breaker.py
├── LICENSE
├── Pipfile
├── Pipfile.lock
├── README.md
└── third_parties # add this module
    ├── __init__.py
    └── linkedin.py # we will write the logic of scraping linkedin profile here

```

File: /home/matt/Github/ice_breaker/third_parties/linkedin.py
```python
import requests

def scrape_linkedin_profile(linkedin_profile_url: str, mock: bool = True):
    # if mock:
    linkedin_profile_url = "https://gist.githubusercontent.com/emarco177/859ec7d786b45d8e3e3f688c6c9139d8/raw/5eaf8e46dc29a98612c8fe0c774123a7a2ac4575/eden-marco-scrapin.json"
    response = requests.get(
        linkedin_profile_url,
        timeout=10
    )
    #### course use the api of scrapin.io to scrape linkedin profile, but I don't use it at all, just mock the data from the gist
    # else:
    #     api_endpoint = "https://api.scrapin.io/enrichment/profile"
    #     params = {
    #         "apikey": os.environ["SCRAPIN_API_KEY"],
    #         "linkedInUrl": linkedin_profile_url
    #     }
    #     response = requests.get(
    #         api_endpoint,
    #         params=params,
    #         timeout=10
    #     )

    data = response.json().get("person")
    #      ^^^^^^^^^^^^^^^ turn json response to python dict

    # remove any fields that have empty values, and remove the certifications field specifically
    data = {
        k: v
        for k, v in data.items()
        if v not in ([], "", "", None) and k not in ["certifications"]
    }

    return data
```

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

from third_parties.linkedin import scrape_linkedin_profile

# remove Elon Must's information, we'll replace it with the linkedin data

if __name__ == '__main__':
    summary_template = """
        given the information {information} about a person from I want you to create:
            1. a short summary
            2. two interesting facts about them
    """

    summary_prompt_template = PromptTemplate(input_variables=['information'], template=summary_template)
    llm = ChatOpenAI(temperature=0, model="gpt-3.5-turbo")

    chain = summary_prompt_template | llm | StrOutputParser()

    # get linkedin data
    linkedin_data = scrape_linkedin_profile(
            linkedin_profile_url="https://www.linkedin.com/in/eden-marco/",
    )

    res = chain.invoke(input={"information": linkedin_data})
    #                                        ^^^^^^^^^^^^^ replace prompt's placeholder with linkedin data

    print(res)
```

Let's run the script and see the output:

```sh
$ python ice_breaker.py

1. Short Summary:
Eden Marco is a Backend developer turned AI Engineer, known for being a best-selling Udemy instructor. Currently working as an LLM Specialist in Custo
mer Engineering at Google Cloud, Eden has a strong background in software engineering and a passion for teaching.

2. Two Interesting Facts:
- Eden has produced and published 5 best-selling courses on Udemy, with over 100,000 enrolled students and a solid 4.7-star rating.
- In addition to his professional roles, Eden has also served as a Computer Science Teaching Assistant at Reichman University, where he taught courses
 on Functional Programming and Introduction to Computer Science.
```




