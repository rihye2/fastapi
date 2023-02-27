```
docker run --rm -it \
  --name jupyter \
  --user root \
  --volume `pwd`/jupyter_docker:/host_dir \
  -e JUPYTER_ENV=TEST \
  -p 18888:8888 \
  jupyter/datascience-notebook
```

- 컨테이너 실행

```bash
docker run
```

[option]

**--rm**

컨테이너를 생성, 실행한 뒤 도커 컨테이너가 종료되면 해당 컨테이너가 자동 삭제

(-it 보통 이렇게 씀)

**-i**: 컨테이너의 표준 입력(stdin)을 활성화 

bash 명령 

**-t**: 

tty(가상 터미널)을 할당.  

Bash를 사용하려면 이 옵션을 설정

리눅스에 키보드를 통해 표준 입력(stdin)을 전달

**--name**

: 컨테이너 이름

**-u, --user**

컨테이너에서 사용될 계정이름 또는 UID를 설정.

**-v :** 데이터 볼륨 설정

(-v <호스트 절대경로>:<컨테이너 절대경로>)

호스트와 컨테이너의 디렉토리 연결(마운트) 

파일을 컨테이너에 저장하지 않고 호스트에 바로 저장 

-v /Users:/usr. - 컨테이너 /usr에 저장하는 파일은 호스트의 /Users 디렉토리에 저장.

'pwd'/jupyter_docker:/host_dir 

**-e(--env)**

컨테이너에서 사용할 환경변수 JUPYTER_ENV라는 환경변수는 TEST 값

**-p**

(-p <호스트 포트>:<컨테이너 포트>)

호스트os와 컨테이너의 포트를 연결 (포트포워딩).  
ex) -p 80:8888  - 호스트에 8888로 접속하면, 컨테이너 내부의 80포트로 자동 접속.

**jupyter/datascience-notebook**

이미지 name

- docker container 생성

```bash
docker create -i -t --name (컨테이너 이름) (이미지 이름)
```

- docker image 만들기 [docker build]
  
  - Dockerfile 텍스트 파일 생성(build시 사용될 명령어들을 모아놓음)
  
  - build context 는 `PATH` 또는 `URL` 을 통해 지정된 다수의 파일을 갖는 경로(위치)

build context는 이미지를 생성하는 데 필요한 각종 파일, 소스코드, 메타데이터 등을  담고 있는 디렉터리를 의미합니다. (build 명령어 제일 마지막에 폴더위치)

**도커파일(Dockerfile) 작성**

```bash
FROM [image:tag] #베이스 이미지 지정
```

```bash
COPY <src> <dest> #이미지에 파일 복
```

```bash
RUN <command> #명령어 실행
```

- 컨테이너가 실행될 때 명령어 수행

- 사용자 입력을 받지 않도록 유의 (apt install -y 등..)

- RUN apt-get install / RUN npm install

```bash
WORKDIR /path/to/workdir #작업 디렉토리 변경
```

```bash
EXPOSE <포트번호> #컨테이너에서 사용하는 포트정보
```

- EXPOSE 8000

```bash
CMD ["executable","param1","param2"]
```

- 컨테이너 매번 실행시 수행할 명령어. 하나의 CMD만 작성 가능

```bash
docker build . #.는 현재 이 경로 

#-f 옵션을 통해 build할 도커파일을 지정
docker build -f Dockerfile -t testCache[이미지명:버전] ./[Dockerfile이 있는 현재폴]
```

- docker image 만들기 [docker commit]

실행중인 container에서 image를 만들때 commit

- docker hub로부터 이미지 다운

```bash
docker pull [imagename:tag]
```

- 실행중인 컨테이너 리스트

```bash
docker ps 
```

- 실행했던 컨테이너 리스트

```bash
docker ps -a
```

- 컨테이너 생성 후 접속

```bash
docker run [repository]
```

- 컨테이너 접속

```bash
docker attach [containerID]
```
