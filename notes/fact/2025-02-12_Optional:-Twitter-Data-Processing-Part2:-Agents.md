---
date: 2025-02-12
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Optional: Twitter Data Processing Part2: Agents

After establishing [[2025-02-12_Optional:-Twitter-Data-Processing-Part1:-Scraping|the function for scraping tweets]], we need to create an agent to find the corresponding Twitter handle based on the name in order to provide the correct input for the scraping function.

This is very similar to [[2025-02-11_Create-react-agent-and-execut-it|the previous topic]], so we can simply copy it over and make modifications.

File: /home/matt/Github/ice_breaker/agents/twitter_lookup_agent.py
```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import PromptTemplate
from langchain_core.tools import Tool
from langchain.agents import (
    create_react_agent,
    AgentExecutor,
)
from langchain import hub
from tools.tools import get_profile_url_tavily

def lookup(name: str) -> str:
    llm = ChatOpenAI(
        temperature=0,
        model="gpt-4o-mini"
    )

    # modify the template to fit the Twitter search
    template = """
        given the name {name_of_person}, I want you to find a link to their Twitter profile page, and extract from it their username.
        In Your Final answer only the person's username"""

    prompt_template = PromptTemplate(
        template=template, input_variables=["name_of_person"]
    )
    tools_for_agent = [
        Tool(
            name="Crawl Google 4 Twitter username", # modify the name of the tool
            func=get_profile_url_tavily,
            description="useful for when you need get the Twitter username", # modify the description
        )
    ]
    react_prompt = hub.pull("hwchase17/react")
    agent = create_react_agent(llm=llm, tools=tools_for_agent, prompt=react_prompt)
    agent_executor = AgentExecutor(agent=agent, tools=tools_for_agent, verbose=True)
    
    result = agent_executor.invoke(
        input={"input": prompt_template.format_prompt(name_of_person=name)}
    )

    linked_profile_url = result["output"]
    return linked_profile_url


if __name__ == "__main__":
    linkedin_url = lookup(name="Eden Marco")
    print(linkedin_url)
```


Just a bit of modification is needed to adapt the agent to the Twitter username search. Let's test it out and see if it works as expected.

```sh
 python -m agents.twitter_lookup_agent
/home/matt/.local/share/virtualenvs/ice_breaker-gEYUNqx7/lib/python3.13/site-packages/langsmith/client.py:253: LangSmithMissingAPIKeyWarning: API key must
 be provided when using hosted LangSmith API
  warnings.warn(


> Entering new AgentExecutor chain...
I need to find the Twitter username for the person named Eden Marco. To do this, I will search for their Twitter profile using Google.

Action: Crawl Google 4 Twitter username  
Action Input: "Eden Marco Twitter"

[{'url': 'https://twitter.com/EdenEmarco177/status/1862167325799039295', 'content': "Nov 28, 2024 · Eden Marco · @Eden Emarco177. Really impressed with Cursor's latest agent features! But I really miss that composer CMD+SHIFT+I shortcut - it"}, {'url': 'https://twitter.com /EdenEmarco177/highlights', 'content': "Software developer/Programmer/Software engineer. Joined July 2015. 405 Following · 2,936 Followers · Posts · Repli es · Highlights · Media. Eden Marco's"}, {'url': 'https://twitter.com/EdenEmarco177/status/1874333150459158787', 'content': 'Log in · Sign up. Conversatio n. Eden Marco · @EdenEmarco177. Damn! This is. 5:53 AM · Jan 1, 2025. ·. 420. Views.'}, {'url': 'https://twitter.com/EdenEmarco177/status/1869089079188664 617', 'content': 'Dec 17, 2024 · Conversation. Eden Marco · @EdenEmarco177. Damn! I like it. I am all in in the LangStack. 6:35 PM · Dec 17, 2024.'}, {'ur l': 'https://twitter.com/EdenEmarco177/status/1864360381730132199', 'content': 'Eden Marco on X: "Its like copilot on steroids Though I haven\'t used copi lot in a while now and they did introduce multi file editing (where cursor shines)" / X Don’t miss what’s happening People on X are the first to know. Log in Sign up Post See new posts Eden Marco Its like copilot on steroids Though I haven\'t used copilot in a while now and they did introduce multi file edi ting (where cursor shines) New to X? Sign up now to get your own personalized timeline! Sign up with Apple By signing up, you agree to the Terms of Servic e and Privacy Policy, including Cookie Use. Something went wrong. Terms of ServicePrivacy PolicyCookie PolicyAccessibilityAds info © 2025 X Corp.'}]

I have found multiple references to Eden Marco's Twitter profile, and they all indicate the same username.

Final Answer: @EdenEmarco177

> Finished chain.
@EdenEmarco177

```

