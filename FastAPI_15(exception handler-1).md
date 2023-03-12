### Handling Errors(1)

- 클라이언트가 접근할 수 없는 자원에 접근

- 존재하지않는 자원에서 접근을 원함

- 권한이 없는 경우 



에러를 내야 하는데 일반적으로 400범위의 에러를 내서 문제가 있음을 클라이언트에게 알림



#### HttpException

item_id를 보냈는데 해당 item이 존재하지 않을 경우, 404 에러 발생시키는 예제

에러를 발생시키기 위해서 `raise`를 이용

item_id 에 "aa"가  아닌 다른 item_id를 넣으면 404 에러가 발생한다. 

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"aa" : "item_id aa"}

@app.get("/items/{item_id}")
async def read_item(item_id : str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found",
                headers={"error":"404 error test"})
    return {"item": items[item_id]}


```



또한, `headers=`를 통해 HTTP error를 추가할 수 있음



#### Custom exception handlers

```python
from fastapi import FastAPI, HTTPException, Request
from fastapi.responses import JSONResponse

app = FastAPI()

items = {"aa":"test code"}

class CustomException(Exception):
    def __init__(self, name: str):
        self.name = name

@app.exception_handler(CustomException)
async def unvicorn_exception_handler(request: Request, exc: CustomException):
    return JSONResponse(
        status_code=418,
        content={"message": f"{exc.name} ..."}
    )

@app.get("/unicorns/{name}")
async def read_unicorn(name: str):
    if name == "yolo":
        raise CustomException(name=name)
    return {"unicorn_name": name}
```



name에 "yolo"를 입력하여 요청 보내면 에러가 발생, 다음과 같이 설정한 에러메세지 확인 가능 

```json
{
  "message": "yolo ..."
}
```


