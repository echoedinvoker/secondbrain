---
date: 2025-01-30
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Retrieve with MMR search type

In the new version of retriver, the MMR search type has been introduced, which we can directly use to avoid retrieving overly similar documents.

## MMR Principle

MMR is an intelligent document retrieval algorithm designed to balance two critical objectives:

    Document relevance to the query
    Diversity among retrieved documents

**Core Concept**

    Not just returning the most similar documents to a query
    Selecting documents that are both relevant to the query and dissimilar to each other

**MMR Algorithm Steps**

    Initial Retrieval Phase

    Calculate similarity scores between query vector and all documents
    Sort candidate documents by relevance in descending order

    Diversity Selection

    Progressively select documents while penalizing candidates highly similar to already selected documents
    Use a formula balancing relevance and diversity


## MMR Parameters Explained

**1. search_type="mmr"**

    Explicitly specifies MMR retrieval strategy

**2. fetch_k**

    Number of initial documents to retrieve
    Default typically 20
    Purpose: Expand candidate document pool for diverse selection

Example:
```py
retriever = vector_store.as_retriever(
    search_type="mmr", 
    search_kwargs={
        "fetch_k": 20  # Select final results from 20 documents
    }
)
```

**3. lambda_mult (Most Critical Parameter)**

    Range: 0 to 1
    Controls trade-off between relevance and diversity

λ = 0 (Maximum Diversity)

    Prioritizes document differences
    Returns highly dissimilar documents
    Suitable for scenarios requiring broad perspectives

λ = 1 (Maximum Relevance)

    Almost equivalent to standard similarity search
    Returns most query-similar documents
    Ideal for precise matching scenarios

Intermediate Values (Recommended 0.5 - 0.7)

    Balances relevance and diversity
    0.5: Relatively balanced
    0.7: Slightly bias towards relevance

Example:
```py
retriever = vector_store.as_retriever(
    search_type="mmr", 
    search_kwargs={
        "fetch_k": 20,
        "lambda_mult": 0.7  # Slightly favoring relevance
    }
)
```

*Practical Application Recommendations*

    Experimental Optimization

    Experiment with different lambda_mult values
    Adjust based on specific task and dataset

    Typical Use Cases

    Summary generation: Requires multi-perspective information
    Question-answering systems: Needs comprehensive context
    Research reviews: Requires covering different viewpoints

## Code Example
```py
query = "A quote about our favorite four-legged pets"
retriever = docstore.as_retriever(
    search_type="mmr", 
    search_kwargs={
        "fetch_k": 20,     # Initial document retrieval count
        "lambda_mult": 0.7 # Balance relevance and diversity
    }
)
matched_docs = retriever.invoke(query)
```

## Underlying Mathematical Principle (Simplified)

MMR = argmax[D_i ∈ R] [λ * Sim(D_i, Q) - (1-λ) * max[D_j ∈ S] Sim(D_i, D_j)]

Where:

    D_i: Candidate document
    R: Retrieved document set
    Q: Query vector
    S: Already selected documents
    λ: Diversity control parameter
    Sim(): Similarity function

Key Insights

    MMR is not just about finding similar documents
    It's about creating a diverse, relevant document set
    The lambda_mult parameter is your primary tuning mechanism

Recommendation: Always experiment and validate MMR performance with your specific use case and dataset.
