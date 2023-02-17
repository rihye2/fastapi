### query 매개변수

```python
query_param_app = FastAPI()
dia_bemember_db = [{"name":"hrLee"}, {"nam":"gg"}, {"name":"yjkim"},{"name:":"WW"}]

@query_param_app.get("/members/")
async def read_mem(skip: int=0, limit:int = 10):
    return dia_bemeber_db[skip: skip+limit]
```

- 이 예제에서 ‘default’값인

- http://127.0.0.1:8000/members는

- http://127.0.0.1:8000/members/?skip=0&limit=10 와 같은 파라미터로 처리됨. 

- &을 구분자로 ?을 통해 다음 문구가 쿼리파라미터라는 것을 알려줌

# 

### 선택적 매개변수

```python
#optional 을 통해 매개변수 필수값 여부 설정 가능
from typing import Optional
from fastapi import FastAPI

query_param_app = FastAPI()

@query_param_app.get("/members/{member_name}")
async def read_memone(member_name: str, member_age: Optional[int]=None):
    if (member_age):
        return {"name": member_name, "age: ":member_age}
    return {"name": member_name}

#http://127.0.0.1:8000/members/hrL
#http://127.0.0.1:8000/members/hrL?member_age=25
```

- member_age가 Optional = None 이므로 필수값이 아님 위와같은 1번 요청에도 성공

- Optional[int] 로만 지정하고 1로 접근 -> member_age 입력하라고 나옴 

# 

### 쿼리 매개변수 형변환

```python
from typing import Optional
from fastapi import FastAPI

query_param_app = FastAPI()
@query_param_app.get("/members/{member_name}")
async def read_membool(member_name:str, query: Optional[str] = None, ishrL: bool=False):
    name = {"member_name": member_name}
    if query:
        name.update({"query":query})
    if not ishrL:

        name.update(
            {"description": "ishrL is false?"}
    return name
)
```

- http://127.0.0.1:8000/members/hrL:ishrL=1

- http://127.0.0.1:8000/members/hrL:ishrL=True

- http://127.0.0.1:8000/members/hrL:ishrL=true

### 여러 경로 매개변수

```python
@query_param_app.get("/members/{member_name}/items/{item_id}")
async def read_membool2(member_name: str, item_id: str, query: Optional[str] = None, ishrL:bool=False):
    name = {"member_name":member_name, "have item_id":item_id}
    if query:
        name.update({"query":query})
    if not ishrL:
        name.update(
            {"description":"ishrL is false?"}
        )
    return name
```

- http://127.0.0.1:8000/members/hrL/items/123?ishrL=ture

### 여러 쿼리 매개변수

```python
from typing import Optional
from fastapi import FastAPI

query_param_app = FastAPI()
@query_param_app.get("/members/{member_name}")
async def read_member(member_name: str, ishrL: bool):
    member = {"name": member_name, "ishrL?":ishrL}
    return member
```

- http://127.0.0.1:8000/members/hrL  -> error 

- {"detail":[{"loc":["query","ishrL"],"msg":"field required","type":"value_error.missing"}]}

- http://127.0.0.1:8000/members/hrL?ishrL=y