Once the agent's response is confirmed to be correct, we can integrate it into our main file `ice_breaker.py` for use.

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

from third_parties.linkedin import scrape_linkedin_profile
from third_parties.twitter import scrape_user_tweets # import scraping funtion of twitter
from agents.linkedin_lookup_agent import lookup as linkedin_lookup
from agents.twitter_lookup_agent import lookup as twitter_lookup # import the agent used to find the Twitter username

def ice_break_with(name: str):
    linkedin_url = linkedin_lookup(name=name)
    linkedin_data = scrape_linkedin_profile(linkedin_profile_url=linkedin_url)

    # get the Twitter username and scrape the user's tweets
    twitter_username = twitter_lookup(name=name)
    user_tweets = scrape_user_tweets(username=twitter_username)

    # modify the template to add the Twitter posts parameter `twitter_posts`
    summary_template = """
        given the information about a person from linkedin {information},
        and lastest twitter posts {twitter_posts}. I want you to create:
            1. a short summary
            2. two interesting facts about them
        
        Use both the linkedin information and the twitter posts to create the summary and the interesting facts."""
    #   force the LLM to answer the question with both the linkedin information and the twitter posts

    summary_prompt_template = PromptTemplate(
        input_variables=['information', 'twitter_posts'],
        #                               ^^^^^^^^^^^^^^^ one more input variable
        template=summary_template
    )

    llm = ChatOpenAI(temperature=0, model="gpt-4o-mini")

    chain = summary_prompt_template | llm | StrOutputParser()

    res = chain.invoke(input={
        "information": linkedin_data,
        "twitter_posts": user_tweets  # pass the user tweets because the prompt template inputs are now two
    })

    print(res)


if __name__ == '__main__':
    ice_break_with("Eden Marco")


```

Currently, ice_breaker.py not only crawls data from Linkedin, but also crawls the user's latest tweets from Twitter, and uses the data from both to pass to LLM to generate a brief summary. Let's test it out.

```sh
 python ice_breaker.py

