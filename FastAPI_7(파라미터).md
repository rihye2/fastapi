##### Path parameter

: 경로 매개변수로 들어올 때 처리하는 방법,  `Path` 를 import 한다

path parameter는 경로의 일부이므로 필수값이다. `...` 을 통해 필수임을 나타내는것이 좋음



**metadata 선언**

- Query와 똑같이 title에 metadata를 넣어준다

```python
from typing import Optional
from fastapi import FastAPI, Path, Query
app = FastAPI()

@app.get("/items/{item_id}")
async def read_items(
    item_id: int = Path(..., title="get item ID"),
    q: Optional[str] = Query(None, alias="item-query"),
):
    results = {"item_id": item_id}
    if q:
        results.update({"q":q})
    return results
```



##### Number validations

- Query, Path를 사용하여 제약조건을 걸 수 있음

- ge: greater than or equal

- gt: greater than

- lt: less than

- le: less than or equal



item_id가 1보다 크거나 같다 라는 제약 조건일 때

```python
from fastapi import FastAPI, Path
app = FastAPI()

@app.get("/items/{item_id}")
async def read_items(
    *, item_id: int = Path(..., title="get item ID", ge=1),q: str
):
    results = {"item_id": item_id}
    if q:
        results.update({"q":q})
    return results
```

```python
# 0보다 크고 100보다 작거나 같다
async def read_items(
    *, item_id: int = Path(..., title="get item Id", gt=0, le=100),
    q:str,
):
```



Query문을 이용하여 float 자료형에 10.5보다 작다는 제약 조건일 때

```python
async def read_items(
    *,
    item_id: int = Path(..., title="get item ID", ge=0, le=100),
    q: str,
    size: float = Query(..., gt=0, lt=10.5)
):
```


