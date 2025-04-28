---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[python]]"
---

# Python Random String Generation: random.choices and string Module

```python
import random
import string

# string 模組中的常用常量
print(string.ascii_lowercase)  # 小寫字母: 'abcdefghijklmnopqrstuvwxyz'
print(string.ascii_uppercase)  # 大寫字母: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
print(string.ascii_letters)    # 所有字母: 小寫+大寫
print(string.digits)           # 數字字符: '0123456789'
print(string.punctuation)      # 標點符號: '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'

# random.choices() 用法
# 語法: random.choices(population, weights=None, cum_weights=None, k=1)
# - population: 要從中選擇的序列
# - weights: 相對權重列表 (可選)
# - cum_weights: 累積權重 (可選)
# - k: 要選擇的元素數量

# 示例 1: 生成包含 5 個隨機字母的字符串
random_letters = ''.join(random.choices(string.ascii_letters, k=5))
print(random_letters)  # 例如: 'xHbQs'

# 示例 2: 生成包含字母和數字的隨機字符串
random_alphanumeric = ''.join(random.choices(
    string.ascii_letters + string.digits, k=8))
print(random_alphanumeric)  # 例如: 'a7Xp9Qr2'

# 示例 3: 使用權重 - 使數字出現的機率是字母的兩倍
weighted_random = ''.join(random.choices(
    population=string.ascii_letters + string.digits,
    weights=[1] * len(string.ascii_letters) + [2] * len(string.digits),
    k=10
))
print(weighted_random)  # 將包含更多數字的隨機字符串

# 示例 4: 生成隨機密碼 (包含字母、數字和特殊字符)
random_password = ''.join(random.choices(
    string.ascii_letters + string.digits + string.punctuation, k=12))
print(random_password)  # 例如: 'e4$xA2?p9&Zk'

# 與 random.choice() 的區別
# random.choice(): 從序列中隨機選擇單個元素
# random.choices(): 可以選擇多個元素，並允許重複
single_choice = random.choice(string.ascii_letters)  # 只返回一個字符
multiple_choices = random.choices(string.ascii_letters, k=5)  # 返回一個列表，可能有重複字符
```

