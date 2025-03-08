---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Connecting Chains Together

In the [[2025-01-26_Parsing-COmmand-Line-Arguments|previous topic]], we created a chain to generate a code for given input. In this topic, we will create another chain to write a unit test for the generated code of the first chain. And connect these two chains together.

```py
from langchain_openai import OpenAI
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--language", default="Python")
parser.add_argument("--task", default="add two numbers")
args = parser.parse_args()

# for better readability, rename the `prompt` variable to `generate_prompt`
generate_prompt = ChatPromptTemplate.from_template("Write a very short {language} function that will {task}.")

# create the prompt template of the 2nd chain to write a unit test
check_prompt = ChatPromptTemplate.from_template("Write a unit test for following {language} code: {code}")

model = OpenAI() # we can reuse the same model for both chains

chain = generate_prompt | model | StrOutputParser()

composed_chain = (
    chain
    | (lambda x: {"code": x, "language": args.language })
#             ^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#      here we use lambda to access the output of the previous chain
#      and group it with the language argument from the command line
#      for the next chain prompt template
    | check_prompt
    | model
    | StrOutputParser()
)
# basically, we pipe lots of Runable objects together to create a composed chain, which is also a Runable object (means it can be invoked)

# invoke the composed chain instead of the first chain
result = composed_chain.invoke({ "language": args.language, "task": args.task })
print(result)
```

Then, run the script with the following command:

```sh
$ python main.py --language javascript --task 'print hello world'

describe("sayHello", () => {
  it("should print 'Hello World' when called", () => {
    // Arrange
    const expected = "Hello World";

    // Act
    sayHello();

    // Assert
    expect(console.log).toHaveBeenCalledWith(expected);
  });
});


