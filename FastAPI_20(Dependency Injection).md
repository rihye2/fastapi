### Dependency Injection

코드의 재활용을 위해 제공해주는 기능

- 로직 공유

- 데이터베이스 커넥션 공유

- 보안, 인증, 업무 규칙 



예제

```python
from fastapi import FastAPI, Depends
from typing import Optional, List

app = FastAPI()

async def common_parameters(q:Optional[str] = None, 
                            skip: int=0, limit: int=100):
    return {"q": q, "skip": skip, "limit":limit}

@app.get("/items/")
async def read_items(commons: dict=Depends(common_parameters)):
    return commons

@app.get("/users/")
async def read_users(commons: dict=Depends(common_parameters)):
    return commons
```

- common_parameters -> /items/ , /users/

- items와, users 컨트롤러는 common_parameters 함수에 의존한다. 
  즉, 함수가 바뀌면 두 컨트롤러 또한 바뀐다. 
1. 설정한 dependency를 부르고 parameter가 맞게 설정되었는지 확인

2. 해당 함수에서 설정한 리턴값을 가져옴

3. 리턴값을 인자로 설정한 값인 commons에 주입





#### Class dependency

클래스의 생성자는 함수처럼 사용이 가능 따라서 클래스도 dependency가 가능하

commons: CommonQueryParams = Depends(CommonQueryParams)를 통해 의존성 설정할 수 있다

설정된 클래스를 호출하고, 인스턴스를 생성하여 commons라는 파라미터에 넘김 

```python
from fastapi import FastAPI, Depends
from typing import Optional, List

app = FastAPI()

example_db = [{"item_name": "abc"}, {"item_name": "test2"}, {"item_name":"test3"}]


class CommonQueryParams:
    def __init__(self, q:Optional[str]=None, skip:int=0, limit:int=10):
        self.q = q
        self.skip = skip
        self.limit = limit


@app.get("/items/")
async def read_items(commons: CommonQueryParams=Depends(CommonQueryParams)):
    response = {}
    if commons.q:
        response.update({"q":commons.q})
    items = example_db[commons.skip: commons.skip + commons.limit]
    response.update({"items":items})
    return response

```



#### sub dependency

dependency를 원하는 만큼 deep하게 만들 수 있음







```python
from fastapi import FastAPI, Depends, Cookie
from typing import Optional, List

app = FastAPI()

def query_extractor(q: Optional[str]=None):
    return q

def query_or_cookie_extractor(q:str=Depends(query_extractor),
                              last_query:Optional[str]=Cookie(None)):
    if not q:
        return last_query
    return q
#마지막 path operation에 연
@app.get("/items/")
async def read_query(query_or_default: str = Depends(query_or_cookie_extractor)):
    return {"q_or_cookie": query_or_default}
```



#### Path Operation Decorator's Dependency

컨트롤러가 return값을 반환하지 않거나, 의존성함수가 return을 하지않는 경우 존재

데코레이터에 dependencies 파라미터에 Depends를 list 형태로 넘겨준다.

verify_key dependency를 보면, return x_key를 하지만 path operation에 전달되지 않는다.

```python
from fastapi import FastAPI, Depends, Header, HTTPException
from typing import Optional, List

app = FastAPI()

async def verify_token(x_token: str = Header(...)):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")

async def verify_key(x_key:str = Header(...)):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="X-key header invalid")
    return x_key

@app.get("/items/", dependencies=[Depends(verify_token), Depends(verify_key)])
async def read_items():
    return [{"item": "Foo"}, {"item":"bar"}]

```



#### Global Dependencies

의존성을 애플리케이션 전체에 적용하는 방법

모든 요청에 대해서 적용된다.

```python
async def verify_token(x_token: str = Header(...)):
    if x_token != "fake-super-secret-token":
        raise HTTPException(status_code=400, detail="X-Token header invalid")

async def verify_key(x_key:str = Header(...)):
    if x_key != "fake-super-secret-key":
        raise HTTPException(status_code=400, detail="X-key header invalid")
    return x_key
app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)])

@app.get("/items/")
async def read_items():
    return [{"item": "Foo"}, {"item":"bar"}]

@app.get("/users")
async def read_users():
    return [{"username":"Rick"}, {"username":"morty"}]
```


