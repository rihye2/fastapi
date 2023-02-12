## 사전정의 값

```
from typing import Optional
from fastapi import FastAPI
from enum import Enum

hrtestapp = FastAPI() #FastAPI Instance 생성

class Animal(str, Enum):
  cat = "cat"
  dog = "dog"
  lion = "lion"
  tiger = "tiger"
  rabbit = "rabbit"

@hrtestapp.get("/animals/{animal_name}")
async def getanimal(animal: Animal):
  #열거형 멤버 비교
  if animal == Animal.cat: #Animal.cat.value로도 접근이 가능
    return {"animal_name": animal, "type":"goyang-i"}
  if animal == Animal.rabbit:
    return {"animal_name": animal, "type":"tokki"}
  #열거형 값
  if animal.value == "dog":
    return {"animal_name": animal, "type":"gae"}
  if animal.value == "tiger":
    return {"animal_name": animal, "type":"horang-e"}

  return {"animal_name": animal, "type":"lion"} 

#path를 포함하는 경로 매개변수 선언 가능
#랴ㅣ
@hrtestapp.get("/files/{file_path:path}")
```


