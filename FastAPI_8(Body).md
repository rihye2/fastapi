### Body parameter

#### QueryParameter, PathParameter, RequestBody를 같이 사용

(RequestBody값에 Path와 Query를 적용하는 예제)

```python
from fastapi import FastAPI, Path
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None

@app.put("/items/{item_id}")
async def update_item(*, item_id: int = Path(..., title="item id to get",
                        ge=0, le=100),
                        q: str | None = None
                        item: Item | None = None,):
    results = {"item_id": item_id}
    if q: 
        results.update({"q":q})
    if item:
        results.update({"item": item})
    return results
```

#### Multiple Request Body Parameters

requestBody를 함수 input에 여러개를 줄 수 있음

```python
from fastapi import FastAPI, Path
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None

class User(BaseModel):
    username: str
    full_name: str | None = None

@app.put("/items/{item_id}")
async def update_item(item_id : int, item: Item, user:User):
    results = {"item_id": item_id, "item": item, "user": user}

    return results
```

```json
#request body
{
  "item_id": 12,
  "item": {
    "name": "string",
    "description": "string",
    "price": 10,
    "tax": 3
  },
  "user": {
    "username": "aaa",
    "full_name": "bbb"
  }
}
  }
}
```

#### single value in body

여러개의 변수를 캡슐화한 Pydantic 모델이 아닌, 단일 변수로 받고 싶을 때

Body 클래스를 통해 데이터 선언가능

```python
from typing import Optional

from fastapi import Body, FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


class User(BaseModel):
    username: str
    full_name: Optional[str] = None


@app.put("/items/{item_id}")
async def update_item(
    item_id: int, item: Item, user: User, importance: int = Body(...)):
    results = {"item_id": item_id, "item": item, "user": user, "importance": importance}
    return results
```

- importance 변수에 Body(...)를 줌 = 해당 변수가 RequestBody에 포함된 값임을 명시

- QueryParameter는 Query, PathParameter에서는 Path, RequestBody는 Body로 제

- 사용하지 않을 경우 QueryParameter로 인지 

#### Embed a single body parameter

embed를 이용하여 key값을 요청 받을 수 있다. 이전처럼 

```python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(...)):
    results = {"item_id": item_id, "item": item}
    return results
```

이런 경우 RequestBody는 이렇게 요청을 보낸다. 

```json
{
    "name": "Foo",
    "description": "The pretender",
    "price": 42.0,
    "tax": 3.2
}
```

그러나 key를 명시한 것을 요청으로 받고 싶을 경우 `embed=True` 구문을 사용한다.

```python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(..., embed=True)):
    results = {"item_id": item_id, "item": item}
    return results
```

이렇게 되면 이전과 다르게 아래처럼 body를 만들어 보내야하며, 
그렇지 않을경우 두번째 예시와 같은 error발

```json
{
    "item": {
        "name": "Foo",
        "description": "The pretender",
        "price": 42.0,
        "tax": 3.2
    }
}
```

```json
{
  "detail": [
    {
      "loc": [
        "body",
        "item"
      ],
      "msg": "field required",
      "type": "value_error.missing"
    }
  ]
}
```
