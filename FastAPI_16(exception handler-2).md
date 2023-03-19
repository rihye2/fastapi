### Handling Errors (2) - Override default exception handler

- 유효하지 않은 요청을 받을 때, raise를 통해 default JSON Reponses를 포함한 HTTPException 을 발생 시킬 수 있음

- 유효하지 않은 데이터가 요청에 포함되어 있을 경우 내부적으로 RequestValidationError를 발생시킴

- 이 때, RequestValidationError exception에 대한 핸들러를 override 하는 예제

아래 처럼 @app.exception_handler 에 원하는 handler를 등록해주면 된다.

```python
from fastapi import FastAPI, HTTPException
from fastapi.exceptions import RequestValidationError
from fastapi.responses import PlainTextResponse

app = FastAPI()


@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request, exc):
    return PlainTextResponse(str(exc), status_code=400)


@app.get("/items/{item_id}")
async def read_item(item_id: int):
    if item_id == 3:
        raise HTTPException(status_code=418, detail="exception: item id 3")
    return {"item_id": item_id}
```

- http://localhost:8000/items/f

item_id가 int가 아닌 다른 자료형을 보낼 경우, 아래와 같이 뜸!

```
1 validation error for Request
path -> item_id
  value is not a valid integer (type=type_error.integer)
```

헤더 정보에도 status code 400인 것을 확인할 수 있음!

#### Override the HTTPException error handler

```python
from starlette.exceptions import HTTPException as StarletteHTTPException
app = FastAPI()

@app.exception_handler(StarletteHTTPException)
async def http_exception_handler(request, exc):
    return PlainTextResponse(str(exc.detail), status_code=exc.status_code)
```

마찬가지로 http://localhost:8000/items/f

요청을 보낼 경우

```
{"detail":[{"loc":["path","item_id"],"msg":"value is not a valid 
integer","type":"type_error.integer"}]}
```

헤더정보를 보면, status code 422 Unprocessable Entity 를 확인할 수 있음

#### RequestValidationError body

RequestValidationError는 body속성을 가지고 있는데 

body는 에러가 난 데이터를 가지고 있다

```python
from fastapi import FastAPI, HTTPException, Request, status
from fastapi.exceptions import RequestValidationError
from fastapi.responses import PlainTextResponse, JSONResponse
from starlette.exceptions import HTTPException as StarletteHTTPException
from fastapi.encoders import jsonable_encoder
from pydantic import BaseModel

app = FastAPI()




@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request: Request, exc: RequestValidationError):
    return JSONResponse(
            status_code=status.HTTP_422_UNPROCESSABLE_ENTITY,
            content=jsonable_encoder({"datail": exc.errors(), "body":exc.body})

    )

class Item(BaseModel):
    title: str
    size: int

@app.post("/items/")
async def create_item(item: Item):
    return item
```

request body에 다음과 같이 요청을 보낼 경우

```
{
  "title": "aaa",
  "size": "bd"
}
```

error code: 422, Unprocessable Entity

body에 보내진 데이터가 함께 출력 

```json
{
  "datail": [
    {
      "loc": [
        "body",
        "size"
      ],
      "msg": "value is not a valid integer",
      "type": "type_error.integer"
    }
  ],
  "body": {
    "title": "aaa",
    "size": "bd"
  }
}
```





#### + HTTP status code

- 200단위: OK를 의미, 요청에 대한 처리 성공
  
  - 201: "Created": Database에 새로운 레코드를 생성할 때 사용
  
  - 204: "No content" 클라이언트에게 돌려줄 데이터가 없음, response body를 가질 수 없음

- 300단위: "Redirection"
  
  - 304: "Not Modified" : response body를 가질 수 없음

- 400단위: "Client error"
  
  - 400: 클라이언트 측 요청에 문제가 있음
  
  - 404: "Not Found"

- 500: 서버 에러 
