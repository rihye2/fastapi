

```
@app.get("/hello") #hello라는 URL요청이 발생하면 해당함수를 실행
def hello():  
 return {"message":"안녕하세요"}
```

## 경로 매개변수, 타입이 있는 매개변수

```
from typing import Optional
from pydantic import BaseModel

hrtestapp = FastAPI() #FastAPI Instance 생성

class User(BaseModel):

    age : int

    name : str

    kms : Optional[bool] = None

@hrtestapp.get("/")
def read_root():

    return {"Im": "KMS~!"}

@hrtestapp.get("/user/{user_id}")
def read_item(user_id: int, name: Optional[str]=None):

    return {"user_id":user_id, "name":name}

@hrtestapp.put("/users/{user_id}")
def update_user(user_id: int, user: User):

    return {"user_name": user.name, "user_id":user_id}

@hrtestapp.get("/computers/{computer_id}")
async def read_com(computer_id: int):

    # int가 아닐경우 err

    # return {"computer_id: ": computer_id}

#/computers/hrcom 이라는 경로와 /computers/{computer_name}이라는 경로가 있을경우

#상세히 명시한 /computers/hrcom을 먼저 선언 그렇지않으면 {computer_name}에 매핑

@hrtestapp.get("/computers/hrcom")
async def read_comhr():

    return {"hrcomputer"}

@hrtestapp.get("/computers/{computer_name}")
async def read_com(computer_name: str):

    return {"computer_name: " : computer_name}
```

- read_comhr 먼저 선언할 경우
  
  - 확인: http://127.0.0.1:8000/computers/hrcom -> 결과: "hrcomputer"

- read_com 먼저 선언할 경우
  
  - 확인: http://127.0.0.1:8000/computers/hrcom -> 결과: {"computer_name: ": "hrcom"}


