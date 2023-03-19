### JSON compatible Encoder

- jsonable_encoder() 
  
  - 데이터 자료형을 json compatible한 데이터로 바꿔주는 함수
  
  - 즉, 어떠한 데이터를 JSON으로 바꿔야 할 때 사용 
  
  - ex) 데이터베이스는 Pydantic model을 받지 않고 dict만 받는 경우가 있음
  
  - ex) 또는 datetime을 데이터베이스로 넘겨줄 때, str으로 바꿔주어야 함



```python
from datetime import datetime
from fastapi import FastAPI
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel
from typing import Optional

app = FastAPI()
test_db = {}

class Item(BaseModel):
    title: str
    timestamp: datetime
    description: Optional[str] = None


@app.put("/items/{id}")
def update_item(id: str, item: Item):
    print("original item : ", type(item), "original datetime: ", type(item.timestamp))

    json_compatible_item_data = jsonable_encoder(item)
    print("convert item :", type(json_compatible_item_data), "convert datetime :", type(json_compatible_item_data['timestamp']))

    test_db[id] = json_compatible_item_data
    return test_db[id]
```


