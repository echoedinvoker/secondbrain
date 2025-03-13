---
date: 2025-01-29
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Building a Retrieval Chain

To resolve the issue of [[2025-01-28_Introducing-ChromaDB|Introducing ChromaDB]] is easy, just split the `main.py` to two files, one for inserting data and the other for querying the most similar data from the database.

```python
# main.py
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma

loader = TextLoader("facts.txt")
text_splitter_recursive = RecursiveCharacterTextSplitter(
    chunk_size=100,
    chunk_overlap=20
)
embeddings = OpenAIEmbeddings(model="text-embedding-3-large")

documents = loader.load()

docs = text_splitter_recursive.split_documents(documents)

db = Chroma.from_documents(
    docs,
    embeddings,
    persist_directory="emb"
)

# remove querying codes

# results = db.similarity_search(
#     "Tell me an interesting fact about the English language.",
#     k=2
# )
#
# print(results)
```
Create a new file named `prompt.py` and copy the querying codes to it.

```python
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma

embeddings = OpenAIEmbeddings(model="text-embedding-3-large")

db = Chroma(
#    ^^^^^^ directly create a Chroma instance with Chroma() instead of Chroma.from_documents()
    embedding_function=embeddings, # need to use argument name `embedding_function` to pass the embeddings here, don't be confused
    persist_directory="emb" # it'll take the embeddings from the directory (SQLite database) we've created in the main.py
)

# move the querying codes here
results = db.similarity_search(
    "Tell me an interesting fact about the English language.",
    k=2
)

print(results)
```

Let's try to run the above code.

```sh
$ python main.py # to insert the data and create the embeddings

$ python prompt.py # to query the most similar data

[Document(id='1a364f04-5c5e-4f10-9782-3a47a408c5ec', metadata={'source': 'facts.txt'}, page_content="5. The longest word in the English language is 'pneum
onoultramicroscopicsilicovolcanoconiosis.'"), Document(id='5d527eb8-b95e-410d-b7e1-1667f11888e0', metadata={'source': 'facts.txt'}, page_content='1. "Drea
mt" is the only English word that ends with the letters "mt."')]
```

Now we can reuse `prompt.py` to query the most similar documents and avoid the problem of repeatedly entering data into ChromaDB.

However, the work does not end here. We must use the documents queried from ChromaDB and user input to construct a complete prompt to be sent to LLM.

![retrieve-qa.png](../assets/imgs/retrieve-qa.png)

