### Path Operation Configuration

path 경로에 몇 가지 기능을 더 넣을 수 있음



- response의 status code 지정

- tags를 이용하여 operation에 tag를 달아 줄 수 있음

- summary, description 추가 가능

```python
from fastapi import FastAPI, status
from pydantic import BaseModel
from typing import Optional, Set

app = FastAPI()



class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = set()


@app.post("/items/", response_model=Item, 
          summary="Create an item",
          status_code=status.HTTP_201_CREATED,
          description="create an tiem with all the information, name,description",
          tags=["items"])
async def create_item(item: Item):
    return item

@app.get("/items/", tags=["items"])
async def read_items():
    return [{"name":"aa", "price":123}]

@app.get("/users/", tags=["users"])
async def read_users():
    return [{"username":"dfjfjfd"}]

```



아래와 같은 방법으로 docs에 description 추가하는 방법도 있음

```python

```




