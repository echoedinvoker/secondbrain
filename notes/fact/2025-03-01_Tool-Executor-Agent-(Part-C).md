---
date: 2025-03-01
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Tool Executor Agent (Part C)

In this topic, we need to convert the results obtained from using Tavily search online in [[2025-02-28_Tool-Executor-Agent-(Part-B)-_update-and-rebult-batch!|the previous topic]] into a single ToolMessage to return, to meet the requirements of a node function.


## Convert the search results into a dictionary

But before converting it into a ToolMessage, we need to convert the result into a dictionary with a reasonable structure so that we can then convert the result into a ToolMessage.

File: /home/matt/Projects/reflexion-agent/tool_executor.py
```python
...

from collections import defaultdict # to create a dictionary which can create a new key if it does not exist automatically


load_dotenv()


search = TavilySearchAPIWrapper(tavily_api_key=os.environ["TAVILY_API_KEY"])
tavily_tool = TavilySearchResults(api_wrapper=search, max_results=5)

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:
    tool_invocation: AIMessage = state[-1]
    parsed_tool_calls = parser.invoke(tool_invocation)

    outputs = []

    # in order to return a single ToolMessage, we need to collect the ids and tool inputs for each output
    ids = []
    tool_inputs = []

    for parsed_call in parsed_tool_calls:
        queries = parsed_call["args"]["search_queries"]

        # because output got from the nested loop, we need to collect the ids and tool inputs in the same loop, so the number of outputs, ids, and tool inputs are the same
        for parsed_call in parsed_tool_calls:
            for query in parsed_call["args"]["search_queries"]:
                tool_inputs.append(query)
                ids.append(parsed_call["id"])

        with concurrent.futures.ThreadPoolExecutor() as executor:
            future_to_query = {executor.submit(tavily_tool.invoke, query): query for query in queries}
            for future in concurrent.futures.as_completed(future_to_query):
                search_results = future.result()
                outputs.append(search_results)

    # create a dictionary which can create a new key if it does not exist automatically
    ouputs_map = defaultdict(dict)

    # build the outputs map with the outputs, ids, and tool inputs
    for id_, output, tool_input in zip(ids, outputs, tool_inputs):
        ouputs_map[id_][tool_input] = output

    print(ouputs_map)

# defaultdict(
#     <class 'dict'>,
#     {
#         'call_KpYHichFFEmLitHFvFhKy1Ra': {
#             'AI-Powered SOC startups funding': [
#                 {
#                     'url': 'https://www.securityweek.com/radiant-snags-15-million-for-ai-powered-soc-technology/',
#                     'content': 'Radiant Security gets $15 million in new financing as ...'
#                 },
#                 {
#                     'url': 'https://www.maginative.com/article/ai-security-startup-conifers-ai-secures-25m-to-enhance-soc-operati ons-with-ai/',
#                     'content': 'AI Security Startup Conifers.ai Secures $25M to Enhance SOC Operations with ...'},
#                 {
#                     'url': 'https://www.chiefaioffice.xyz/p/6-ai-startups-raise-400m- 15ac',
#                     'content': '📌 7 AI Startups raise $400M+ 📌 7 AI Startups raise $400M+ 📌 7 AI Startups ...'
#                 },
#                 {
#                     'url': 'https://www.securityweek.com/conifers-ai-scores-25m-investment-for-agentic-ai-soc-technolog y/',
#                     'content': 'Backed by SYN Ventures, Conifers.ai plans to use “agentic AI” technology to ...'
#                 },
#                 {
#                     'url': 'https://aimresearch.co/ai-startups/andesite-secured-23 m-to-handle-the-soc-crisis-with-ai-driven-cybersecurity',
#                     'content': 'Andesite Secured $23M To Handle The SOC Crisis With AI-driven Cybersecurity. ...'
#                 }
#             ],
#             'AI SOC problem domain specifics': [...],
#             'Technologies used by AI-Powered SOC startups': [...]
#         }
#     }
# )

```

## Convert the dictionary into a ToolMessage

```py
...

import json  # import
from langchain_core.messages import AIMessage, BaseMessage, HumanMessage, ToolMessage
#                                                                         ^^^^^^^^^^^^ import ToolMessage

...

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:

    ...

    outputs_map = defaultdict(dict)

    for id_, output, tool_input in zip(ids, outputs, tool_inputs):
        outputs_map[id_][tool_input] = output

    tool_messages = [] # create an empty list to store the ToolMessages because it may contain multiple tool calls at once
                       # even in our case, we only have one tool call (only one id), but it is better to store it in a list for robustness
    
    for id_, mapped_outputs in outputs_map.items():
    #                          ^^^^^^^^^^^^^^^^^^^ turn this dictionary key-value pairs into a list of (key, value) tuples
        tool_messages.append(
            # convert the mapped outputs into a ToolMessage
            ToolMessage(
                content=json.dumps(mapped_outputs),
                #       ^^^^^^^^^^^^^^^^^^^^^^^^^^ mapped_outputs is a dictionary, so we need to convert it into a string so that we can store it in the content field
                tool_call_id=id_
            )
        )

    return tool_messages # return the list of ToolMessages which fit the requirements of the node function


# [
#     ToolMessage(
#         content='{
#             "AI-Powered SOC startups funding": [
#                 {
#                     "url": "https://www.forbes.com/councils/forbestechcouncil/2024/07/31/ai-powered-soc-a-new-intelligent-era-in-cybersecurity/",
#                     "content": "An SOC powered by AI supercharges threat detection and response efforts. AI-powered SOC ..."
#                 },
#                 {
#                     "url": "https://certpro.com/soc-2-compliance-ai-startups-usa/",
#                     "content": "Therefore, AI-driven startup businesses need SOC 2 certification i n USA to ensure data safety. Thus, ..."
#                 },
#                 {
#                     "url ": "https://blog.google/outreach-initiatives/entrepreneurs/growth-academy-ai-cybersecurity-cohort-2025/",
#                     "content": "Google Cloud Google Cloud Google Cloud Announcing the 2025 Google for Start ups Growth Academy: AI for ..."
#                 },
#                 {
#                     "url": "https://radiantsecurity.ai/",
#                     "content": "AI-Powered SOC Analyst | Radiant Security Guides for the AI-Driven SOC What Is a SOC Analyst? SOC Analysts ..."
#                 },
#                 {
#                     "url": "https://www.tractiontechnology.com/blog/the-traction-five-june-2024s-hottest-ai-security-startups-tech-trends",
#                     "content": "Top 5 AI Secur ity Startups & Emerging Tech Trends (June 2024) | Traction Technology The ..."
#                 }
#             ],
#             "AI SOC problem domain specifics": [...],
#             "Technologies used by AI-Powered SOC startups": [...]
#         }',
#         tool_call_id='call_KpYHichFFEmLitHFvFhKy1Ra'
#     )
# ]
