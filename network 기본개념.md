#### network 기본개념

 

**host:** 네트워크/인터넷에 연결된 장치(어떠한 device, pc나 스마트폰이 될 수 있음)

- host는 IP주소를 가짐으로써 다른 host와 데이터를 주고 받을 수 있으며, 자신을 구분할 수 있음

- IP주소를 사용하여 통신할 수 있는 것들

- localhost:8000 (이때 localhost는 host 자신을 가리키는 고유 별칭이라고 생각하면 된다.)



**hostname:** 네트워크에 연결된(IP주소를 가지고 있는) device에 할당되는 고유 이름
                       도메인 네임 중 IP주소를 설정할 수 있는 이름이 host name
                        host name은 domain name의 유형중 일부분?

**domain name:** 네트워크상에서 컴퓨터를 식별하는 hostname, 원하는 호스트를 가리키는 이름

    - www[hostname]

    - mail[hostname].naver.com[domain name]

    - en[hostname].wikipedia.org[domain name]

+) 메일: mail.naver.com, 웹툰: comic.naver.com 각각 해당 주소를 사용하는데 여기서 mail과 comic은 naver.com이라는 도메인 네임에서 각각의 서비스를 구분하기 위한 hostname

또한, 네트워크 안에서 IP address가 아닌 별칭을 사용하기 위해 hostname과 IP를 관리해주는 테이블이 존재

네트워크의 gateway(router)를 통해 대상지에 대한 IP와 hostname을 관리

내부 네트워크를 이용하는 경우 별도로 host name을 등록하고 IP주소가 아닌 hostname을 통해 ssh접속 또는 ping 테스트를 할 수 있다



#### Port forwarding

특정 포트로 들어오는 패킷을 다른 포트로 바꿔서 재전송

외부에서 직접 접근이 불가능한 내부 네트워크에 있는 호스트와 연결을 중계할 수 있음



포트포워딩을 설정하면, 충돌을 방지할 수 있다.

예를 들면, 포트 80번을 설정하고 A 기기에 접속하려고 한다면, 80번 포트와 겹치는 B, C 기기 등 포트 번호가 겹치게 되어 혼선이 발생한다. 

따라서 포트포워딩 설정으로 port 180으로 진입하게 될 경우, A기기의 port 80으로 요청을 보내라. 고 지시를 해두는 것(이러한 숫자를 지정하는 것이 포트포워딩 설정)


