---
date: 2025-01-27
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Implementing a Chat Chain

```py
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

chat_model = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7
)

prompt_template = ChatPromptTemplate.from_messages([
    ("human", "{content}"),
])

chain = prompt_template | chat_model

while True:
    content = input(">> ")
    response = chain.invoke({
        "content": content
    })
    print(response)

```

Let's try to use above script to chat with the AI model.

```bash
$ python main.py

>> 5 + 2 =
content='5 + 2 = 7.' additional_kwargs={'refusal': None} response_metadata={'tok
en_usage': {'completion_tokens': 9, 'prompt_tokens': 12, 'total_tokens': 21, 'co
mpletion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, '
reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details':
 {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18
', 'system_fingerprint': 'fp_72ed7ab54c', 'finish_reason': 'stop', 'logprobs': N
one} id='run-d7a4fe4c-d79a-4104-8ccc-80b5f4a86aa4-0' usage_metadata={'input_toke
ns': 12, 'output_tokens': 9, 'total_tokens': 21, 'input_token_details': {'audio'
: 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}}

>> add 3 more 
content='Sure! However, I need more context to understand what you would like me
 to add three more of. Could you please provide more details?' additional_kwargs
={'refusal': None} response_metadata={'token_usage': {'completion_tokens': 29, '
prompt_tokens': 11, 'total_tokens': 40, 'completion_tokens_details': {'accepted_
prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_predi
ction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens':
 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_72ed7ab5
4c', 'finish_reason': 'stop', 'logprobs': None} id='run-8baf0bab-451b-4928-acb6-
f5008ceedf2b-0' usage_metadata={'input_tokens': 11, 'output_tokens': 29, 'total_
tokens': 40, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token
_details': {'audio': 0, 'reasoning': 0}}
>> 
```

In the second response of AI, we can see that it does not know the content of the first question and answer, because we did not pass the previous answer to the next question. This way, AI cannot know what the previous question was, so it cannot achieve a real conversation.

We'll sovle this problem in next topic.



