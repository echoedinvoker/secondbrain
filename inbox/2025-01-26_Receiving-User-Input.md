---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Receiving User Input

For this topic, we want to create a prompt that can repeatedly receive user input, and directly print out the content entered by the user.

```py
while True:
    content = input(">> ")
    print("You entered: " + content)
```

Then, run the script to into the prompt:

```sh
$ python main.py
>> hi
You entered: hi
>> test
You entered: test
>>
```