```python
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.runnables import RunnableParallel, RunnablePassthrough
from langchain_core.output_parsers import StrOutputParser
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--query")
args = parser.parse_args()

# format the retrieved documents to be more verbose
def verbose_format_docs(docs):
    print("\n=== Retrieved Documents ===")
    for i, doc in enumerate(docs, 1):
        print(f"Document {i}:")
        print(doc.page_content)
        print("-" * 50)
    return "\n\n".join(doc.page_content for doc in docs)

# create a traceable runnable for debugging
def create_traceable_runnable(runnable, name):
    def wrapper(input):
        print(f"\n=== {name} Input ===")
        print(input)
        output = runnable(input)
        print(f"\n=== {name} Output ===")
        print(output)
        return output
    return wrapper

embeddings = OpenAIEmbeddings(model="text-embedding-3-large")
llm = ChatOpenAI(model="gpt-4o-mini")

db = Chroma(
    embedding_function=embeddings,
    persist_directory="emb"
)

retriever = db.as_retriever() # convert ChromaDB to a retriever, which can be used in the RAG pipeline as a runnable

prompt = ChatPromptTemplate.from_messages([
    ("system", """You are an assistant, use the context retrieved below to answer the questions. If you don't know the answer, honestly say you don't know. Try to answer concisely, using up to three sentences.

    Context: {context}"""),
    ("human", "{question}")
])

# create a runnable chain to execute the full RAG pipeline
rag_chain = (
    RunnableParallel(
#   ^^^^^^^^^^^^^^^^ run multiple Runnables in parallel and collect their outputs to dictionary
        {
            "context": create_traceable_runnable(
                lambda x: verbose_format_docs(retriever.invoke(x)),
                "Document Retrieval and Formatting"
            ),
            "question": RunnablePassthrough()
            #           ^^^^^^^^^^^^^^^^^^^ used to create a Runnable that simply passes the input through to the output
        }
    )
    | create_traceable_runnable(prompt.invoke, "Prompt Formatting")
    | create_traceable_runnable(llm.invoke, "LLM Generation")
    | create_traceable_runnable(StrOutputParser().parse, "Output Parsing")
)

# run the full RAG chain
try:
    print("\n=== Full RAG Chain Execution ===")
    response = rag_chain.invoke(args.query)
    
    print("\n=== Final Response ===")
    print(response)

except Exception as e:
    print(f"An error occurred: {e}")

```
```sh
$ python prompt.py --query 'tell me a interesting fact about English language.'

=== Full RAG Chain Execution ===

=== Document Retrieval and Formatting Input ===
tell me a interesting fact about English language.

=== Retrieved Documents ===
Document 1:
5. The longest word in the English language is 'pneumonoultramicroscopicsilicovolcanoconiosis.'
--------------------------------------------------
Document 2:
1. "Dreamt" is the only English word that ends with the letters "mt."
--------------------------------------------------
Document 3:
90. The modern Italian language originated from the region of Tuscany.
--------------------------------------------------
Document 4:
87. The dot over an 'i' or 'j' is called a tittle.
88. A group of owls is called a parliament.
--------------------------------------------------

=== Document Retrieval and Formatting Output ===
5. The longest word in the English language is 'pneumonoultramicroscopicsilicovolcanoconiosis.'

1. "Dreamt" is the only English word that ends with the letters "mt."

90. The modern Italian language originated from the region of Tuscany.

87. The dot over an 'i' or 'j' is called a tittle.
88. A group of owls is called a parliament.

=== Prompt Formatting Input ===
{
  'context': '5. The longest word in the English language is \'pneumonoultramicroscopicsilicovolcanoconiosis.\'\n\n1. "Dreamt" is the only English word tha
t ends with the letters "mt."\n\n90. The modern Italian language originated from the region of Tuscany.\n\n87. The dot over an \'i\' or \'j\' is called a 
tittle.\n88. A group of owls is called a parliament.',
  'question': 'tell me a interesting fact about English language.'
}

=== Prompt Formatting Output ===
messages=[
  SystemMessage(
    content='You are an assistant, use the context retrieved below to answer the questions. If you don\'t know the answer, honestly say you don\'t know. Try to answer concisely, using up to three sentences.\n\n    Context: 5. The longest word in the English language is \'pneumonoultramicroscopicsilicovolcanoconiosis.\'\n\n1. "Dreamt" is the only English word that ends with the letters "mt."\n\n90. The modern Italian language originated from the region of Tuscany.\n\n87. The dot over an \'i\' or \'j\' is called a tittle.\n88. A group of owls is called a parliament.',
    additional_kwargs={},
    response_metadata={}
  ),
  HumanMessage(content='tell me a interesting fact about English language.', additional_kwargs={}, response_metadata={})
]

=== LLM Generation Input ===
messages=[
  SystemMessage(
    content='You are an assistant, use the context retrieved below to answer the questions. If you don\'t know the answer, honestly say you don\'t know. Try to answer concisely, using up to three sentences.\n\n    Context: 5. The longest word in the English language is \'pneumonoultramicroscopicsilicovolcanoconiosis.\'\n\n1. "Dreamt" is the only English word that ends with the letters "mt."\n\n90. The modern Italian language originated from the region of Tuscany.\n\n87. The dot over an \'i\' or \'j\' is called a tittle.\n88. A group of owls is called a parliament.',
    additional_kwargs={},
    response_metadata={}
  ),
  HumanMessage(content='tell me a interesting fact about English language.', additional_kwargs={}, response_metadata={})
]

=== LLM Generation Output ===
content='An interesting fact about the English language is that "Dreamt" is the only English word that ends with the letters "mt."'
additional_kwargs={'refusal': None}
response_metadata={
  'token_usage':
    {
      'completion_tokens': 27,
      'prompt_tokens': 155,
      'total_tokens': 182,
      'completion_tokens_details': {
        'accepted_prediction_tokens': 0,
        'audio_tokens': 0,
        'reasoning_tokens': 0,
        'rejected_prediction_tokens': 0
      },
      'prompt_tokens_details': {
        'audio_tokens': 0,
        'cached_tokens': 0
      }
    },
  'model_name': 'gpt-4o-mini-2024-07-18',
  'system_fingerprint': 'fp_72ed7ab54c',
  'finish_reason': 'stop',
  'logprobs': None
}
id='run-0efe9d88-22e8-439b-9ed2-fb03af0b0567-0'
usage_metadata={
  'input_tokens': 155,
  'output_tokens': 27,
  'total_tokens': 182,
  'input_token_details': {
    'audio': 0,
    'cache_read': 0
  },
  'output_token_details': {'audio': 0, 'reasoning': 0}}

=== Output Parsing Input ===
content='An interesting fact about the English language is that "Dreamt" is the only English word that ends with the letters "mt."'
additional_kwargs={'refusal': None}
response_metadata={
  'token_usage': {
    'completion_tokens': 27,
    'prompt_tokens': 155,
    'total_tokens': 182,
    'completion_tokens_details': {
      'accepted_prediction_tokens': 0,
      'audio_tokens': 0,
      'reasoning_tokens': 0,
      'rejected_prediction_tokens': 0},
      'prompt_tokens_details': {
        'audio_tokens': 0, 'cached_tokens': 0
      }
    },
    'model_name': 'gpt-4o-mini-2024-07-18',
    'system_fingerprint': 'fp_72ed7ab54c',
    'finish_reason': 'stop',
    'logprobs': None
  }
  id='run-0efe9d88-22e8-439b-9ed2-fb03af0b0567-0'
  usage_metadata={
    'input_tokens': 155,
    'output_tokens': 27,
    'total_tokens': 182,
    'input_token_details': {'audio': 0, 'cache_read': 0},
    'output_token_details': {'audio': 0, 'reasoning': 0}
  }

=== Output Parsing Output ===
content='An interesting fact about the English language is that "Dreamt" is the only English word that ends with the letters "mt."'
additional_kwargs={'refusal': None}
response_metadata={
  'token_usage': {
    'completion_tokens': 27,
    'prompt_tokens': 155,
    'total_tokens': 182,
    'completion_tokens_details': {
      'accepted_prediction_tokens': 0,
      'audio_tokens': 0,
      'reasoning_tokens': 0,
      'rejected_prediction_tokens': 0
    },
    'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}
  },
  'model_name': 'gpt-4o-mini-2024-07-18',
  'system_fingerprint': 'fp_72ed7ab54c',
  'finish_reason': 'stop',
  'logprobs': None
}
id='run-0efe9d88-22e8-439b-9ed2-fb03af0b0567-0'
usage_metadata={
  'input_tokens': 155,
  'output_tokens': 27,
  'total_tokens': 182,
  'input_token_details': {'audio': 0, 'cache_read': 0},
  'output_token_details': {'audio': 0, 'reasoning': 0}}

=== Final Response ===
content='An interesting fact about the English language is that "Dreamt" is the only English word that ends with the letters "mt."'
additional_kwargs={'refusal': None}
response_metadata={
  'token_usage': {
    'completion_tokens': 27,
    'prompt_tokens': 155,
    'total_tokens': 182,
    'completion_tokens_details': {
      'accepted_prediction_tokens': 0,
      'audio_tokens': 0,
      'reasoning_tokens': 0,
      'rejected_prediction_tokens': 0
    },
    'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}
  },
  'model_name': 'gpt-4o-mini-2024-07-18',
  'system_fingerprint': 'fp_72ed7ab54c',
  'finish_reason': 'stop',
  'logprobs': None
}
id='run-0efe9d88-22e8-439b-9ed2-fb03af0b0567-0'
usage_metadata={
  'input_tokens': 155,
  'output_tokens': 27,
  'total_tokens': 182,
  'input_token_details': {'audio': 0, 'cache_read': 0},
  'output_token_details': {'audio': 0, 'reasoning': 0}
}
```

