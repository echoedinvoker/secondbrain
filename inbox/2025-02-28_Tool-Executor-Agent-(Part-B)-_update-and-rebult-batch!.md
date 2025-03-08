---
date: 2025-02-28
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Tool Executor Agent (Part B) _update and rebult batch!

In [[2025-02-28_Tool-Executor-Agent-(Part-B)-_update!|Tool Executor Agent (Part B) _update!]] we have updated the codes of [[2025-02-28_Tool-Executor-Agent-(Part-B)|Tool Executor Agent (Part B)]] but loss the batch functionality. In this article, we will rebuild the batch functionality back.


```py
...

import concurrent.futures # import this for creating threading pool


load_dotenv()


search = TavilySearchAPIWrapper(tavily_api_key=os.environ["TAVILY_API_KEY"])
tavily_tool = TavilySearchResults(api_wrapper=search, max_results=5)

def execute_tools(state: List[BaseMessage]) -> List[ToolMessage]:
    tool_invocation: AIMessage = state[-1]
    parsed_tool_calls = parser.invoke(tool_invocation)

    outputs = []

    for parsed_call in parsed_tool_calls:
        # for query in parsed_call["args"]["search_queries"]: # This is the old way of doing it
        #     search_results = tavily_tool.invoke(query)
        #     outputs.append(search_results)

        queries = parsed_call["args"]["search_queries"] # get all query strings

        with concurrent.futures.ThreadPoolExecutor() as executor: # create a thread pool

            # submit all queries to the thread pool
            future_to_query = {executor.submit(tavily_tool.invoke, query): query for query in queries}

            # get the results as they come in and append to outputs
            for future in concurrent.futures.as_completed(future_to_query):
                search_results = future.result()
                outputs.append(search_results)



if __name__ == "__main__":  ...

```

We'll see the same outputs as before but now the Tavily API calls are made concurrently. This will speed up the process of getting the search results.



