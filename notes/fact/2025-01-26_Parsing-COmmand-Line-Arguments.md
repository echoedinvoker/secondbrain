---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Parsing COmmand Line Arguments

Let's modify the codes we've written in [[2025-01-26_Adding-a-Chain|Adding a Chain]], so that we can pass the prompt inputs as command line arguments.

```py
from langchain_openai import OpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
import argparse # Import the argparse library for parsing command line arguments

parser = argparse.ArgumentParser() # Create an ArgumentParser object
# Add the arguments we want to parse
parser.add_argument("--language", default="Python")
parser.add_argument("--task", default="add two numbers")
# Parse the arguments, and store them in the args variable
args = parser.parse_args()

prompt = ChatPromptTemplate.from_template("Write a very short {language} function that will {task}.")

model = OpenAI()

chain = prompt | model | StrOutputParser()

# replace the dictionary values with the command line arguments
result = chain.invoke({ "language": args.language, "task": args.task })
#                                   ^^^^^^^^^^^^^          ^^^^^^^^^
print(result)
```

Then, we can run the script with arguments like this:

```sh
$ python main.py --language javascript --task 'print hello world'
#                ^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^

function printHello() {
  console.log("Hello World");
}
```


