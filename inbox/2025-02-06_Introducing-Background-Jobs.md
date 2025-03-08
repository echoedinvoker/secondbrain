---
date: 2025-02-06
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Introducing Background Jobs


## What is Pattern `background processing`

In [[2025-02-06_Why-is-Processing-Taking-Forever?|Why is Processing Taking Forever?]], we figured out a way to optimize user experience by handling long-running tasks in the background. This pattern is called `background processing`, which is a extremely common pattern in web development.

Anything that takes a long time to process should use this pattern, such as:

- Sending emails
- Processing uploaded files
- Report generation
- Content moderation
- File conversion
- Bulk operations
- Search indexing
- Complex calculations
- Recommendation generation
- Data import/export

In our case, we have some processing that takes a long time to complete, listed below:

- Generating embeddings
- Running an agent
- Running tools
- Generating a text in bulk

But there is an exception:

> Generating text to immediately show to a user

![generating-to-user-immediately.png](../assets/imgs/generating-to-user-immediately.png)

In the above situation, the user will be eager to see the results, so background processing is not suitable. However, we can change to a streaming method to display the text.


## How to Implement `background processing`

![how-to-implement-back-proc.png](../assets/imgs/how-to-implement-back-proc.png)

Our original Python server does not handle time-consuming jobs itself, but instead generates a JSON string describing the job and passes it to a Worker for processing through a message broker (we use Redis). The Worker is a standalone program specifically designed to handle these long-running jobs.

But, why do we need a message broker? Can't we just pass the job json to the Worker directly?

The reason is that if our app becomes more popular and the number of jobs increases, we can easily scale the Worker and the server separately, it can't be done without a message broker.

![high-flow-situation-broker.png](../assets/imgs/high-flow-situation-broker.png)

Message broker can ensure that jobs are evenly distributed to each worker.

In this framework, we can easily increase or decrease the number of workers according to demand and load, in order to achieve better performance.



