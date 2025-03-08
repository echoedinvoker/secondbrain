---
date: 2025-02-12
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Linkedin Data Processing Part5: Custom Search Agent Testing

In the [[2025-02-11_Linkedin-Data-Processing-Part4:-Custom-Search-Agent-Implementation|previous topic]], we created an agent to obtain the Linkedin URL about the input name, so we can use it in `ice_breaker.py` to retrieve the Linkedin URL for the `scrape_linkedin_profile` function to use instead of hardcoding the URL.

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

from third_parties.linkedin import scrape_linkedin_profile
from agents.linkedin_lookup_agent import lookup # import the lookup function from the agent

# create a function and move the codes from the main block to here
def ice_break_with(name: str):
    linkedin_url = lookup(name=name) # use the agent to get the linkedin URL about the input name
    linkedin_data = scrape_linkedin_profile(linkedin_profile_url=linkedin_url)
    #                                                            ^^^^^^^^^^^^ replace hardcoded URL with the url that we got from the agent

    # codes from the main block
    summary_template = """
        given the information {information} about a person from I want you to create:
            1. a short summary
            2. two interesting facts about them
    """

    summary_prompt_template = PromptTemplate(input_variables=['information'], template=summary_template)
    llm = ChatOpenAI(temperature=0, model="gpt-3.5-turbo")

    chain = summary_prompt_template | llm | StrOutputParser()

    chain.invoke(input={"information": linkedin_data})


if __name__ == '__main__':
    ice_break_with("Eden Marco") # simply call the function with the name we want to search


```

Let's run the final code to see if it works as expected.

```bash
 python ice_breaker.py 

/home/matt/.local/share/virtualenvs/ice_breaker-gEYUNqx7/lib/python3.13/site-packages/langsmith/client.py:253: LangSmithMissingAPIKeyWarning: API key must
 be provided when using hosted LangSmith API
  warnings.warn(


> Entering new AgentExecutor chain...
I need to find the LinkedIn profile page for someone named Eden Marco. To do this, I will use the tool to search for their LinkedIn profile.  
Action: Crawl Google 4 linkedin profile page  
Action Input: Eden Marco

[{'url': 'https://www.linkedin.com/in/edenmarco', 'content': 'Eden Marco\nStrategy Analyst at Deloitte\nNew York, United States\
n1032 connections, 1036 followers\n\n\nAbout:\nN/A\n\n\nExperience:\nStrategy Analyst at Deloitte Digital (https://www.linkedin.com/company/deloitte-digit
al)\nAug 2023 - Present\nNew York, New York, United States\n\n\nEducation:\nUNC Kenan-Flagler Business School\nBSBA, Business Administration\nN/A - Presen
t\nGrade: N/A\nActivities and societies: N/A'}, {'url': 'https://www.linkedin.com/in/eden-marco', 'content': 'Eden Marco\nLLMs @ Google Cloud | Best-selli
ng Udemy Instructor | Backend & GenAI | Opinions stated here are my own, not those of my company\nUnited States, United States of America\n4995 connection
s, 6872 followers\n\n\nAbout:\nBackend developer --> Now an AI Engineer, Udemy.com best seller instructor\nOpinions stated here are my own, not those of m
y company\n\n\nExperience:\nLLM Specialist, Customer Engineering - Google Cloud at Google (https://www.linkedin.com/company/google)\nJul 2023 - Present\n\
n\nEducation:\nTechnion - Israel Institute of Technology\nBachelor’s Degree, Computer Science\nJan 2015 - Dec 2019\nGrade: N/A\nActivities and societies: 
N/A'}, {'url': 'https://odsc.com/blog/speaker/eden-marco/', 'content': "Eden Marco | Open Data Science Conference Ai+ AI Agents East 2025 Training Track A
I Agents East 2025 Training Track With years of experience in backend development, he currently works at Google as an LLM Specialist, assisting customers 
in implementing complex generative AI solutions on GCP using open-source frameworks like LangChain and Google's generative AI services. This two-hour sess
ion is crafted for AI practitioners who already possess a background in Python and familiarity with LangChain, aiming to elevate their skills in developin
g cutting-edge LLM agentic applications. By the end of this workshop, participants will be equipped with the expertise to leverage LangGraph by LangChain 
for developing sophisticated LLM agents, ready to tackle a diverse range of applications in production environments."}, {'url': 'https://www.researchgate.
net/profile/Eden-Marco', 'content': 'Eden MARCO | University of Toronto, Toronto | U of T | Department of Medicine | Research profile Join ResearchGate to
 contact this researcher and connect with your scientific community. Join ResearchGate to contact this researcher and connect with your scientific communi
ty. Research Research Join ResearchGate to find the people and research you need to help your work Only verified researchers can join ResearchGate and sen
d messages to other members. University students and faculty, institute members, and independent researchers Not a researcher Sorry, you need to be a rese
archer to join ResearchGate. Recruit researchers Tip: Most researchers use their institutional email address as their ResearchGate login Tip: Most researc
hers use their institutional email address as their ResearchGate login'}, {'url': 'https://www.udemy.com/user/eden-marco/?srsltid=AfmBOor3MqaAxgx_fexDSHAm
FJ3s-fL_em0dkWrO8qim_nZI_5qolyfM', 'content': 'I am a passionate Software Engineer with years of experience in back-end development, one of the first engi
neers at Orca Security, and now I am working as a'}]

I have found multiple LinkedIn profiles for individuals named Eden Marco. I need to determine which on
e is the most relevant based on their current roles and locations. 

The first profile is for an Eden Marco who is a Strategy Analyst at Deloitte in New York. The second profile is for an Eden Marco who is an LLM Specialist
 at Google Cloud in the United States. 

Since the second profile seems to be more prominent and has a larger number of connections, I will choose that one.

Final Answer: https://www.linkedin.com/in/eden-marco # agent returned the LinkedIn URL about input name

> Finished chain.

## Response of the chain in ice_breaker.py
1. Short Summary:
Eden Marco is a Backend developer turned AI Engineer, known for being a best-selling Udemy instructor. Currently working as an LLM Specialist in Customer 
Engineering at Google Cloud, Eden has a strong background in software engineering and a passion for teaching.

2. Two Interesting Facts:
- Eden has produced and published 5 best-selling courses on the Udemy platform, with over 100K enrolled students and a solid 4.7-star rating.
- In addition to his professional roles, Eden has also served as a Computer Science Teaching Assistant at Reichman University, where he taught courses on 
Functional Programming and Introduction to Computer Science.

```



