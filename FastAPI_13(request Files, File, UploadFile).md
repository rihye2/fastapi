

### Request Files - File, UploadFile



File, UploadFile을 통해 파일 업로드 요청을 받을 수 있음

`pip install python-multipart`

```python
from fastapi import FastAPI, File, UploadFile

@app.post("/file")
async def create_file(file: bytes = File(default=None, description="file read by~")):
    if not file:
        return {"message": "no file sent"}
    else:
        return {"file_size": len(file)}

@app.post("/files")
async def create_files(files: List[bytes] = File(default=None)):
    return {"file_sizes": [len(file) for file in files]}

@app.post("/uploadfiles/")
async def create_upload_files(files: List[UploadFile]):
    return {"filenames " : [file.filename for file in files]}

@app.get("/")
async def main():
    content = """
<body>
<form action="/files/" enctype="multipart/form-data" method="post">
<input name="files" type="file" multiple>
<input type="submit">
</form>
<form action="/uploadfiles/" enctype="multipart/form-data" method="post">
<input name="files" type="file" multiple>
<input type="submit">
</form>
</body>
    """
    return HTMLResponse(content=content)
```



- **file: bytes = File()** 을 통해 파일을 요청받을 수 있음 (이렇게 명시해주지 않으면, 쿼리 파라미터로 인식 혹은 RequsetBody로 인식)

- description='file read by~' 와같이 metadata를 넣을 수 있

- 메모리에 저장되므로 파일이 작을수록 더 잘 동작

- **file: UploadFile** 을 사용할 경우, 파일 크기가 메모리 사이즈 이상을 넘어가면 디스크에 저장

- image, video, 큰 바이너리 파일에 적합

- default값 설정 가능 File(default=None)



#### Multiple File Upload

- 동일한 form field에서 form data를 받아야 함

- bytes 방식, UploadFile 방식 둘 다 가능

- http://localhost:8000/ 에 들어가면 form 양식이 뜨며, 여러 파일을 넣을 경우

- {"filenames ":["dd.jfif","화면 캡처 2022-06-26 010351.png"]} 와 같이 뜸


