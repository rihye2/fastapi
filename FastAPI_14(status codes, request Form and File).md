

### FastAPI 응답코드 설정, status codes

`status_code`를 사용해 HTTP 상태값을 반환할 수 있음

`fastapi.status` 를 통해 편의성을 제공한다.

```python
from fastapi import Body, FastAPI, status

app = FastAPI()

@app.post("/items/", status_code=status.HTTP_201_CREATED)
async def create_item(name: str):
    return {"name": name}



```

status_code = 201 보다 직관적

- `201 Create` : The `POST` request was successful and the resource is returned as JSON.

- `200 OK` : The `GET`, `PUT` or `DELETE` request was successful, the resource(s) itself is returned as JSON.

- `401 Unauthorized` : The user is not authenticated, a valid [user token](https://velog.io/@kjh03160/Fast-API-%EC%9D%91%EB%8B%B5-%EC%BD%94%EB%93%9C-%EC%84%A4%EC%A0%95#authentication) is necessary.

- `403 Forbidden` : the request is not allowed. For example, the user is not allowed to delete a project.



### Request Forms and Files

File과 Form을 같이 받을 수 있음

```python
from fastapi import Body, FastAPI, Form, File, UploadFile


app = FastAPI()

@app.post("/files/")
async def create_item(file: bytes=File(), fileb: UploadFile=File(), token:str=Form()):
    return {
        "file_size": len(file),
        "token": token,
        "fileb_content_type": fileb.content_type
    }


```


