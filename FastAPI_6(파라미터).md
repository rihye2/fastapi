##### Query parameter list

- Query Parameter 는 list를 지원

```python
from fastapi import FastAPI, Query
app = FastAPI()

@app.get("/items/")
async def read_itmes(q: "list[str] | None" = Query(None)):
    query_items = {"q":q}
    return query_items


```

```bash
{
  "q": [
    "test",
    "test3",
    "test22",
    "string"
  ]
}
```



```context
http://127.0.0.1:8000/items/?q=testquery1&q=testquery2
{"q":["testquery1","testquery2"]}
```



list의 default 값을 지정할 수 있다

```python
async def read_items(q: list[str] = Query(["test1","test2"))
```



##### metadata 추가

- title, description 키워드로 쿼리에 대한 설명을 추가할 수 있음(metadata)

- title: 사용 API 이름

- description: 사용 API 설명

```python
from fastapi import FastAPI, Query

app = FastAPI()

@app.get("/items/")
async def read_items(
    q: "str | None" = Query(None, title="Query string",
                        description="query string for the items",
                        min_length=3,)
):
    results = {"items": [{"item_id": "id1"}, {"item_id": "id2"}]}
    if q:
        result.update({"q":q})
    return results
```



##### include_in_schema

```python
@app.get("/items/")
async def read_items(hidden_q: "str | None" = Query(None, include_in_schema=False)):
    if hidden_q:
        return {"hidden_q": hidden_q}
    else:
        return {"hidden_q": "Not found"}
```


