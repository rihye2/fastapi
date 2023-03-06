### 예제

```python
from fastapi import Body, FastAPI
from pydantic import BaseModel, EmailStr
from typing import Optional, List

app = FastAPI()

'''
input model은 password 포함
output model은 password 미포함
DB model은 hashed password 포함

'''

class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr
    full_name: Optional[str] = None

class UserOut(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None

class UserInDB(BaseModel):
    username: str
    hashed_password: str
    email:EmailStr
    full_name: Optional[str] = None

def convert_password_hasher(raw_password: str):
    return "secret" + raw_password

def convert_save_user(user_in: UserIn):
    hashed_password = convert_password_hasher(user_in.password)
    user_in_db = UserInDB(**user_in.dict(), hashed_password=hashed_password)
    print('converted User save')
    return user_in_db

@app.post("/user/", response_model=UserOut)
async def create_user(user_in: UserIn):
    user_saved = convert_save_user(user_in)
    return user_saved
```

여기서 **user_in.dict()은

```python
user_in = UserIn(username="kkk", password="abab", email="kkk11@email.com")
```

라고 생성된 pydantic object를 user_in.dict()를 통해 dict 자료형으로 변환

```python
{
    'username': 'kkk',
    'password': 'abab',
    'email': 'kkk11@email,com',
    'full_name': None,
}
```

이렇게 생성된 데이터를 함수에 인자를 통해 넘기고 싶으면 **user_dict를 통해 unwrap하여 넘긴다. 아래와 동일 

```python
UserInDB(
    username = user_dict["username"],
    password = user_dict["password"],
    email = user_dict["email"],
    full_name = user_dict["full_name"],
    hashed_password = hashed_password,
)
```

위의 코드에서 클래스마다 중복되는 값들을 상속으로 줄일 수 있다

```python
'''
UserBase를 각 클래스에서 상속받아 속성값 중복을 줄
'''


class UserBase(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None

class UserIn(UserBase):
    password: str


class UserOut(UserBase):
    pass

class UserInDB(UserBase):
    hashed_password: str


def convert_password_hasher(raw_password: str):
    return "secret" + raw_password

def convert_save_user(user_in: UserIn):
    hashed_password = convert_password_hasher(user_in.password)
    user_in_db = UserInDB(**user_in.dict(), hashed_password=hashed_password)
    print('converted User save')
    return user_in_db

@app.post("/user/", response_model=UserOut)
async def create_user(user_in: UserIn):
    user_saved = convert_save_user(user_in)
    return user_saved

### 
```

### Union

response 모델에 Union을 통해 두가지 타입 지정이 가능하다.

Union을 사용할 때, 첫 번째 타입은 두 번째 타입보다 중요하고 명시하고 싶은 타입 선언

ex. PlaneItem을 CarItem 보다 더 명시하고 싶음 

```python
class BaseItem(BaseModel):
    description: str
    type: str

class CarItem(BaseItem):
    type = "car"
    

class PlaneItem(BaseItem):
    type="plane"
    size: int


items = {
    "item1" : {"description": "drive a low rider", "type": "car"},
    "item2" : {"description": "plane", "type":"planedddd", "size":5}
}

@app.get("/items/{item_id}", response_model=Union[PlaneItem, CarItem])
async def read_item(item_id: str):
    return items[item_id]


```

http://localhost:8000/items/item2 접근하면 아래와 같은 결과를 얻을 수 있음

```python
{"description":"plane","type":"planedddd","size":5}
```
