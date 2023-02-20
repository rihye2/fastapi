### [Airflow 기초] 왕초보의 튜토리얼



## 설치하기

Mac에서 진행하였고 콘다 환경을 생성하였다. 

- [파이썬은 3.8 버전, 가상환경명은 airflow_env ]

```bash
conda create -n airflow_env python=3.8
conda activate airflow_env
```

다음은 airflow를 설치할 차례

```bash
pip install apache-airflow
```

`airflow` 입력 → 사용가능한 명령어 설명들이 나옴 (성공!)



홈 디렉토리 에 airflow 라는 폴더가 생성되었다

airflow 로 이동 후, DB 초기화 실행

```bash
cd airflow
airflow db init
```



초기화 후, airflow.db 가 생성되었다.

- airflow.cfg: airflow 환경설정 파일

- airflow.db: DB 관련 정보를 담고 있음



dags 폴더를 생성 (dags 디렉토리에 dag들을 생성할 예정)

```bash
mkdir dags
```

에어플로우를 사용할 때 필요한 관리자 계정을 생성

```bash
airflow users create -u admin -p admin -f rihye -l tests  -r Admin -e admin@admin.com
```

`-u` : userid

`-p` : password  

`-f` : 사용할 이름

`-l` : 성

 `-e` : email



에어플로우 웹서버와 스케줄러를 켜보기.

먼저 웹서버를 한 터미널에서 실행(디폴트가 8080 포트이므로 8080 포트를 쓸 거면 `-p 8080`은 빼도 된다).

```bash
airflow webserver -p 8080
```



성공하면 위와같은 화면이 나온다



다른 터미널 창을 켜서 스케쥴러도 실행

```bash
airflow scheduler
```



`localhost:8080` 을 쳐서 웹서버에 접속하면 아래와 같은 화면이 뜨고 계정으로 로그인 해준다

![](/Users/hyeri/cloudai/fastapi/imgs/webserver.png)



예시 DAG중 실행, 종료, 기록 등을 테스트해보자

![](/Users/hyeri/cloudai/fastapi/imgs/dag_webserver2.png)

예시 `example_branch_datetime_operator_2`  를 클릭.. 

상단  `▶︎`  클릭 ` Trigger DAG ` 선택하여 실행

- 긴 진한 초록색 bar : DAG가 성공적, 2번 실행되었음(Total success) 

- 우측에 DAG에 대한 여러가지 실행 기록들을 볼 수 있음

- 정지하고 싶을 때, 상단 DAG:exaple_branch_datetime_oprator2 옆 하늘색 상단버튼으로 재개/정지 가능



Graph 클릭을 하면 DAG 순서도를 볼 수 있고 어떤 타입의 Operator를 사용했는지에 대한 정보도 뜬다!

![](/Users/hyeri/cloudai/fastapi/imgs/dag_webserver1.png)



이러한.. DAG 파이프라인을 잘 설계해야하는데 예제를 찾아봐야겠다.


