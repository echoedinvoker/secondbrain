---
date: 2025-02-12
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Optional: Twitter Data Processing Part1: Scraping

This part is very similar to the [[2025-02-11_Integrating-Linkedin-Data-Processing-Part-1-Scraping|previous topic]], but the scraping target has changed from LinkedIn to Twitter, so we will use the Twitter API to crawl data.

The Twitter API is now charging a fee, so we will only use Gist to simulate the response of the Twitter API. We will use comments to indicate the parts that would have used the Twitter API.

In order to use the Twitter API in Python, we usually use Tweepy, which is a wrapper for the Twitter API that allows us to use the Twitter API more conveniently.

```sh
pipenv install tweepy
```

Then, we create anothe file `twitter.py` in the `third_parties` module:

```sh
 tree
.
├── agents
│   ├── __init__.py
│   └── linkedin_lookup_agent.py
├── ice_breaker.py
├── LICENSE
├── Pipfile
├── Pipfile.lock
├── README.md
├── third_parties
│   ├── __init__.py
│   ├── linkedin.py
│   └── twitter.py  # create this file
└── tools
    ├── __init__.py
    └── tools.py
```

File: /home/matt/Github/ice_breaker/third_parties/twitter.py
```python
import requests
# import tweepy

# twitter_client = tweepy.Client(
    # this client needs some environment variables to be set:
    # TWITTER_BEARER_TOKEN, TWITTER_API_KEY, TWITTER_API_KEY_SECRET,
    # TWITTER_ACCESS_TOKEN, TWITTER_ACCESS_TOKEN_SECRET
# )
def scrape_user_tweets(username, num_tweets=5, mock: bool = True):
    """
    Scraps a Twitter user's original tweets (i.e., not retweets or replies) and returns them as a list of dictionaries. Each dictionary has three fields: "time_posted" (relative to now), "text", and "url".
    """
    tweet_list = []

    # if mock:
    EDEN_TWITTER_GIST = "https://gist.githubusercontent.com/emarco177/827323bb599553d0f0e662da07b9ff68/raw/57bf38cf8acce0c87e060f9bb51f6ab72098fbd6/eden-marco-twitter.json"
    tweets = requests.get(EDEN_TWITTER_GIST, timeout=5).json()

    # else:
    #     user_id = twitter_client.get_user(username=username).data.id
    #     tweets = twitter_client.get_users_tweets(
    #         id=user_id,
    #         max_results=num_tweets,
    #         exclude=["retweets", "replies"],
    #     )

    for tweet in tweets:
        # recreate the dictionary with only the fields we want
        tweet_dict = {}
        tweet_dict["text"] = tweet["text"]
        tweet_dict["url"] = f"https://twitter.com/{username}/status/{tweet['id']}"
        # append the recreated dictionary to the list
        tweet_list.append(tweet_dict)

    return tweet_list # return the list of recreated dictionaries


# test purpose
if __name__ == "__main__":
    print(scrape_user_tweets("emarco177", num_tweets=5, mock=True))
```

Test the function in the terminal:

```sh
 python third_parties/twitter.py
[
  {
    'text': '🚨 Demo Alert: risks when deploying insecure LLM agents 🤖\n(Spoiler: total environment compromise!)🚨\n\nIn a recent demo, I showcased what happens when deploying an LLM Agent to th
e cloud without taking basic security measures.\nThe outcome was alarming☠️\nan exploitable… https://t.co/OKoxiOUFGQ https://t.co/DPi8aVcPls',
    'url': 'https://twitter.com/emarco177/status/1767277531537744100'
  },
  {
    'text': 'Contextual Answers from the @AI21Labs team is a fantastic model when you want the LLM to always stay within the provided context.\nThe main benefits for using this kind of model are:\n\n1.  Significant reduction in hallucinations, enhancing accuracy and reliability.\n\n2.  Improved… https://t.co/rqTuHdvOMU https://t.co/6OUIHorMrf',
    'url': 'https://twitter.com/emarco177/status/1766014021033972000'
  }
]
```