/home/matt/.local/share/virtualenvs/ice_breaker-gEYUNqx7/lib/python3.13/site-packages/langsmith/client.py:253: LangSmithMissingAPIKeyWarning: API key must be provided when using hosted LangSmith API

  warnings.warn(


> Entering new AgentExecutor chain...

I need to find the LinkedIn profile page for someone named Eden Marco. To do this, I will use the tool to crawl Google for their LinkedIn profile page.  

Action: Crawl Google 4 linkedin profile page  

Action Input: Eden Marco

[{'url': 'https://www.linkedin.com/in/edenmarco', 'content': 'Eden Marco\nStrategy Analyst at Deloitte\nNew York, United States\ n1032 connections, 1036 followers\n\n\nAbout:\nN/A\n\n\nExperience:\nStrategy Analyst at Deloitte Digital (https://www.linkedin.com/company/deloitte-digit al)\nAug 2023 - Present\nNew York, New York, United States\n\n\nEducation:\nUNC Kenan-Flagler Business School\nBSBA, Business Administration\nN/A - Presen t\nGrade: N/A\nActivities and societies: N/A'}, {'url': 'https://www.linkedin.com/in/eden-marco', 'content': 'Eden Marco\nLLMs @ Google Cloud | Best-selli ng Udemy Instructor | Backend & GenAI | Opinions stated here are my own, not those of my company\nUnited States, United States of America\n4995 connection s, 6872 followers\n\n\nAbout:\nBackend developer --> Now an AI Engineer, Udemy.com best seller instructor\nOpinions stated here are my own, not those of m y company\n\n\nExperience:\nLLM Specialist, Customer Engineering - Google Cloud at Google (https://www.linkedin.com/company/google)\nJul 2023 - Present\n\ n\nEducation:\nTechnion - Israel Institute of Technology\nBachelor’s Degree, Computer Science\nJan 2015 - Dec 2019\nGrade: N/A\nActivities and societies: N/A'}, {'url': 'https://odsc.com/blog/speaker/eden-marco/', 'content': "Eden Marco | Open Data Science Conference Ai+ AI Agents East 2025 Training Track A I Agents East 2025 Training Track With years of experience in backend development, he currently works at Google as an LLM Specialist, assisting customers in implementing complex generative AI solutions on GCP using open-source frameworks like LangChain and Google's generative AI services. This two-hour sess ion is crafted for AI practitioners who already possess a background in Python and familiarity with LangChain, aiming to elevate their skills in developin g cutting-edge LLM agentic applications. By the end of this workshop, participants will be equipped with the expertise to leverage LangGraph by LangChain for developing sophisticated LLM agents, ready to tackle a diverse range of applications in production environments."}, {'url': 'https://www.researchgate. net/profile/Eden-Marco', 'content': 'Eden MARCO | University of Toronto, Toronto | U of T | Department of Medicine | Research profile Join ResearchGate to contact this researcher and connect with your scientific community. Join ResearchGate to contact this researcher and connect with your scientific communi ty. Research Research Join ResearchGate to find the people and research you need to help your work Only verified researchers can join ResearchGate and sen d messages to other members. University students and faculty, institute members, and independent researchers Not a researcher Sorry, you need to be a rese archer to join ResearchGate. Recruit researchers Tip: Most researchers use their institutional email address as their ResearchGate login Tip: Most researc hers use their institutional email address as their ResearchGate login'}, {'url': 'https://www.udemy.com/user/eden-marco/?srsltid=AfmBOoouQBxqEgbeFZJkB0ts uFzoyDVC71lmBSnSxjek-dPHbUojAs7z', 'content': 'I am a passionate Software Engineer with years of experience in back-end development, one of the first engi neers at Orca Security, and now I am working as a'}]

I have found multiple LinkedIn profiles for individuals named Eden Marco. I need to determine which one is the most relevant based on their current roles and locations. 

The first profile is for an Eden Marco who is a Strategy Analyst at Deloitte in New York. The second profile is for an Eden Marco who is an LLM Specialist at Google Cloud in the United States. 

Since both profiles are valid, I will provide the first one as it is more specific about the location and current role.

Final Answer: https://www.linkedin.com/in/edenmarco

> Finished chain.
/home/matt/.local/share/virtualenvs/ice_breaker-gEYUNqx7/lib/python3.13/site-packages/langsmith/client.py:253: LangSmithMissingAPIKeyWarning: API key must be provided when using hosted LangSmith API

warnings.warn(


> Entering new AgentExecutor chain...

I need to find the Twitter username for the person named Eden Marco. To do this, I will search for their Twitter profile using Google.

Action: Crawl Google 4 Twitter username  

Action Input: "Eden Marco Twitter"

[{'url': 'https://twitter.com/EdenEmarco177/status/1862167325799039295', 'content': "Nov 28, 2024 · Eden Marco · @Eden Emarco177. Really impressed with Cursor's latest agent features! But I really miss that composer CMD+SHIFT+I shortcut - it"}, {'url': 'https://twitter.com /EdenEmarco177/highlights', 'content': "Software developer/Programmer/Software engineer. Joined July 2015. 405 Following · 2,936 Followers · Posts · Repli es · Highlights · Media. Eden Marco's"}, {'url': 'https://twitter.com/EdenEmarco177/status/1874333150459158787', 'content': 'Log in · Sign up. Conversatio n. Eden Marco · @EdenEmarco177. Damn! This is. 5:53 AM · Jan 1, 2025. ·. 420. Views.'}, {'url': 'https://twitter.com/EdenEmarco177/status/1869089079188664 617', 'content': 'Dec 17, 2024 · Conversation. Eden Marco · @EdenEmarco177. Damn! I like it. I am all in in the LangStack. 6:35 PM · Dec 17, 2024.'}, {'ur l': 'https://twitter.com/EdenEmarco177/status/1864360381730132199', 'content': 'Eden Marco on X: "Its like copilot on steroids Though I haven\'t used copi lot in a while now and they did introduce multi file editing (where cursor shines)" / X Don’t miss what’s happening People on X are the first to know. Log in Sign up Post See new posts Eden Marco Its like copilot on steroids Though I haven\'t used copilot in a while now and they did introduce multi file edi ting (where cursor shines) New to X? Sign up now to get your own personalized timeline! Sign up with Apple By signing up, you agree to the Terms of Servic e and Privacy Policy, including Cookie Use. Something went wrong. Terms of ServicePrivacy PolicyCookie PolicyAccessibilityAds info © 2025 X Corp.'}]

I have found multiple references to Eden Marco's Twitter profile, and they all indicate the same username.

Final Answer: @EdenEmarco177

> Finished chain.

### Summary
Eden Marco is an AI Engineer and LLM Specialist at Google Cloud, with a strong background in backend development. He is also a best-selling instructor on 
Udemy, where he has produced five popular courses that have attracted over 100,000 students. Eden is passionate about AI and cloud computing, as evidenced
 by his recent Twitter posts discussing the security risks of deploying LLM agents and the benefits of using contextual models to reduce hallucinations in
 AI outputs. With a Bachelor's degree in Computer Science from the Technion - Israel Institute of Technology, he has accumulated extensive experience in v
arious software engineering roles.

### Interesting Facts
1. Eden has created five best-selling courses on Udemy, amassing over 100,000 enrolled students and achieving a solid average rating of 4.7 stars from mor
e than 22,000 ratings.
2. In a recent Twitter demo, he highlighted the alarming security risks associated with deploying LLM agents in the cloud without proper security measures
, showcasing his commitment to promoting safe AI practices.

```



