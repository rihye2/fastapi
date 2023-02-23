DAO(Data Access Object)

- DB의 데이터에 접근하기 위해 생성하는 객체

- 프로젝트의 서비스 모델과 실제 데이터 베이스를 연결

- DB에 접근하기 위한 로직을 분리하기 위해 사용

- DAO를 통해 DB를 연결하여 데이터를 조회, 삽입, 삭제, 수정 등 의 작업(CRUD)



DTO(Data Transfer Object) 

- 계층 간 데이터 전달 역할(DB에서 data를 얻어 service, controller등으로 보낼 때 사용하는 객체), 데이터를 담아서 전달
  (service: 사용자에게 제공할 기능을 구현하는 클래스)

- 로직을 가지지 않는 데이터 객체

-  그러니까 어떤 메소드나 클래스간 객체 정보를 주고 받을 때 특정 모양으로 주고 받겠다는 약속(?)
  
  

파이썬에서는 아래와 같은 방식으로 DTO를 정의할 수 있음

dataclasses 

- 데코레이터를 통해 만들 수 있음

```python
>>> from dataclasses import dataclass
>>> @dataclass
... class UserDTO:
...     name: str
...     email: str
...     password: str
 
>>> user_dto = UserDTO(
...             name = "kim",
...             email = "kim@email.com",
...             password="123")
>>> print(user_dto)
UserDTO(name='kim', email='kim@email.com', password='123')
```



NamedTuple

```python
>>> from typing import NamedTuple
>>> class UserDTO(NamedTuple):
...     name: str
...     email: str
...     password: str
...
>>> user_dto = UserDTO("kim", "kim@email.com", "1234")
>>> print(user_dto)
UserDTO(name='kim', email='kim@email.com', password='1234')
```



Pydantic을 이용한 FastAPI Schema 만들기

```python
from datetime import datetime
from fastapi import FastAPI, Body, Query, status
from fastapi.responses import PlainTextResponse, JSONResponse
from typing import Optional
from pydantic import BaseModel, Field, EmailStr

class RequestUserDTO(BaseModel):
    nickname: str = Field(title='사용자 닉네임')
    email: EmailStr = Field(title='사용자 이메일')
    phone: str = Field(title='사용자 휴대폰 번호', regex="^(010)-([0-9]{4})-([0-9]{4})$",min_length=3)
    description: Optional[str] = Field(title='사용자 소개')

app = FastAPI()
@app.post(path='/register', description='회원가입 API',
            status_code=201,
            response_model=JSONResponse,
            responses={
                201: {
                    "description": "가입 사용자 정보",
                    "content":{
                        "application/json":{
                            "example":{
                                "nickname": "kim",
                                "email": "kim@email.com",
                                "phone":"010-1234-5676",
                                "description": "kim 입니다"
                            }
                        }
                    }
                }
            }
        )
def register_user(req: RequestUserDTO):
    return req.dict()
```
