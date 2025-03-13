---
date: 2025-02-11
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Environment Variables and dot env File

In Python, we can access environment variables through the os module.

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
import os  # Import the os module
if __name__ == '__main__':
    print(os.environ['EDITOR'])
    #     ^^^^^^^^^^^^^^^^^^^^ get the value of the EDITOR environment variable
```

Let's run the script and see the output.

```bash
 python ice_breaker.py 
nvim # EDITOR environment variable value
```

We can also create a .env file in the root directory of the project, and use the python-dotenv package to load the variables in the .env file into the environment variables.

File: /home/matt/Github/ice_breaker/.env
```sh
COOL_API_KEY='you_are_cool'
```

Then, install the python-dotenv package.

```bash
$ pipenv install python-dotenv
```

Modify the script a bit to load the value of the COOL_API_KEY and print it.

File: /home/matt/Github/ice_breaker/ice_breaker.py
```python
import os
from dotenv import load_dotenv # import the load_dotenv function from the dotenv module
if __name__ == '__main__':
    load_dotenv() # execute the load_dotenv function, it'll load all the variables in the .env file into the environment variables
    print(os.environ['COOL_API_KEY']) # now you can get the value of the COOL_API_KEY as an environment variable
```

Let's run the script and see the output.

```bash
 python ice_breaker.py
you_are_cool
```





