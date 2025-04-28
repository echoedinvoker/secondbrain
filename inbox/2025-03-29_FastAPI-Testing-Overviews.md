---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# FastAPI Testing Overviews

## What is Testing?

- It's a way for us to make sure our application is working as intended.

- Part of the software development lifecycle (SDLC) that aims to identify:
    - Bugs
    - Errors
    - Defects
 
- Meets user requirements and specifications.

- Ensuring Software is high quality, reliable, secure and user friendly.


## Three Types of Testing

1. **Manual Testing**
Manual testing is the process of manually testing software for defects. It requires a tester to play the role of an end user and use most of all features of the application to ensure correct behavior.

2. **Unit Testing**
- Involve testing individual components or units of software in isolation from the rest of the application.
- Validate that each unit of the software performs as designed.
- Unit = Testable part of the application.
- Developers write unit tests during the development phase.
- Tests are automated and executed by a testing framework (e.g. Pytest).
- Benefit is to identify bugs early in the development process.

3. **Integration Testing**
- Focuses on testing the interactions between different units or components of the piece of software.
- The scope is broader than unit testing, as we are testing multiple units together.
- Helps identify problems for the entire solution.
- Example: Call an API endpoint and make sure the correct solution is returned.

## Pytest

- Popular testing framework for Python.

- Known for simplicity, scalability and ability to handle both unit and integration tests.

- Top reasons to use Pytest:
    - Simple & Flexible - Native Assertions
    - Fixtures - Feature setup and teardown
    - Parameterized Testing - Run same tests with different data


