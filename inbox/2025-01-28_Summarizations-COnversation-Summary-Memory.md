---
date: 2025-01-28
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Summarizations COnversation Summary Memory

The course uses another memory class of Chain called `ConversationSummaryMemory`, which will organize question and answer summaries into system messages during each response, in order to reduce the accumulation of prompt tokens in the conversation.

But in LangGraph, we need to write the logic for `ConversationSummaryMemory` ourselves, and add a new node in `LangGraph` to use it.

In [[2025-01-27_Saving-and-Extending-Conversations|Saving and Extending Conversations]], we used `MessagesState` to store the conversation history. But it has some uncontrollable things. For more customization purposes, we can define our own state schema and use it in the `LangGraph` workflow.

```py
...

CONVERSATION_FILE = "conversation_history.json"

parser = argparse.ArgumentParser()
parser.add_argument("--id")
args = parser.parse_args()

def save_messages(thread_id: str, messages: List[BaseMessage]): ...

def load_messages(thread_id: str) -> List[BaseMessage]: ...

chat_model = ChatOpenAI(...)

# define the state schema on our own
class SummaryState(TypedDict):
    messages: List[BaseMessage]

workflow = StateGraph(state_schema=SummaryState)

def call_model(state: SummaryState):
    response = chat_model.invoke(state["messages"])
    return {"messages": state["messages"] + [response]}
    #                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    #                   in custom state schema, we need to combine the old messages with the new response on our own

def summarize_conversation(state: Dict[str, Any]):
    messages = state.get("messages", [])
    
    if len(messages) < 4:
        return state

    summary_prompt = [
        SystemMessage(content="You are an assistant that is good at summarizing conversations."),
        HumanMessage(content=f"Please summarize the key points of the following conversation:\n{' '.join([m.content for m in messages])}")
    ]

    summary_response = chat_model.invoke(summary_prompt)
    
    return {
        "messages": [
            SystemMessage(content=f"Conversation Summary: {summary_response.content}"),
            messages[-2],
            messages[-1] 
        ],
    }

workflow.add_edge(START, "model")
workflow.add_edge("model", "summarize")
workflow.add_node("model", call_model)
workflow.add_node("summarize", summarize_conversation)

memory = MemorySaver()
app = workflow.compile(checkpointer=memory)

thread_id = args.id or uuid.uuid4()

config = RunnableConfig(...)


initial_messages = load_messages(str(thread_id))

result = {"messages": initial_messages}

if initial_messages:
    print("resuming conversation...")
    result = app.invoke(
        {"messages": initial_messages}, 
        config=config
    )
    print("last message:", result["messages"][-1].content)

while True:
    try:
        content = input(">> ")
        
        result = app.invoke(
            {"messages": result.get("messages", []) + [HumanMessage(content=content)]},
            #            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
            #            in custom state schema, we need to combine the old messages with the new human message on our own
            config=config
        )
        
        ai_response = result["messages"][-1].content
        print(ai_response)
        
        save_messages(str(thread_id), result["messages"])
        
    except KeyboardInterrupt:
        print("exiting...")
```

Let's try to run the code above:

```sh 
$ python main.py --id 'conversation-1'
>> 1 + 1
1 + 1 equals 2.

>> add 2 more
If you add 2 more to 1 + 1, you get:

1 + 1 + 2 = 4.
>> add 3 more
If you add 3 more to the previous total of 4, you get:

4 + 3 = 7.
>> 
```

Let's check json file:
```json
{
  "conversation-1": [
    {
      "role": "system",
      "content": "Conversation Summary: The conversation revolves around a basic arithmetic problem, starting with the equation 1 + 1, which equals 2. Then, it adds 2 to this total, resulting in 1 + 1 + 2 = 4. Following that, it adds 3 to the total of 4, yielding 4 + 3 = 7."
    },
    {
      "role": "human",
      "content": "add 3 more"
    },
    {
      "role": "ai",
      "content": "If you add 3 more to the previous total of 4, you get:\n\n4 + 3 = 7."
    }
  ]
}
```

You can see that no matter how many messages are in the conversation, the system will summarize the conversation to the system message and only left the last two messages in the conversation.



