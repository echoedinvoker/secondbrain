---
date: 2025-02-13
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Output Parsers: Getting Ready to work with a Frontend

We used `StrOutputParser` before to parse the content field of llm output and do some formatting, but the result obtained in this way is plain text, which is not suitable for use in backend or frontend applications. Therefore, we need to use another parser to serialize the output values into an Object or JSON.

File: /home/matt/Github/ice_breaker/output_parsers.py (create new file)
```python
from typing import List, Dict, Any
from langchain_core.output_parsers import PydanticOutputParser
from pydantic import BaseModel, Field


# define pydantic model what we want to parse the output into
class Summary(BaseModel):
    summary: str = Field(description="summary")
    #                    ^^^^^^^^^^^^^^^^^^^^^
    facts: List[str] = Field(description="interesting facts about them")
    #                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ description is important for LLM to understand the meaning of the field

    # helper method to get the dict of the object to be used in the application
    def to_dict(self) -> Dict[str, Any]:
        return {"summary": self.summary, "facts": self.facts}


# create a parser object to parse the output into the Summary object
summary_parser = PydanticOutputParser(pydantic_object=Summary)


```
Now, we can replace the `StrOutputParser` with the `summary_parser` in the `ice_breaker.py` file.



File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI

from third_parties.linkedin import scrape_linkedin_profile
from third_parties.twitter import scrape_user_tweets
from agents.linkedin_lookup_agent import lookup as linkedin_lookup
from agents.twitter_lookup_agent import lookup as twitter_lookup
from output_parsers import summary_parser # import the summary_parser

def ice_break_with(name: str):
    linkedin_url = linkedin_lookup(name=name)
    linkedin_data = scrape_linkedin_profile(linkedin_profile_url=linkedin_url)

    twitter_username = twitter_lookup(name=name)
    user_tweets = scrape_user_tweets(username=twitter_username)

    summary_template = """
        given the information about a person from linkedin {information},
        and lastest twitter posts {twitter_posts}. I want you to create:
            1. a short summary
            2. two interesting facts about them
        
        Use both the linkedin information and the twitter posts to create the summary and the interesting facts.\n{format_instructions}"""
        #                                                                                                       ^^^^^^^^^^^^^^^^^^^^^^^
        #                                                                                                 we want to LLM know what kind of output object we want

    summary_prompt_template = PromptTemplate(
        input_variables=['information', 'twitter_posts'],
        template=summary_template,
        # because we already know the value of parameter `format_instructions`
        # we can pass it directly to the partial_variables before invoking the chain
        partial_variables={"format_instructions": summary_parser.get_format_instructions()}
        #                                                       ^^^^^^^^^^^^^^^^^^^^^^^^ this is a built-in method of BaseModel to get the format instructions
    )

    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")

    chain = summary_prompt_template | llm | summary_parser
    #                                     ^^^^^^^^^^^^^^^^ replace the StrOutputParser with the summary_parser

    res = chain.invoke(input={
        "information": linkedin_data,
        "twitter_posts": user_tweets
    })

    print(res)


if __name__ == '__main__':
    ice_break_with("Eden Marco")


```

Then, we can run the `ice_breaker.py` file to see if the output is parsed correctly.


```sh
 python ice_breaker.py

# ... (ommitted)

summary='Eden Marco is an AI Engineer and best-selling Udemy instructor with a strong background in backend development. Currently working as an LLM Specialist at Google Cloud, he has a proven track record in cloud computing and software engineering, having held multiple positions at notable companies like Orca Security and Deep Instinct. Eden is also an advocate for secure AI practi ces, as highlighted in his recent Twitter posts discussing the risks of deploying insecure LLM agents.'
facts=[
    'Eden has produced five best-selling courses on Udemy, amassing over 100,000 enrol led students and a 4.7-star rating.',
    'He recently demonstrated the alarming risks associated with deploying LLM agents to the cloud without proper security measures, showcasing his commitment to AI safety.'
]

```

The output is now in the form of a Python object with two fields: `summary` and `facts`. We can now use this object in our backend or frontend applications.

The important thing to note in `ice_breaker.py` is that we not only use `summary_parser` to replace `StrOutputParser`, but also need to inform the LLM in the prompt template of the expected output result, so that the LLM can correctly parse the output result.