Remove debugging codes we got the final version of the code as below:

```python
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.runnables import RunnablePassthrough
from langchain_core.output_parsers import StrOutputParser
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--query")
args = parser.parse_args()

def verbose_format_docs(docs):
    # remove print statements, just return the formatted string
    return "\n\n".join(doc.page_content for doc in docs)

# remove the traceable runnable creation function, which only for debugging purpose

embeddings = OpenAIEmbeddings(model="text-embedding-3-large")

db = Chroma(
    embedding_function=embeddings,
    persist_directory="emb"
)

retriever = db.as_retriever()

llm = ChatOpenAI(model="gpt-4o-mini")

prompt = ChatPromptTemplate.from_messages([
    ("system", """You are an assistant, use the context retrieved below to answer the questions. If you don't know the answer, honestly say you don't know. Try to answer concisely, using up to three sentences.

    Context: {context}"""),
    ("human", "{question}")
])

# remove all use of the traceable runnable function
rag_chain = (
    # no RunnableParallel needed, it'll be used under the hood automatically
    {
        "context": retriever | verbose_format_docs, # replace lambda nested structure with pipe operator which is more readable
        "question": RunnablePassthrough()
    }
    # remove traceable runnable creation function, only left Runables
    | prompt
    | llm
    | StrOutputParser()
)

try:
    # remove all print statements
    response = rag_chain.invoke(args.query)
    print(response)

except Exception as e:
    print(f"An error occurred: {e}")

```

Now we can run the final version of the code to get the response from the LLM.

```sh
$ python prompt.py --query 'tell me a interesting fact about English language.'

One interesting fact about the English language is that "dreamt" is the only word that ends with the letters "mt."
```
