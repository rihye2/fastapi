[Airflow 실습] basic tutorial

*공식문서에* https://airflow.apache.org/docs/apache-airflow/2.1.4/tutorial.html 

*튜토리얼이 존재한다. 이거 하나만으로는 이해가 매우 부족할 듯 하여 해당 예제를 기점으로 여러 예제를 테스트 해보는게 좋을 것 같다)*

```python
from datetime import timedelta
from textwrap import dedent

from airflow import DAG

from airflow.operators.bash import BashOperator
from airflow.utils.dates import days_ago


#Task생성할때 사용할 기본 매개변수 Dict 정의

default_args = {
    'owner':'airflow',
    'depends_on_past':False,
    'email': ['riheein@gmail.com'],
    'email_on_failure': False,
    'email_on_retry': False,
    'retries':1,
    'retry_delay':timedelta(minutes=5)   
}

#작업을 연결할 DAG객체 생성
with DAG('tutorial', #객체에 수많은 dag를 구별할 고유한 식별자 dag_id를 문자열로 정의
         default_args=default_args,#argument dictionary
         description= 'simple tutorial DAG',
         schedule_interval=timedelta(days=1), #dag 실행간격
         start_date=days_ago(2), #dag가 시작되는 기준 시점
         tags=['example'],
         ) as dag:
    #Task 인스턴스 생성 (Operator의 인스턴스)
    #Task는 반드시 task_id와 owner argument를 포함하거나 상속받아야 한다
    task1 = BashOperator(
        task_id='print_date',
        bash_command='date'
    )

    task2 = BashOperator(
        task_id='sleep',
        depends_on_past = False,
        bash_command='sleep 5',
        retries=3,
    )
    templated_command = dedent(
        """
    {% for i in range(5) %}
        echo "{{ ds }}"
        echo "{{ macros.ds_add(ds, 7)}}"
        echo "{{ params.my_param }}"
    {% endfor %}
        """
    )

    task3 = BashOperator(
        task_id = 'templated',
        depends_on_past = False,
        bash_command=templated_command,
        params={'my_param': 'Parameter I passed in'},
    ) 

    #의존관계 정의
    #task method : set_downstream & set_upstream
    #연산자: >> & <<
    task1.set_downstream(task2)

    task2.set_upstream(task1)

    task1 >> task2
    task2 << task1

    task1 >> task2 >> task3

    task1.set_downstream([task2, task3])
    task1 >> [task2, task3]
    [task2, task3] << task1
```

##### DAG 구조 확인

`airflow dags show <DAG_ID>` : 어떤 Task로 구성되어 있고, Task간 의존성 확인 가능

- `airflow dags show tutorial`

```bash
digraph tutorial {
        graph [label=tutorial labelloc=t rankdir=LR]
        print_date [color="#000000" fillcolor="#f0ede4" label=print_date shape=rectangle style="filled,rounded"]
        sleep [color="#000000" fillcolor="#f0ede4" label=sleep shape=rectangle style="filled,rounded"]
        templated [color="#000000" fillcolor="#f0ede4" label=templated shape=rectangle style="filled,rounded"]
        print_date -> sleep
        print_date -> templated
        sleep -> templated
}
```

##### Dag의 Task 목록 확인

`airflow tasks list <DAG_ID> -t` (트리 형태로 확인하고 싶을 때)

- `airflow tasks list tutorial --tree` : DAG 의존성 정보 확인

```bash
<Task(BashOperator): print_date>
    <Task(BashOperator): sleep>
        <Task(BashOperator): templated>
    <Task(BashOperator): templated>
```

airflow는 Jinja Template, 사전 정의된 Parameters, Macro를 지원한다.

Jinja Templating을 사용하면, Template이 rendering될 때 변수 및 표현들을 실제 값으로 반환한다.

##### Jinja template 란?

- 간단한 문법으로 html에서 {}, {{}} 등의 규칙을 이용하여 python 프로그래밍이 가능함

- 데이터를 담은 변수를 return 값으로 html과 함께 넘겨준 뒤, html에서 jinja 템플릿 규칙에 따라 화면에 표현할 수 있다

- jinja template html에서 주석은 {# 주석 처리하고 싶은 내용 #}으로 표시

- 변수를 사용하고 싶을 때는 {{변수명}}

- 반복문을 사용하고 싶을 때는 {% for %} ~ {% endfor %} 사이에 html코드와 변수를 위치시키면 for문 처럼 사용할 수 있다. (들여쓰기는 신경쓰지 않아도 된다고 함)

- 조건문: {% if %}{% elif %}{% else %} ~{% endfor %} endfor로 조건문 종료









 The four F's of active reviewing (오늘도 부끄러운 4줄)

facts:  airflow 의 아주 기초적인 개념정도를 봤으니, DagGenerator가 어떻게 작동하는지 코드를 뜯어봐야한다.

feelings: 산넘어 산.. 그래도 개념을 봤다고 동공지진이 오진 않았음. 가장 빠르게 이해하기 위해서는 예제를 따라해보는게 최고인 것 같다. 

findings: Operator 한번 써봤는데.. 아직 감이 안 온다. 굉장히 다양한 Operator가 존재하는 것 같다. 

우리팀 코드를 보니 dummy, kubernetes 등을 사용한게 보인다. 

futures: basic 예제를 따라한 정도라 좀 더 여러 예제들을 테스트해보는게 좋을 것 같다


