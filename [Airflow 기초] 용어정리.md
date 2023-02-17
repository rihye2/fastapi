#### [Airflow 기초] 용어정리

workflow를 관리하는 툴로 (관리한다? 워크플로우작성, 스케줄링, 모니터링하는 작업들)

python 프레임워크를 이용하여 developing, scheduling, monitoring을 할 수 있는 플랫폼이다.

[공식문서 - https://airflow.apache.org/docs/apache-airflow/stable/core-concepts/index.html]



*그렇다면 왜? airflow를 사용할까*

기존에는 다음과 같은 문제점이 존재

- 실패한 배치에 대해서 복구가 어려움(기존 작업들이 실패했을 때, 다시 스크립트를 실행하려면 로그를 확인하고 실행해야 하는 등의 문제점.. 어디서 부터 잘못됐는가 확인도 어려움)

- 의존성 관리가 힘듦(스크립트들이 서로에 대한 의존성이 존재할 경우 관리가 어려움)  

- 모니터링이 어려움

- 이러다보니 새로운 workflow를 배포하기 힘들다는 단점이 존재



**Airflow 장점** (Airflow가 이러한 단점들을 해결해 주었다)

- 의존성을 표현할 수 있다: 의존성이 생기게 되면 컨트롤하기 어렵다(어디서 잘못되었는지 확인이 어렵고, 복구에도 문제가 생김!)

- 문제가 생겼을 때 이전 버전으로 롤백할 수 있도록 workflow를 버전 제어에 저장할 수 있다

- 검증을 위한 테스트를 작성할 수 있다



*workflow는 무엇인가?*

가장 작은 단위의 Operator들이 모여 Task가 되며, Task가 모여 DAG,DAG가 모여 workflow가 되는것이다.

###### Airflow 구성요소

![](/imgs/airflow.png)

- **scheduler:** DAG와 작업들을 모니터링하고 실행순서와 상태를 관리
  (airflow로 할당된 work들을 스케쥴링, executor에게 task를 제공해줌)

- **executor:** 실행 중인 task를 처리. 스케쥴러와 함께 동작, 실행중인 task를 handling한다.

- **webserver:** User Interface를 제공\. (DAG 및 task의 동작을 검사, trigger, debug) 
  (airflow의 로그를 보여주거나, 스케쥴러에 의해 생성된 DAG 목록과 Task 상태를 시각화)

- **DAG Directory:** task schedule을 만들어주는 스케쥴링 단위를 작성한 DAG를 보관하는 장소

- **Metadata Database**: airflow에 있는 DAG, Task 등의 metadata를 저장, 관리

- **Worker**: 실제 task를 수행하는 주체



###### DAG란 ?

- Directed Acyclic Graph(비순환 구조 그래프, 싸이클이 없다. 즉, 단방향)

- airflow에서는 하나의 작업 flow를 DAG라고 한다

- 관계 의존성을 그래프로 그려주어 사용자로 하여금 flow를 잘 파악할 수 있도록 도와준다

- DAG를 이용해 workflow를 구성하여 어떤 순서로 task를 실행시킬 것인지 설정

- 작업을 정의한 것

- ">>(downStream)", "<<(upStream)" 등으로 stream을 정의



###### Task란?

- 가장 작은 작업 단위

- Task는 Operator 또는 Sensor로 정의

- 하나 또는 여러개의 Task를 이용해 하나의 DAG를 생성

- 여러 Task의 순서와 저건을 정의해서 DAG를 생성한다.



###### Operator란?

airflow는 Operator를 사용하여 python, bash, aws, slack등 다양한 동작 실행시킬 수 있음

예를 들어,

-  python 코드를 실행시키는 PythonOperator

- bash 스크립트를 실행할 수 있는 BashOperator

- k8s 파드를 하나 띄울 수 있는 KubernetesPodOperator

- BaseOperator를 상속해서 customized operator 정의도 가능

예시)

```python
exp_task = BashOperator(
    task_id='exp_task',
    bash_command="operator example code",
    dag=dag
)
```

airflow에는 여러 Operator가 존재한다

- Action Operator: action 실행

- Transfer Operator: 데이터 이동

- Sensor: 조건이 맞을때까지 대기



###### Sensor 란?

Operator처럼 Task로 사용할 수 있다. 특정 조건을 만족하는 경우 이후 Task로 넘어가게 하는 역할

- 특정 조건을 검사해서 해당 조건을 Pass할때까지 끝나지 않는 Task를 생성할 수 있음

- 예를 들어, timeout을 지정해서 시간 내 pass가 안 되는 경우 fail 시킬 수 있음



따라서, Airflow는 어떻게 동작하는가?

###### Airflow 동작방식

- Task로 구성된 DAG를 작성하여 workflow를 생성

- Task는 Operator의 인스턴스화 (ex. bashOperator, pythonOperator...)

- Scheduler가 DAGRun 오브젝트를 만들어 DAG를 실행

- DagRun 오브젝트는 Task instance를 생성

- Worker가 Task를 수행

- 수행 후, DagRun을 완료로 변경



###### DagRun이란?

DAG에서 정의한 작업을 동작하는 instance다

동시 최대 몇개 DagRun을 돌게 허용할 것인지 세팅도 가능하다(max_active_run)



다음은 실습을 해보자!
