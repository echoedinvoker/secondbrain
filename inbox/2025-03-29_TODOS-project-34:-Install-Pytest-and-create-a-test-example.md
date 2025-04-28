---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# TODOS project 34: Install Pytest and create a test example

```sh
$ pip install pytest # pytest can help us to write tests and execute them
```

```sh
 tree
.
├── fastapienv
└── TodoApp
    ├── __init__.py
    ├── alembic
    ├── alembic.ini
    ├── database.py
    ├── main.py
    ├── models.py
    ├── routers
    └── test # Create a test folder, Pytest will look for `test` folder by default
        ├── __init__.py # make this folder a package
        └── test_example.py # Create a test file, Pytest will look for the file with `test` inside the name
                            # the convention is to use `test_` as a prefix
```

```py
# test_example.py
def test_example():
#   ^^^^^ Pytest will execute all functions which have `test` inside the name, the convention is to use `test_` as a prefix
    assert 1 == 1
    #      ^^^^^^ assertion is a condition, if it's True, the test will pass, otherwise, it will fail
    #             typically we use it to compare two pieces of data or to check if a function returns the expected value
```

We can simply run the test by executing the following command:

```sh
$ pytest
# ============================= test session starts ==============================
# platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
# rootdir: /home/matt/Projects/fastapi
# plugins: anyio-4.8.0
# collected 1 item                                                               
#
# TodoApp/test/test_example.py .                                           [100%]
#
# ============================== 1 passed in 0.01s ===============================
```

A test can include multiple assertions, all assertions must be True to pass the test, otherwise, the test will fail.

```py
def test_example():
    assert 1 == 1
    assert 3 != 4 # add another assertion, which is True
```
```sh
 pytest
# ============================= test session starts ==============================
# platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
# rootdir: /home/matt/Projects/fastapi
# plugins: anyio-4.8.0
# collected 1 item                                                               
#
# TodoApp/test/test_example.py .                                           [100%]
#
# ============================== 1 passed in 0.01s ===============================
```

Because both assertions are True, the test passes.

If we change the second assertion to be False.

```py
def test_example():
    assert 1 == 1
    assert 3 != 3
    #           ^ let the second assertion be False
```

```sh
 pytest
# ============================= test session starts ==============================
# platform linux -- Python 3.13.2, pytest-8.3.5, pluggy-1.5.0
# rootdir: /home/matt/Projects/fastapi
# plugins: anyio-4.8.0
# collected 1 item                                                               
#
# TodoApp/test/test_example.py F                                           [100%]
#
# =================================== FAILURES ===================================
# _________________________________ test_example _________________________________
#
#     def test_example():
#         assert 1 == 1
# >       assert 3 != 3
# E       assert 3 != 3
#
# TodoApp/test/test_example.py:3: AssertionError
# =========================== short test summary info ============================
# FAILED TodoApp/test/test_example.py::test_example - assert 3 != 3
# ============================== 1 failed in 0.03s ===============================
```

Because the second assertion is False, the test fails, even though the first assertion is True.

