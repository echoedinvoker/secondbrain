---
date: 2024-10-08
type: fact
aliases:
  -
hubs:
  - "[[DDI]]"
---

# Check ESG Risk data with Python

**load and print head of the data**

```python
df_esg = pd.read_csv('SP500_ESG_Data.csv')
df_esg.head()
```

| Symbol | Security | ESG Risk Score | Environment Risk Score | Social Risk Score | Governance Risk Score | Controversy Level | Data Date |
|--------|----------|----------------|------------------------|-------------------|----------------------|-------------------|-----------|
| MMM | 3M Company | 35.0 | 13.2 | 13.6 | 8.1 | 3.0 | 2021-05 |
| ABT | Abbott Laboratories | 30.0 | 3.1 | 16.2 | 10.6 | 3.0 | 2021-05 |
| ABBV | AbbVie Inc. | 29.0 | 0.6 | 17.1 | 11.3 | 3.0 | 2021-05 |
| ABMD | Abiomed | NaN | NaN | NaN | NaN | NaN | NaN |
| ACN | Accenture | 11.0 | 0.6 | 5.0 | 5.8 | 2.0 | 2021-05 |

You should notice that the ESG risk in the table above is cross-sectional data, meaning that each row represents the ESG risk data of a company, and [[2024-10-08_ESG-Data-limitations|the values do not change over time]].

Additionally, the ESG risk data contains missing values. For example, the ESG risk data for ABMD company is NaN.


**draw histogram of ESG Risk Score**

```python
df_esg['ESG Risk Score'].hist(figsize=(8,5))
```
![hist-of-ESG-risk.png](../assets/imgs/hist-of-ESG-risk.png)

x-axis: ESG Risk Score
y-axis: count

