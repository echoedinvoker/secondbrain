---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Books2 project: create items of the list by regular class

Instead of using dictionaries to represent the books in the list, we can create a class to represent a book, and then use the constructor of the class to create book objects in the list.

```py
from fastapi import FastAPI


app = FastAPI()

# create a class to represent a book
class Book:
    id: int
    title: str
    author: str
    description: str
    rating: int

    def __init__(self, id: int, title: str, author: str, description: str, rating: int):
        self.id = id
        self.title = title
        self.author = author
        self.description = description
        self.rating = rating

BOOKS = [
    # use the constructor of the Book class to create book objects in the list
    Book(
        id=1,
        title="哈利波特與魔法石",
        author="J.K. 羅琳",
        description="一個住在樓梯下儲藏室的男孩，發現自己是一個巫師，並開始了他在霍格華茲魔法學校的冒險。",
        rating=5
    ),
    Book(
        id=2,
        title="小王子",
        author="安東尼·德·聖修伯里",
        description="一個來自外星球的小王子，透過旅行學習愛與責任的寓言故事。",
        rating=4
    ),
    Book(
        id=3,
        title="1984",
        author="喬治·歐威爾",
        description="描述一個極權主義社會，探討監視、思想控制和個人自由的經典反烏托邦小說。",
        rating=5
    ),
    Book(
        id=4,
        title="三體",
        author="劉慈欣",
        description="中國科幻小說，描述人類與外星文明的接觸，以及由此引發的一系列事件。",
        rating=5
    ),
    Book(
        id=5,
        title="活著",
        author="余華",
        description="講述了一個人在經歷各種苦難後，仍然堅強活下去的故事。",
        rating=4
    )
]

@app.get("/books")
def read_all_books():
    return BOOKS

@app.post("/books")
def create_book(book_request=Body()):
    BOOKS.append(book_request)

```
However, when using this method, the client may not necessarily pass an object that conforms to the Book class when using the POST API. Let me check the definition in Swagger UI.

![swagger-post-with-regular-class.png](../assets/imgs/swagger-post-with-regular-class.png)

As you can see, there is no description about the Book class in the Request body section of Swagger UI. In this case, the client cannot know what kind of object should be passed to the API, and there is no mechanism for checking. Any object passed by the client will be appended to the BOOKS list.



