### Middleware

특정 path에 따른 모든 요청/응답을 처리하기 전에 수행할 함수

- 모든 request에 대해 path operation이 수행되기 전 실행되는 함수

- response에 대해서도 response를 return 해주기 전에 실행되는 함수 또한 middleware



아래의 데코레이터를 사용해 미들웨어를 만들 수 있음

```python
@app.middleware("http")
```

```python
import time
from fastapi import FastAPI, Request


app = FastAPI()

@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    response.headers["X-rocess-Time"] = str(process_time)
    return response


```



middleware가 받는 파라미터

- request

- call_next: 함수, request를 parameter로 받음
  request를 path operation에 넘김
  reponse를 리턴





#### Middleware 추가

```python
from unicorn import UnicornMiddleware

app.add_middleware(UnicornMiddleware, som_config="rainbow")
```


