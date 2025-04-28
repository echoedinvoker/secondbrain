---
date: 2025-03-31
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Pytest objects: Fixture

Sometimes we need to instantiate the same object in multiple tests as follows:

```py
import pytest

class Student:
    def __init__(self, name: str, major: str, years: int):
        self.name = name
        self.major = major
        self.years = years

def test_persion_init():
    student = Student('John', 'CS', 4) # instantiate the object
    assert student.name == 'John', "Name should be 'John'"
    assert student.major == 'CS', "Major should be 'CS'"
    #                             ^^^^^^^^^^^^^^^^^^^^^^ this is the assertion message, which will be printed if the test fails
    #                                                    it's optional
    assert student.years == 4

def test_student_update_major():
    student = Student('John', 'CS', 4) # instantiate the same object
    student.major = 'Math'
    assert student.major == 'Math'
```

We can use the `pytest.fixture` decorator to create a fixture and reuse it in multiple tests.

```py
import pytest

class Student:
    def __init__(self, name: str, major: str, years: int):
        self.name = name
        self.major = major
        self.years = years

# create a fixture by using the pytest.fixture decorator
@pytest.fixture
def default_student():
    return Student('John', 'CS', 4)

def test_persion_init(default_student):
#                     ^^^^^^^^^^^^^^^ pass the fixture as an argument
    assert default_student.name == 'John'
    assert default_student.major == 'CS'
    assert default_student.years == 4
    #      ^^^^^^^^^^^^^^^ use the fixture

def test_student_update_major(default_student):
#                             ^^^^^^^^^^^^^^^
    default_student.major = 'Math'
    assert default_student.major == 'Math'
    #      ^^^^^^^^^^^^^^^ reuse the fixture
```

