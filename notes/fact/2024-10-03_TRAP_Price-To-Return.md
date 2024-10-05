---
date: 2024-10-03
type: fact
aliases:
  -
hubs:
  - "[[DDI]]"
---

# Trap: Price To Return

![price_to_return_fail.png](../../assets/imgs/price_to_return_fail.png)

When calculating with Pandas, NaN will be ignored but 0.0 will not, so the result above will produce errors compared to the expected result.

However, we can use the parameter fill_method=None of .pct_change() to solve this problem.

![price_to_return_pass.png](../../assets/imgs/price_to_return_pass.png)

