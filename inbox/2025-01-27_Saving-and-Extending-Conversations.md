---
date: 2025-01-27
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Saving and Extending Conversations

The course uses the method `chat_memory=FileChatMessageHistory()` in `ConversationBufferMemory` to save conversation records to json, and is able to read these conversation records every time the app is opened.

However, because the modern version has switched to using LangGraph to handle complex workflows, the class `ConversationBufferMemory` does not exist in the app. We must write our own methods to save/load messages to JSON.

```py
import uuid
import json
import os
import argparse
from typing import List
from langchain_core.runnables import RunnableConfig
from langchain_openai import ChatOpenAI
from langgraph.checkpoint.memory import MemorySaver
from langgraph.graph import START, MessagesState, StateGraph
from langchain_core.messages import BaseMessage, HumanMessage, AIMessage

# define the file to save the conversation history
CONVERSATION_FILE = "conversation_history.json"

# parse the thread id from the command line
parser = argparse.ArgumentParser()
parser.add_argument("--id")
args = parser.parse_args()

# write the functions to load messages to JSON on our own
def save_messages(thread_id: str, messages: List[BaseMessage]):
    serializable_messages = [
        {
            "role": msg.type, 
            "content": msg.content
        } for msg in messages
    ]
    
    conversations = {}
    if os.path.exists(CONVERSATION_FILE):
        with open(CONVERSATION_FILE, 'r') as f:
            conversations = json.load(f)
    
    conversations[str(thread_id)] = serializable_messages
    
    with open(CONVERSATION_FILE, 'w') as f:
        json.dump(conversations, f, indent=2)

# write the functions to load messages to JSON on our own
def load_messages(thread_id: str) -> List[BaseMessage]:
    if not os.path.exists(CONVERSATION_FILE):
        return []
    
    with open(CONVERSATION_FILE, 'r') as f:
        conversations = json.load(f)
    
    thread_id_str = str(thread_id)
    if thread_id_str not in conversations:
        return []
    
    messages = []
    for msg_data in conversations[thread_id_str]:
        if msg_data['role'] == 'human':
            messages.append(HumanMessage(content=msg_data['content']))
        elif msg_data['role'] == 'ai':
            messages.append(AIMessage(content=msg_data['content']))
    
    return messages

chat_model = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7
)

workflow = StateGraph(state_schema=MessagesState)

def call_model(state: MessagesState):
    response = chat_model.invoke(state["messages"])
    return {"messages": response}

workflow.add_edge(START, "model")
workflow.add_node("model", call_model)

memory = MemorySaver()
app = workflow.compile(checkpointer=memory)

thread_id = args.id or uuid.uuid4() # generate a new thread id if not provided

config = RunnableConfig(
    configurable={
        "thread_id": thread_id
    }
)

initial_messages = load_messages(str(thread_id)) # load the conversation from json when the app starts
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
            {"messages": [{"role": "human", "content": content}]}, 
            config=config
        )
        
        ai_response = result["messages"][-1].content
        print(ai_response)
        
        save_messages(str(thread_id), result["messages"]) # every time we get a response, save the conversation to json
        
    except KeyboardInterrupt:
        print("exiting...")
```

Let's run the script and see how it works:

```sh
$ python main.py --id 'conversation-1'
>> 2 + 3 =     
2 + 3 = 5.
```

Quit the app, now the json content is:

/home/matt/Projects/pycode/conversation_history.json
```json
{
  "conversation-1": [
    {
      "role": "human",
      "content": "2 + 3 ="
    },
    {
      "role": "ai",
      "content": "2 + 3 = 5."
    }
  ]
}
```

Restart the app:
```sh
$ python main.py --id 'conversation-1'
resuming conversation...
last message: 2 + 3 = 5.
>> add 5 more
If you add 5 more to 5, you get:

5 + 5 = 10.
```

Quit the app, now the json content is:
```json
{
  "conversation-1": [
    {
      "role": "human",
      "content": "2 + 3 ="
    },
    {
      "role": "ai",
      "content": "2 + 3 = 5."
    },
    {
      "role": "ai",
      "content": "2 + 3 = 5."
    },
    {
      "role": "human",
      "content": "add 5 more"
    },
    {
      "role": "ai",
      "content": "If you add 5 more to 5, you get:\n\n5 + 5 = 10."
    }
  ]
}
```

