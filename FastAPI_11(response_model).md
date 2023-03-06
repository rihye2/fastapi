#### Response Model

 operation들에 대해(get, post, put, delete 등) response_model을 지정할 수 있다.

- decoration에서 선언!

- output data를 response_model에서 지정한 모델로 변환

```python
app = FastAPI()

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    tags: List[str] = []

@app.post("/items/", response_model=Item)
async def create_item(item: Item):
    return item
```

create_user함수에서 UserIn을 받지만 operation에서 UserOut의 형태로 output data를 반환

- output 형태를 선언한 타입으로 변환할 수 있음
  (즉, 출력 데이터 모델을 지정해줄 수 있다)

```python
from pydantic import BaseModel, EmailStr

app = FastAPI()

class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr
    full_name: Optional[str] = None

class UserOut(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None


@app.post("/user/", response_model=UserOut)
async def create_user(user: UserIn):
    return user
```

#### response_model_exclude_unset=True

아래 Item은 default value를 가지지만, 이러한 값을 response에 담아서 보내기 싫을 경우 `response_model_exclude_unset=True` 로 지정

```python
class Item(BaseModel):

    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = 10.5
    tags: List[str] = []


items = {
    "aa" : {"name": "aa", "price":50.2},
    "bb" : {"name": "bb", "description": "The bartenders", "price":62, "tax":20.2},
    "cc" : {"name": "cc", "description": None, "price": 50.2, "tax":10.5, "tags":[]},
}

@app.post("/items/{item_id}", response_model=Item, response_model_exclude_unset=True)
async def create_item(item_id: str):
    return items[item_id]
```

순서대로 item_id = 'aa', 'bb', 'cc' execute 할 경우

```python
#item_id = "aa""

{
  "name": "aa",
  "price": 50.2
}
#item_id = "bb"
{
  "name": "bb",
  "description": "The bartenders",
  "price": 62,
  "tax": 20.2
}

#item_id = "cc"
{
  "name": "cc",
  "description": null,
  "price": 50.2,
  "tax": 10.5,
  "tags": []
}
```

#### response_model_include, response_model_exclude

response 모델의 필드를 include or exclude 할지 결정할 수 있음

- response_model_include의경우 지정된 값이 존재할 시 return, 없으면 null

- response_model_exclude의 경우 특정 필드 제외하고 나머지 값이 있다면 전부 return

```python
@app.get("/items/{item_id}/name", response_model=Item, response_model_include={"name", "description"},)
async def read_item_name(item_id: str):
    return items[item_id]

@app.get("/items/{item_id}/public", response_model=Item, response_model_exclude={"tax"})
async def read_item_public_data(item_id: str):
    return items[item_id]
```

response_model_include={"name", "description"}

```json
{
  "name": "aa",
  "description": null
}


{
  "name": "bb",
  "description": "The bartenders"
}


{
  "name": "cc",
  "description": null
}
```

response_model_exclude={"tax"}

```json
{
  "name": "aa",
  "description": null,
  "price": 50.2,
  "tags": []
}

{
  "name": "bb",
  "description": "The bartenders",
  "price": 62,
  "tags": []

}

{
  "name": "cc",
  "description": null,
  "price": 50.2,
  "tags": []
}
```
