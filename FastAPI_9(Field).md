### Body Fields

pydantic 모델도 validation이나 metadata 추가 가능

request body안에 있는 metadata를 field 클래스로 다룰 수 있음(pydantic의 model 변수선언에 제약 조건을 걸 수 있음)

```python
from typing import Optional
from fastapi import Body, FastAPI
from pydantic import BaseModel, Field

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str | None = Field(
        None, title="The description", max_length=100
    )
    price: float = Field(..., gt=0, description="greater than 0")
    tax: float | None = None

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(..., embed=True)):
    results = {"item_id": item_id, "item": item}
    return results
```

#### List Field

metadata로 List Field 선언 또한 가능하며, **List에 type parameter**를 넣으면 리스트안에 들어갈 타입을 제한할 수 있다.

```python
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: List[str] = []

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

#### Set Field

마찬가지로 set 또한 이용할 수 있음

```python
class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = set()

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```

#### 중첩 Nested model

pydantic으로 정의한 모델 또한 하나의 자료형이 된다. 따라서 다른 pydantic 모델의 type hint에 쓰일 수 있다.

아래의 예시는 두번째 예시와 같은 구조의 body가 들어와야 한다.

```python
from typing import List, Optional, Set
from fastapi import FastAPI
from pydantic import BaseModel, HttpUrl

app = FastAPI()

class Image(BaseModel):
    url: HttpUrl #경로를 str이 아닌 Pydantic의 HttpUrl로 선언 가
    name: str

class Item(BaseModel):

    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: Set[str] = set()                
    images: Optional[List[Image]] = None #Model을 List타입으로 강제할 수 있


class Offer(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    items: List[Item]

@app.post("/offers/")
async def create_offer(offer: Offer):
    return offer
```

```json
{
  "name": "string",
  "description": "string",
  "price": 0,
  "items": [
    {
      "name": "string",
      "description": "string",
      "price": 0,
      "tax": 0,
      "tags": [],
      "images": [
        {
          "url": "string",
          "name": "string"
        }
      ]
    }
  ]
}
```

++ 추가

**Query, Path는 Param이라는 클래스의 object를 return한다. Param은 pydantic의 FieldInfo 클래스의 하위 클래스**

*(확인해보니 아래와 같았음)*

```python
from fastapi import params
#param_function.py
def Path(  # noqa: N802
    .... ) -> Any:
    return params.Path(....)

def Query(
    .... )-> Any:
    return params.Query(....)

#params.py
class Path(Param)
class Query(Param)


class Param(FieldInfo)
```

**Field는 FieldInfo 클래스의 인스턴스를 리턴,**

**Body도 마찬가지로 FieldInfo 타입의 하위 클래스를 return**

```python
#param_function.py
def Field(....) -> Any:
    field_info = FieldInfo(..)
    ....
    return field_info

def Body(....) -> Any: 
    ....
    return params.Body()



#params.py
class Body(FieldInfo):
```
