---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Your First LangChain application: Chaining a simple prompt


## First Simple Chain

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
from langchain_core.prompts import PromptTemplate # wrapper for the prompt template, it provides a way to insert variables into the prompt template
from langchain_openai import ChatOpenAI # wrapper for the OpenAI API to integrate with LangChain, it'll use the API KEY from environment variables automatically

# copy the information about Elon Musk from the wikipedia page
information = """
Elon Reeve Musk (/ˈiːlɒn mʌsk/; born June 28, 1971) is a businessman and U.S. special government employee, best known for his key roles in Tesla, Inc. and SpaceX, and his ownership of Twitter. Musk is the wealthiest individual in the world; as of February 2025, Forbes estimates his net worth to be US$397 billion.

A member of the wealthy South African Musk family, Musk was born in Pretoria before immigrating to Canada, acquiring its citizenship. He moved to California in 1995 to attend Stanford University, and with his brother Kimbal co-founded the software company Zip2, that was later acquired by Compaq in 1999. That same year, Musk co-founded X.com, a direct bank, that later formed PayPal. In 2002, Musk acquired U.S. citizenship, and eBay acquired PayPal. Using the money he made from the sale, Musk founded SpaceX, a spaceflight services company, in 2002. In 2004, Musk was an early investor in electric vehicle manufacturer Tesla and became its chairman and later CEO. In 2018, the U.S. Securities and Exchange Commission (SEC) sued Musk, alleging he falsely announced that he had secured funding for a private takeover of Tesla, stepped down as chairman, and paid a fine. In 2022, he acquired Twitter, and rebranded the service as X the following year.

His political activities and views have made him a polarizing figure. He has been criticized for making unscientific and misleading statements, including COVID-19 misinformation, affirming antisemitic and transphobic comments, and promoting conspiracy theories. His acquisition of Twitter was controversial due to an increase in hate speech and the spread of misinformation on the service. Musk has engaged in political activities in several countries, including as a vocal and financial supporter of U.S. president Donald Trump. He was the largest donor in the 2024 United States presidential election, and is a supporter of far-right activists, causes, and political parties. In January 2025, Musk was appointed leader of Trump's newly restructured Department of Government Efficiency.
"""

if __name__ == '__main__':

    # raw prompt template, not wrapped in the PromptTemplate class yet
    # we insert the variable {information} into the prompt template, it'll be replaced with the actual information with PromptTemplate
    summary_template = """
        given the information {information} about a person from I want you to create:
            1. a short summary
            2. two interesting facts about them
    """

    # wrap the prompt template in the PromptTemplate class
    summary_prompt_template = PromptTemplate(input_variables=['information'], template=summary_template)
    #                                                         ^^^^^^^^^^^^^ list all parameters that are used in the prompt template here

    # create llm object
    llm = ChatOpenAI(temperature=0, model="gpt-3.5-turbo")
    #                            ^ means no any creative responses

    # group the prompt template and the llm object together to create a chain
    chain = summary_prompt_template | llm

    # use chain with `invoke` method to run the chain
    res = chain.invoke(input={"information": information})
    #                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ need to pass all the parameters that PromptTemplate needs

    print(res)
```

Let's run the script and see the output:

```bash
 python ice_breaker.py
content='1. Elon Musk is a billionaire businessman and U.S. special government employee known for his roles in Tesla, Inc., SpaceX, and Twitter. He is the wealthiest individual in the worl
h a net worth of US$397 billion. Musk has been involved in various ventures in the tech and space industries, and has also been a controversial figure due to his political activities and v
\n\n2. Two interesting facts about Elon Musk:\n- Musk was born in South Africa and later acquired Canadian and U.S. citizenship.\n- He co-founded the online payment system PayPal and later
ded SpaceX, a spaceflight services company.' additional_kwargs={'refusal': None} response_metadata={'token_usage': {'completion_tokens': 123, 'prompt_tokens': 491, 'total_tokens': 614, 'co
ion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_toke
0}}, 'model_name': 'gpt-3.5-turbo-0125', 'system_fingerprint': None, 'finish_reason': 'stop', 'logprobs': None} id='run-338e14ac-7598-41f3-8ac0-a13a89592912-0' usage_metadata={'input_token
91, 'output_tokens': 123, 'total_tokens': 614, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}}

```


## Output Parser

We can refer `content` by ourself, but there is object `StrOutputParser` that can help us to extract the content from the response and do some formatting.


```py
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser  # import 

information = """
Elon Reeve Musk (/ˈiːlɒn mʌsk/; born June 28, 1971) is a businessman and U.S. special government employee, best known for his key roles in Tesla, Inc. and SpaceX, and his ownership of Twitter. Musk is the wealthiest individual in the world; as of February 2025, Forbes estimates his net worth to be US$397 billion.

A member of the wealthy South African Musk family, Musk was born in Pretoria before immigrating to Canada, acquiring its citizenship. He moved to California in 1995 to attend Stanford University, and with his brother Kimbal co-founded the software company Zip2, that was later acquired by Compaq in 1999. That same year, Musk co-founded X.com, a direct bank, that later formed PayPal. In 2002, Musk acquired U.S. citizenship, and eBay acquired PayPal. Using the money he made from the sale, Musk founded SpaceX, a spaceflight services company, in 2002. In 2004, Musk was an early investor in electric vehicle manufacturer Tesla and became its chairman and later CEO. In 2018, the U.S. Securities and Exchange Commission (SEC) sued Musk, alleging he falsely announced that he had secured funding for a private takeover of Tesla, stepped down as chairman, and paid a fine. In 2022, he acquired Twitter, and rebranded the service as X the following year.

His political activities and views have made him a polarizing figure. He has been criticized for making unscientific and misleading statements, including COVID-19 misinformation, affirming antisemitic and transphobic comments, and promoting conspiracy theories. His acquisition of Twitter was controversial due to an increase in hate speech and the spread of misinformation on the service. Musk has engaged in political activities in several countries, including as a vocal and financial supporter of U.S. president Donald Trump. He was the largest donor in the 2024 United States presidential election, and is a supporter of far-right activists, causes, and political parties. In January 2025, Musk was appointed leader of Trump's newly restructured Department of Government Efficiency.
"""

if __name__ == '__main__':
    summary_template = """
        given the information {information} about a person from I want you to create:
            1. a short summary
            2. two interesting facts about them
    """

    summary_prompt_template = PromptTemplate(input_variables=['information'], template=summary_template)
    llm = ChatOpenAI(temperature=0, model="gpt-3.5-turbo")

    chain = summary_prompt_template | llm | StrOutputParser()
    #                                     ^^^^^^^^^^^^^^^^^^^
    #                                     it'll handle the response from the llm object (extract the content from the response and do some formatting)

    res = chain.invoke(input={"information": information})

    print(res)
```

Let's run the script and see the output:

```bash
$ python ice_breaker.py
1. Elon Musk is a billionaire businessman and U.S. special government employee know
n for his roles in Tesla, Inc., SpaceX, and Twitter. He is the wealthiest individua
l in the world with a net worth of US$397 billion.

2. Two interesting facts about Elon Musk:
- He was born in South Africa and later acquired Canadian and U.S. citizenship.
- Musk co-founded PayPal and SpaceX, and later acquired Twitter, making him a key p
layer in the tech and space industries.
```



