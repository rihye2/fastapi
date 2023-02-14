

### Request Body

- **requestbody:** client 가 server로 보내는 데이터

- **responsebody:** server가 client 쪽으로 보내는 데이터



#### Pydantic

```python
from fastapi import FastAPI
from typing import Optional

from pydantic import BaseModel

class Item(BaseModel): #BaseModel 상속
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()

@app.post("/items/")
async def create_item(item: Item):
    return item
```

- None으로 초기값 설정 -> 필수로 받지 않아도 가능

- { "name": "hr"
    "price":45.2
  
   }  이런 데이터 형태도 허용 

클래스 모델에 맞춰 JSON Schema를 생성



```python
@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price * item.tax
        item_dict.update({"item price with tax: ": price_with_tax})
    return item_dict
```



#### Request Body + Path parameter

```python
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()

@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item):
    return {"item_id": item_id, **item.dict()}



```

- path parameter 는 경로에서 받고, model을 받을때는 request body에서 받음

```

```

```

```
