---
date: 2025-02-08
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Building a Custom SQL History

Use the concept explained in the [[2025-02-08_Custom-History-Objects|previous topic]] to create a custom history object for sqlite in the course video.

But I'll use `LangGraph` to create a workflow instead of using the legacy `LangChain` chain, so I'll create a custom `memory saver` for the workflow in this topic.

File: /home/matt/Projects/pdf/app/chat/memories/sql_memory.py
```python
from langchain_core.runnables import RunnableConfig
from langgraph.checkpoint.base import BaseCheckpointSaver

class CustomMemorySaver(BaseCheckpointSaver):
    def __init__(self, add_message_by_conversation_id, get_messages_by_conversation_id):
        self.add_message_by_conversation_id = add_message_by_conversation_id  # This is a function that adds a message to the memory, provided by app.web module
        self.get_messages_by_conversation_id = get_messages_by_conversation_id # This is a function that gets messages from the memory, provided by app.web module

    # Rewrite the put method to add the message to the memory when graph has any new message
    def put(self, config: RunnableConfig, checkpoint, metadata, new_versions):
        # Get the conversation_id from the config that passed to the graph when invoking the graph
        conversation_id = config.get("configurable", {}).get("conversation_id")
        message = checkpoint["channel_values"].get("message") # `checkpoint` is the current snapshot of the graph, contains inputs and outputs of each node
                                                              # so we can get the message from the checkpoint
        if message and conversation_id:
            self.add_message_by_conversation_id(conversation_id, message)
        
        return config # to fit the return type of the put method

    # Rewrite the get method to get the messages from the memory when graph is invoked
    def get(self, config: RunnableConfig):
        # Get the conversation_id from the config that passed to the graph when invoking the graph
        conversation_id = config.get("configurable", {}).get("conversation_id")
        messages = self.get_messages_by_conversation_id(conversation_id)
        return messages
```

Above, we created a custom memory saver that persists the messages in the sqlite database through app.web module, we need to intantiate it and pass it to the graph workflow as below:

```py
# Instantiate the custom memory saver with the functions that provided by app.web module
memory_saver = CustomMemorySaver(
    web_module.add_message_by_conversation_id,
    web_module.get_messages_by_conversation_id
)

# Compile the graph with the custom memory saver, so that the graph can use it to save and get the messages automatically
graph = graph_builder.compile(checkpointer=memory_saver)
```

Because the conversation_id is passed from config to memory saver, we need to write the conversation_id into the config when invoking the graph and send it to the graph.

```py
config = {
    "configurable": {
        "conversation_id": "123456"
        #                  ^^^^^^^^ from app.web module
    }
}

result = graph.invoke(input_data, config=config)
#                                 ^^^^^^^^^^^^^^ send the config to the graph

```

