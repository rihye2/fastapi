### Example Data 넣기

docs에서 Json schema에 대해 추가적인 정보를 적어줄 수 있다.

- pydantic 모델에서 Config에 schema_extra를 정의

- Field, Path, Query, Body에도 똑같이 example을 추가 가능



```python
app = FastAPI()

class Item(BaseModel):

    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None
    
    class Config:
        schema_extra = {
            "example": {
                "name": "Foo",
                "description": "item",
                "price": 12.2,
                "tax":1.2
            }
        }

@app.put("/item/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results
```



#### Field에 example 추가

```python
class Item(BaseModel):

    name: str = Field(..., example="Foo")
    description: Optional[str] = Field(None, example="item example")
    price: float = Field(..., example=12.21)
    tax: Optional[float] = Field(None, example=1.1)
```

```json
{
  "name": "Foo",
  "description": "item example",
  "price": 12.21,
  "tax": 1.1
}
```



#### Body에 example 추가

```python
@app.put("/items/{item_id}")
async def update_item(
    item_id: int,
    item: Item = Body(
        ...,
        example={
            "name": "Foo",
            "description": "A very nice Item",
            "price": 35.4,
            "tax": 3.2,
        },
    ),
):
    results = {"item_id": item_id, "item": item}
    return results
```



마찬가지로 /docs에 아래처럼 example이 미리 설정되어 있음

```json
{
  "name": "Foo",
  "description": "A very nice Item",
  "price": 35.4,
  "tax": 3.2
} 
```



example을 여러개 사용하고 싶을 경우, examples를 사용하면 된다.

dict형으로 작성하면 되며, 추가적은 데이터를 담을 수 있음

- summary: example에 대한 간단한 설명

- description: example에 대한 긴 설명, 구두점 . 을 포함 가능

- value: 실제 example 값을 담는 곳



다음은 example과 examples를 쓸 수 있다

- Path, Query, Header, Cookie, Form, File



```python
class Item(BaseModel):

    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

@app.put("/item/{item_id}")
async def update_item(
    *,
    item_id: int,
    item: Item = Body(...,
        examples={
            "normal": {
                "summary": "A normal example",
                "description": "A normal item works correctly.",
                "value": {
                    "name": "Foo",
                    "description": "A very nice Item",
                    "price": 35.4,
                    "tax": 3.2,},},
            "converted": {
                "summary": "An example with converted data",
                "description": "FastAPI can convert price `strings` to actual `numbers` automatically",
                "value": {
                    "name": "Bar",
                    "price": "35.4",},},},),):
    results = {"item_id": item_id, "item": item}
    return results

```



/docs에 Examples에 항목이 생김(summary에 적은 설명으로 명시된다.)

- A normal example

- An example with converted data



또한, request Body에 price가 string에서 float로 변경된 것을 볼 수 있음

```json
{
  "item_id": 22,
  "item": {
    "name": "Bar",
    "description": null,
    "price": 35.4,
    "tax": null
  }
}
```


