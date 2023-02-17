##### Request Body 와 path, query parameter 전부 사용하기

(path parameter는 경로에서, model은 request body에서 받는다)

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()

@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item, q: Optional[str] = None) :
    result = {"item_id" : item_id,**item.dict()}
    if q:
        result.update({"q" : q})
    return result
```

경로에 parameter 존재:  path parameter로 인지

singular type: query parameter로 인지 (ex. int, str..)

Pydantic model type: request body



##### Query parameters and String Validations

```python
from typing import Optional
from fastapi import FastAPI


app = FastAPI()
@app.get("/items/")
async def read_items(q: Optional[str] = None):
    result = {"items":[{"item_id":"aa123"}, {"item_id":"bb123"}, {"item_id":"Test"}]}
    if q:
        results.update({"q":q})
    return results
```

다음과 같이 query parameter q에 Optional[str] = None을 줄 수 있음

- str 이거나 None으로 값을 지정할 수 있음(None을 지정하여 필수값이 아닌 것을 알 수 있음)



##### 글자수 제한 조건 추가 하기

```python
from typing import Optional
from fastapi import FastAPI, Query

app = FastAPI()
@app.get("/items/")
async def read_items(q: Optional[str]=Query(None, min_length=3, max_length=50, regex="^fixedquery$")):
    result = {"items":[{"item_id":"aa123"}, {"item_id":"bb123"}, {"item_id":"Test"}]}
    if q:
        results.update({"q":q})
    return results
```

Query를 import 하고, 안에 min_length, max_length로 최소, 최대 글자수를 지정할 수 있다

위 예시처럼 최소 3글자 보다 적거나, 최대 50자 보다 적을 경우 에러처리

Query('default_value')로 Query(None)과 같이 지정할 수 있음 (아래 두 문법은 가능 기능을 수행)

- async def read_items(q: str=Query("DefaultVal", min_length=3)):

```python
q: Optional[str] = Query(None)
q: Optional[str] = None
```



##### Regular expression 사용하기

Query(regex='')을 통해 parameter에 정규표현식 사용 가능

Query(regex='^fixedQuery$):  q는 fixedquery라는 문자열만 가능하다

-  '^' : 이 다음 단어로 시작해야 함, 이 앞에 단어가 있으면 안된다

- '$' : 문자열의 끝



##### query parameter를 필수값으로 받고 싶을 경우

```python
@app.get("/items/")
async def read_items(q: str=Query(...,min_length=3)):
    ...
```

앞선 예시처럼 q: int 를 통해 바꿀 수 있으나, Query를 통해서도 바꿀 수 있음

- Query(**...**, ) : 첫 번째 parameter에 ... 넣기




