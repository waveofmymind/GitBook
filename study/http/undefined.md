# 인터넷 네트워크

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

위처럼, 내가 쓴 코드를 서버로 전달하기 위해서는 클라이언트와 서버의 IP 주소를 사용한다.

그리고 코드는 인터넷 망의 노드를 거쳐서 목적지로 전달된다.

## IP

패킷이라는 단위로 데이터를 보낸다.

패킷에는 출발지 IP, 목적지 IP 등이 담겨져 있다.

### 단점

비 신뢰성: 보낼 때 받는 대상이 수신 가능한지를 알 수 없다.

비 연결성: 보내고자 하는 데이터의 용량이 커서 패킷을 둘 이상으로 나눌 때, 받는 쪽에서 보내는 쪽의 순서를 보장할 수 없다.

또한 IP는 중복될 수 있기 때문에, 같은 서버가 둘 인 경우 문제가 발생한다.

## TCP, UDP

### TCP

위 문제를 해결하기 위해 TCP는 신뢰성을 보장한다.

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

위와 같이 IP 패킷 내에 세그먼트로 래핑되는데, 이 때 순서, 전송 제어, 검증 정보와 같은 값이 포함된다.

* 3-way handshake(**SYN** - ACK + SYN - **ACK**)
* 데이터 전달 보증
* 순서 보장





