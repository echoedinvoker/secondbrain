---
date: 2024-10-07
type: tmp
aliases:
  -
hubs:
  - "[[Work]]"
---

# Curl to read redis key of bug #26083

```bash
curl -X 'PUT' \
        'http://localhost:8000/dynamic_data/get_dynamic_data' \
        -H 'accept: application/json' \
        -H 'accept-language: zh-TW' \
        -H 'Content-Type: application/json' \
        -d '{"key":"RS05.平置倉.貨主排行榜DESC","params":{},"paginationParams":{}}'

"{\"labels\": [\"1\", \"2\", \"3\", \"4\"], \"datasets\": [{\"label\": \"#\", \"data\": [1, 2, 3, 4]}, {\"label\": \"\\u8ca8\\u4e3b\\u540d\\u7a31\", \"data\": [\"\\u5ee0\\u5
5461\", \"\\u5ee0\\u55464\", \"\\u5ee0\\u55462\", \"\\u5ee0\\u55463\"]}, {\"label\": \"\\u65e5\\u5747\\u51fa\\u8ca8\\u91cf\", \"data\": [28, 44, 14, 250]}, {\"label\": \"\\u
5eab\\u5b58\\u53ef\\u7528\\u91cf\", \"data\": [5179, 2046, 637, 1055]}, {\"label\": \"\\u5eab\\u5b58\\u5468\\u8f49\\u5929\\u6578\", \"data\": [184, 46, 45, 4]}], \"color\": 
[]}"⏎                                                                                                                                                                        

curl -X 'PUT' \
        'http://localhost:8000/dynamic_data/get_dynamic_data' \
        -H 'accept: application/json' \
        -H 'accept-language: zh-TW' \
        -H 'Content-Type: application/json' \
        -d '{"key":"RS05.平置倉.貨主排行榜ASC","params":{},"paginationParams":{}}'

"{\"labels\": [\"1\", \"2\", \"3\", \"4\"], \"datasets\": [{\"label\": \"#\", \"data\": [1, 2, 3, 4]}, {\"label\": \"\\u8ca8\\u4e3b\\u540d\\u7a31\", \"data\": [\"\\u5ee0\\u5
5463\", \"\\u5ee0\\u55462\", \"\\u5ee0\\u55464\", \"\\u5ee0\\u55461\"]}, {\"label\": \"\\u65e5\\u5747\\u51fa\\u8ca8\\u91cf\", \"data\": [250, 14, 44, 28]}, {\"label\": \"\\u
5eab\\u5b58\\u53ef\\u7528\\u91cf\", \"data\": [1055, 637, 2046, 5179]}, {\"label\": \"\\u5eab\\u5b58\\u5468\\u8f49\\u5929\\u6578\", \"data\": [4, 45, 46, 184]}], \"color\": 
[]}"⏎                                                                                                                                                                        


redis-cli -a 1qazxsw2 --raw

get "RS05.平置倉.貨主排行榜DESC"

{"labels": ["1", "2", "3", "4"], "datasets": [{"label": "#", "data": [1, 2, 3, 4]}, {"label": "\u8ca8\u4e3b\u540d\u7a31", "data": ["\u5ee0\u55461", "\u5ee0\u55464", "\u5ee0\
u55462", "\u5ee0\u55463"]}, {"label": "\u65e5\u5747\u51fa\u8ca8\u91cf", "data": [28, 44, 14, 250]}, {"label": "\u5eab\u5b58\u53ef\u7528\u91cf", "data": [5179, 2046, 637, 105
5]}, {"label": "\u5eab\u5b58\u5468\u8f49\u5929\u6578", "data": [184, 46, 45, 4]}], "color": []}

get "RS05.平置倉.貨主排行榜ASC"

{"labels": ["1", "2", "3", "4"], "datasets": [{"label": "#", "data": [1, 2, 3, 4]}, {"label": "\u8ca8\u4e3b\u540d\u7a31", "data": ["\u5ee0\u55463", "\u5ee0\u55462", "\u5ee0\
u55464", "\u5ee0\u55461"]}, {"label": "\u65e5\u5747\u51fa\u8ca8\u91cf", "data": [250, 14, 44, 28]}, {"label": "\u5eab\u5b58\u53ef\u7528\u91cf", "data": [1055, 637, 2046, 517
9]}, {"label": "\u5eab\u5b58\u5468\u8f49\u5929\u6578", "data": [4, 45, 46, 184]}], "color": []}

```
