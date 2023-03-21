PUT operation

- update시 사용 

```python
from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel
from typing import Optional, List

app = FastAPI()
test_db = {}

class Item(BaseModel):
    name: Optional[str] = None
    description: Optional[str] = None
    price: Optional[float] = None
    tax: float = 10.5
    tags: List[str] = []

items = {
    "foo": {"name": "Foo", "price":50.2},
    "bar": {"name": "Bar", "description": "the bartenders", "price":62, "tax":20.2},
    "baz": {"name": "Baz", "description": None, "price": 50.2, "tax":10.5, "tags":[]},
}

@app.get("/items/{item_id}", response_model=Item)
async def read_item(item_id: str):
    return items[item_id]


@app.put("/items/{item_id}", response_model=Item)
async def update_item(item_id: str, item: Item):
    update_item_encoded = jsonable_encoder(item)
    items[item_id] = update_item_encoded
    return update_item_encoded
```



다음과 같이 update를 할 경우, 문제가 생김

```json
{
    "name": "Bar",
    "price": 80,
    "description": None
}
```



pydantic model 초기값이 tax: 10.5이므로 위처럼 업데이트하면, tax값이 20.2 -> 10.5로 바뀜, 다음과 같이 코드를 변경

```python
@app.patch("/items/{item_id}", response_model=Item)
async def update_item(item_id: str, item: Item):
    stored_item_data = items[item_id]
    stored_item_model = Item(**stored_item_data)
    update_data = item.dict(exclude_unset=True)
    updated_item = stored_item_model.copy(update=update_data)
    items[item_id] = jsonable_encoder(updated_item)
    return updated_item
```



- put 대신 patch 를 사용

- stored_item_data = items[item_i] 저장된 data를 가져옴

- Item(**stored_item_data)를 통해 Pydantic Model로 바꿈

-  a = item.dict(exclude_unset=True) request data를 dict로 변경
  
  이때 exclude_unset을 설정(설정된 데이터만 가지고 dict자료형을 만든다)

- 저장된 데이터에서 copy()를 이용하여 update된 data를 얻음

- updated_item을 json compatiable 데이터로 변환

- db 에 업데이트된 item 저장, db업데이트
