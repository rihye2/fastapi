##### [Python] type hint 정리

python은 동적 프로그래밍 언어로,  변수의 타입을 고정시키지 않아도 되며, interpreter가 코드를 실행하면서 type을 추론하여 체크한다.

그러나 잘못된 타입을 사용하게 되면 안정성에 문제가 될 수 있다. 따라서 'typing hint(타입에 대한 메타정보를 제공'이라는 개발 프로세스를 도입하여 사용하며, 파이썬에서는 Typing (Type Hint)를 지원하고 있다.

##### type annotation

파이썬 3.5 부터 타입 어노테이션을 지원하지만, 정적 언어처럼 적극적인 type 체크가 아닌 타입 어노테이션으로 타입에 대한 힌트를 알려주는 정도

```python
def 함수명(<필수 인자>: < 타입 >, <선택 인자>: <타입> = <기본값>)
   -> <반환타입> :
```

```python
def func(a: int) -> int:#(반환값 또 -> 타입)
    b: int = 1 #변수에도 type hint 표기 가
    return a*b
```

그러나 type hint는 이러한 타입 제약 사항이 강요되는 것은 아니다. 즉, 변수에 추가한 type annotation을 무시해도 경고나 에러가 발생하는 것은 아님!

#### typing 모듈

typing 모듈은 int, str 보다 좀 더 복잡한 타입 어노테이션을 추가할 때 도와주는 라이브러리

##### List, Tuple, Set, Dict

- typing.List[<type>]

- typing.Set[<type>]

- typing.Tuple[<type>,<type>]

- typing.Dict[<key type>, <value type>]

```python
from typing import List, Tuple, Set, Dict

a: List[str] = ['a','b','c']
b: Set[str] = {"A","B","C"}

user: Tuple[int, str, bool] = (20, 'hr', True)

city: Dict[str, str] = {"Korea": "Seoul", "Japan": "Tokyo"}
```

##### Union

함수의 인자에 여러 타입이 허용될 수 있는 경우에 사용할 수 있다.

```python
from typing import Union
def func(a: Union[int, str]) -> str:
    return str(a)
```

##### Optional

None이 허용되는 함수의 인자에 타입을 명시할 때 사용할 수 있다. 
즉, Union[<type>, None]을 Optional로 사용할 수 있다.

```python
from typing import Optional
def func(project_id: Optional[str]) -> None:
    ...
```

##### Callable

함수에 대한 타입 어노테이션을 추가할 때 사용한다. 

Callable[[arg1Type, arg2Type], returnType]

Callable[[str], str] : str 타입의 인자를 하나 받아서 str로 리턴하는 함수

```python
def func1(a:int, b:str)-> str:
    ...
def fucn2(c: Callable[[int, str], str], age:int, name: str)->None:
    return func1(age, name)
```

##### Generator, Iterable, Iterator

함수가 generator 역할을 하는 경우에 사용한다. 

Generator[YieldType, SendType, ReturnType]

Iterable[YieldType]

Iterator[YieldType]

```python
def func() -> Generator[int, float, str]:
    a = yield 0
    while a >= 0:
        a = yield round(a)
    return 'end'
```

func()은 float 타입을 입력(send)받고, int 타입을 yield하는 generator다.

만약 음수값을 받는다면? StopIteration 이라는 예외와 함께 str 타입으로 return

(+) Generator는 yield만 하는 경우가 더 많다 -> Generator(YieldType, None, None)으로 명시 가능. 혹은,

Iterable[YieldType], Iterator[YieldType] 사용 

예시)

```python
from typing import Iterator

def func_test(val: int)-> Iterator[float]:
    for i in range(val):
        yield i
```

##### Any

어떠한 타입이든 상관이 없는 경우 (그러나 지양하는게 좋다고 한다)
