# Questions

학습 중 생긴 질문을 정리하는 공간입니다.

- 각 챕터 안에서 자유롭게 작성해 주세요.
- 한 블록에는 가능하면 하나의 질문만 적어 주세요.
- 칸이 부족하면 같은 블록을 복사해서 이어서 사용하면 됩니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 질문: BSD 소켓, IP, TCP, HTTP, file descriptor, DNS 같은 키워드를 깊게 공부하려면 어떤 순서로 접근하는 것이 효과적일까?
- 배경/맥락: 네트워크 프로그래밍 키워드들이 한꺼번에 등장해서 각각의 관계와 학습 순서를 먼저 잡고 싶었음.
- 메모(선택): 전체 학습 로드맵, 계층별 흐름

---
- 질문: 컴퓨터 공학 용어가 왜 관점에 따라 시스템, 프로토콜, API, 자료구조처럼 급격히 다르게 불릴까?
- 배경/맥락: DNS가 "도메인을 IP로 바꿔주는 시스템"이면서 동시에 "프로토콜"이라고 설명되어 혼란이 생김.
- 메모(선택): abstraction, layer, interface

---
- 질문: "application이 통신을 시작한다"에서 application은 구체적으로 브라우저를 뜻하는 걸까?
- 배경/맥락: 도메인 입력 이후 흐름을 설명할 때 application, browser, resolver의 주체가 헷갈렸음.
- 메모(선택): client process, browser

---
- 질문: 클라이언트 쪽 흐름과 서버 쪽 흐름은 어디까지 같고 어디서부터 달라질까?
- 배경/맥락: 클라이언트는 `getaddrinfo()` -> `socket()` -> `connect()`로 진행하는데, 서버는 `bind()` -> `listen()` -> `accept()`가 등장해서 역할 차이가 궁금했음.
- 메모(선택): client side vs server side

<br>
<br>

## [ 11.2 Networks ]

---
- 질문: 와이파이에 물리적으로 연결되었다는 것과 실제 네트워크 통신이 가능한 상태는 어떻게 다를까?
- 배경/맥락: 와이파이 비밀번호를 입력해 연결되더라도 IP, DNS, gateway 설정을 아직 받아야 한다는 점이 헷갈렸음.
- 메모(선택): link layer, network layer

---
- 질문: DHCP 통신이 정확히 무엇이고, 왜 와이파이에 접속하면 DHCP가 실행될까?
- 배경/맥락: 네트워크에 처음 들어간 컴퓨터가 자기 IP와 설정값을 어떻게 얻는지 알고 싶었음.
- 메모(선택): Dynamic Host Configuration Protocol

---
- 질문: DHCP broadcast 패킷은 구체적으로 무엇이며, IP가 없는 상태에서 어떻게 네트워크에 요청을 보낼 수 있을까?
- 배경/맥락: "네트워크 전체에 IP를 달라고 외친다"는 설명에서 실제 전송 방식이 궁금했음.
- 메모(선택): MAC broadcast, `FF:FF:FF:FF:FF:FF`

---
- 질문: 컴퓨터에 저장된 이전 와이파이 목록에는 정확히 어떤 정보가 저장될까?
- 배경/맥락: 저장된 네트워크 목록이 IP, DNS, gateway까지 기억하는지 아니면 SSID와 비밀번호 중심인지 궁금했음.
- 메모(선택): SSID, password, BSSID, WPA/WPA2/WPA3

---
- 질문: DHCP가 할당하는 IP 주소, subnet mask, default gateway, local DNS 서버 주소는 각각 무슨 의미일까?
- 배경/맥락: DHCP 응답에 여러 설정값이 함께 들어온다고 했는데 각 값의 목적이 정리되지 않았음.
- 메모(선택): DHCP lease

---
- 질문: subnet mask는 내부망과 외부망을 판단할 때 어떻게 사용될까?
- 배경/맥락: 목적지 IP가 같은 LAN 안에 있는지 외부 인터넷에 있는지 OS가 어떻게 구분하는지 알고 싶었음.
- 메모(선택): network ID, host ID

---
- 질문: default gateway는 정확히 무엇이며, 왜 외부로 나가는 패킷은 gateway로 보내야 할까?
- 배경/맥락: 내 컴퓨터가 전 세계 경로를 다 아는 것이 아니라 공유기 같은 출입구로 넘긴다는 구조가 궁금했음.
- 메모(선택): router, next hop

---
- 질문: routing table은 무엇이고 OS 내부에서 어떤 역할을 할까?
- 배경/맥락: 목적지 IP를 알고 있어도 실제로 어느 인터페이스로 내보낼지 결정하는 자료구조가 필요하다는 점이 궁금했음.
- 메모(선택): kernel routing table

---
- 질문: 와이파이에 접속하면 routing table의 default gateway 경로가 추가되고, 와이파이가 끊기면 그 경로가 삭제되는 걸까?
- 배경/맥락: 네트워크 연결/해제 시 OS가 라우팅 정보를 동적으로 업데이트하는지 확인하고 싶었음.
- 메모(선택): route add, route flush

---
- 질문: 와이파이가 끊겨도 DNS 서버 IP나 목적지 IP를 알고 있다면 왜 패킷을 보낼 수 없을까?
- 배경/맥락: 목적지 주소를 알고 있어도 물리적 연결이나 routing table 경로가 없으면 전송할 수 없는 이유가 궁금했음.
- 메모(선택): interface down, network unreachable

---
- 질문: "내 네트워크"라고 저장되어 있는 것은 각 와이파이와 연결된 local DNS 서버 주소를 의미하는 걸까?
- 배경/맥락: 집, 카페, 학교 와이파이에 따라 DNS와 gateway 설정이 달라지는 구조가 궁금했음.
- 메모(선택): network profile vs DHCP 설정값

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 질문: 사용자가 `google.com`을 입력하면 바로 DNS 탐색이 실행되는 걸까?
- 배경/맥락: 브라우저가 도메인 문자열을 받았을 때 언제 DNS 조회가 필요한지 알고 싶었음.
- 메모(선택): DNS lookup, cache lookup

---
- 질문: DNS 탐색의 주체는 DNS 자체일까, 브라우저일까?
- 배경/맥락: DNS가 시스템인지 application인지 헷갈렸고, 실제로 누가 조회를 시작하는지 구분하고 싶었음.
- 메모(선택): browser, resolver

---
- 질문: resolver는 어떻게 정의할 수 있을까?
- 배경/맥락: resolver가 OS 함수인지, 시스템 콜인지, 여러 시스템 콜의 집합인지 혼란이 있었음.
- 메모(선택): stub resolver, library code

---
- 질문: "OS가 알아서 DNS 서버와 통신한다"는 말은 정확히 무엇을 의미할까?
- 배경/맥락: resolver가 내부에서 DNS 메시지를 만들고 `socket()`, `sendto()`, `recvfrom()` 같은 시스템 콜을 호출하는 구조인지 궁금했음.
- 메모(선택): user space library, kernel system call

---
- 질문: DNS query에서 query는 구체적으로 무엇일까?
- 배경/맥락: query가 단순 문자열인지, DNS 프로토콜에 맞춘 자료구조인지, 네트워크로 보내는 데이터인지 알고 싶었음.
- 메모(선택): DNS message format

---
- 질문: DNS query 구조체를 왜 만들고 어디로 보내는 걸까?
- 배경/맥락: 도메인을 IP로 바꾸기 위해 어떤 서버에 어떤 형식으로 질의하는지 이해하고 싶었음.
- 메모(선택): local DNS server, UDP 53

---
- 질문: 브라우저 캐시나 OS 캐시에 도메인과 IP 매핑이 있으면 UDP 통신을 생략하는 걸까?
- 배경/맥락: 이미 IP를 알고 있다면 DNS 서버에 다시 물어볼 필요가 없는지 확인하고 싶었음.
- 메모(선택): browser cache, OS cache, hosts file

---
- 질문: local DNS server는 정확히 무엇이고, 정말 내 주변에 도메인-IP 매핑 서버가 있다는 뜻일까?
- 배경/맥락: local DNS가 물리적으로 가까운 서버인지, 네트워크 설정상 1차로 질의하는 DNS 서버인지 헷갈렸음.
- 메모(선택): ISP DNS, public DNS, DHCP

---
- 질문: OS가 모든 목적지 IP를 저장하지 않는다면서 local DNS 서버 IP는 왜 저장하고 있을까?
- 배경/맥락: "전 세계 주소는 저장하지 않지만, 물어볼 DNS 서버 주소는 설정값으로 가진다"는 차이를 분명히 하고 싶었음.
- 메모(선택): final destination IP vs configured DNS server IP

---
- 질문: struct는 원래 메모리에 연속적으로 놓이는데, 왜 따로 serialization이 필요할까?
- 배경/맥락: 구조체를 그대로 보내면 되는 것처럼 보였지만 padding과 byte order 문제가 있다고 해서 궁금했음.
- 메모(선택): serialization, wire format

---
- 질문: `sizeof(struct)`에는 padding이 포함되는 걸까?
- 배경/맥락: 구조체 필드 크기의 합과 실제 구조체 크기가 다를 수 있다는 점을 확인하고 싶었음.
- 메모(선택): alignment, padding

---
- 질문: RAM에는 타입 정보가 없다는데, 그렇다면 "타입을 제거한다"는 말은 정확히 무슨 뜻일까?
- 배경/맥락: 네트워크로 전송될 때 데이터가 `int`, `char`, `struct` 같은 의미를 잃고 byte 배열로 다뤄진다는 말이 헷갈렸음.
- 메모(선택): compile time type, runtime bytes

---
- 질문: endian은 무엇이고 왜 little endian과 big endian이 나뉘었을까?
- 배경/맥락: 네트워크 전송 전에 byte order를 바꿔야 한다는 설명을 이해하기 위해 필요했음.
- 메모(선택): network byte order, host byte order

---
- 질문: `0x1234`가 little endian에서 왜 `43 21`이 아니라 `34 12`가 될까?
- 배경/맥락: endian이 bit나 16진수 한 자리 단위가 아니라 byte 단위로 순서를 바꾼다는 점이 헷갈렸음.
- 메모(선택): byte, nibble

---
- 질문: UDP와 TCP의 포트 번호는 정해져 있고, 같은 번호라도 프로토콜이 다르면 서로 독립적인가?
- 배경/맥락: TCP 53번과 UDP 53번처럼 번호가 같아도 커널 내부에서는 별도의 포트 공간인지 궁금했음.
- 메모(선택): TCP port space, UDP port space

---
- 질문: 하나의 연결을 유지한 채 UDP에서 TCP로 통신 방식을 바꿀 수 있을까?
- 배경/맥락: DNS가 보통 UDP를 쓰지만 필요하면 TCP를 쓴다는 말을 듣고, 같은 연결 안에서 바뀌는지 궁금했음.
- 메모(선택): protocol switch, new socket

---
- 질문: DNS에서는 UDP로 IP를 먼저 얻고, 그 IP로 웹 서버와 TCP 연결을 맺는 흐름이 맞을까?
- 배경/맥락: DNS 질의용 통신과 실제 HTTP 통신이 서로 다른 목적과 프로토콜을 가진다는 점을 정리하고 싶었음.
- 메모(선택): DNS over UDP, HTTP over TCP

---
- 질문: byte stream을 만들었다고 해서 목적지를 아는 것은 아닌데, 목적지 주소는 어떻게 알게 될까?
- 배경/맥락: DNS query를 보낼 local DNS 서버 주소와 최종 웹 서버 주소가 서로 다른 목적지라는 점이 헷갈렸음.
- 메모(선택): destination A, destination B

---
- 질문: 내가 코드에서 목적지를 지정해야 한다면, DNS는 왜 필요한 걸까?
- 배경/맥락: resolver가 local DNS 서버로 질의할 때의 목적지와, 브라우저가 최종 웹 서버로 접속할 때의 목적지를 구분하고 싶었음.
- 메모(선택): DNS server IP vs web server IP

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 질문: `getaddrinfo()`가 반환하는 구조체에는 IP 주소뿐 아니라 어떤 부가 정보가 들어 있을까?
- 배경/맥락: resolver가 IP만 넘기는 것이 아니라 `struct addrinfo` 형태로 주소 체계, 소켓 타입, 포트 정보 등을 함께 준다는 점이 궁금했음.
- 메모(선택): `ai_family`, `ai_socktype`, `ai_protocol`, `ai_addr`, `ai_next`

---
- 질문: IP 주소를 얻은 뒤 다음 목적은 무엇이며, 왜 소켓과 file descriptor가 필요할까?
- 배경/맥락: DNS 이후 실제 웹 서버와 통신하기 위해 커널에 어떤 준비를 요청하는지 알고 싶었음.
- 메모(선택): `socket()`, `connect()`

---
- 질문: socket은 정확히 무엇이라고 정의할 수 있을까?
- 배경/맥락: 소켓이 단순 함수인지, 커널 내부 자료구조인지, application이 통신을 위임한 endpoint인지 정리하고 싶었음.
- 메모(선택): kernel endpoint, network I/O object

---
- 질문: 왜 소켓을 생성해야 하며, 통신할 때마다 시스템 콜만 호출하면 안 될까?
- 배경/맥락: TCP 통신에는 시퀀스 번호, ACK, 버퍼, 연결 상태처럼 계속 유지해야 하는 상태가 필요하다는 점이 궁금했음.
- 메모(선택): TCP state machine

---
- 질문: 소켓은 FIFO 큐 같은 자료구조일까, 아니면 더 무거운 state machine일까?
- 배경/맥락: 송수신 버퍼가 큐처럼 동작하지만 TCP 안정성을 보장하려면 더 많은 상태 관리가 필요하다는 점을 구분하고 싶었음.
- 메모(선택): send buffer, receive buffer, retransmission

---
- 질문: 소켓이 네트워크 통신의 안정성을 어떻게 높일까?
- 배경/맥락: 커널이 수신 버퍼, 재전송, 순서 재조립, 흐름 제어를 application 대신 처리한다는 점이 궁금했음.
- 메모(선택): reliability, ordering, buffering

---
- 질문: file descriptor는 어떻게 정의할 수 있을까?
- 배경/맥락: FD가 프로세스인지, 객체인지, 단순 정수인지 헷갈렸고 정확한 정의가 필요했음.
- 메모(선택): non-negative integer index

---
- 질문: file descriptor는 어떻게 커널 메모리 주소를 숨기고도 소켓을 조작하게 해줄까?
- 배경/맥락: application이 커널의 소켓 포인터를 직접 받지 않고 정수 FD만 받는 구조가 궁금했음.
- 메모(선택): FD table, handle

---
- 질문: file descriptor table은 입출력 객체 자체를 담는 배열일까, 객체의 포인터를 담는 배열일까?
- 배경/맥락: `FD table[fd] -> kernel object pointer` 구조를 이해하며 double pointer 느낌인지 확인하고 싶었음.
- 메모(선택): array of pointers

---
- 질문: 소켓 커널 객체는 user space에 할당될까, kernel space에 할당될까?
- 배경/맥락: `socket()` 시스템 콜 이후 생성되는 자료구조가 application 메모리 안에 있는지 커널 메모리 안에 있는지 알고 싶었음.
- 메모(선택): kernel space allocation

---
- 질문: 시스템 콜을 호출하면 CPU와 OS 내부에서 어떤 일이 일어날까?
- 배경/맥락: `socket()`, `connect()`, `write()` 같은 함수 호출이 user mode에서 kernel mode로 넘어가는 과정을 알고 싶었음.
- 메모(선택): mode switch, trap, syscall

---
- 질문: file descriptor table, routing table 같은 커널 자료구조는 page table 안에 저장되는 걸까?
- 배경/맥락: page table과 routing table/file descriptor table의 역할이 모두 "table"이라 헷갈렸음.
- 메모(선택): page table은 주소 변환용, 커널 자료구조는 kernel memory 안에 별도 존재

---
- 질문: kernel memory는 어떻게 구성되어 있고, page table과 어떤 관계가 있을까?
- 배경/맥락: routing table, FD table, socket 객체가 커널 메모리에 저장된다면 가상 메모리와 어떻게 연결되는지 궁금했음.
- 메모(선택): kernel space, virtual memory

---
- 질문: 지금까지 배운 socket이 BSD socket을 의미하는 걸까?
- 배경/맥락: socket이라는 커널 자료구조와 BSD Socket이라는 용어가 같은 것인지 헷갈렸음.
- 메모(선택): socket object vs socket API

---
- 질문: BSD는 무엇을 의미하며, BSD socket이 아닌 다른 소켓 인터페이스도 있을까?
- 배경/맥락: BSD Socket이라는 이름의 역사와 현대 OS에서 사실상 표준이 된 이유가 궁금했음.
- 메모(선택): Berkeley Software Distribution, Winsock, TLI

---
- 질문: BSD Socket은 커널 내부 자료구조인가, 아니면 API 규격인가?
- 배경/맥락: `socket()`, `connect()`, `bind()`, `listen()`, `accept()` 같은 함수 묶음이 BSD Socket API라는 점을 정리하고 싶었음.
- 메모(선택): API/interface

---
- 질문: Stream Socket과 Datagram Socket은 socket의 어떤 분류일까?
- 배경/맥락: 소켓이 모두 TCP state machine인 줄 알았는데 UDP용 datagram socket도 있다는 점이 궁금했음.
- 메모(선택): `SOCK_STREAM`, `SOCK_DGRAM`

---
- 질문: TCP 방식이면 Stream Socket, UDP 방식이면 Datagram Socket으로 나뉘는 것이 맞을까?
- 배경/맥락: `socket()` 호출 시 type 인자로 어떤 종류의 커널 소켓 엔진을 만들지 선택한다는 점을 정리하고 싶었음.
- 메모(선택): TCP vs UDP

---
- 질문: `connect()`는 `socket()`으로 받은 FD를 가지고 구체적으로 무엇을 하는 함수일까?
- 배경/맥락: 소켓 객체를 만든 뒤 목적지 IP/port와 연결해 TCP 3-way handshake를 시작하는 흐름이 궁금했음.
- 메모(선택): active open

---
- 질문: 클라이언트도 포트가 필요한데 왜 클라이언트는 보통 `bind()`를 명시적으로 호출하지 않을까?
- 배경/맥락: 서버는 `bind()`로 포트를 점유하지만 클라이언트 포트는 `connect()` 과정에서 ephemeral port로 자동 할당되는지 궁금했음.
- 메모(선택): implicit bind, ephemeral port

---
- 질문: 서버 쪽 `getaddrinfo()`도 DNS 조회를 하는 걸까?
- 배경/맥락: 클라이언트는 목적지 도메인을 IP로 바꾸기 위해 `getaddrinfo()`를 쓰지만, 서버는 수신 대기 주소를 만들기 위해 같은 함수를 다르게 쓰는지 궁금했음.
- 메모(선택): `AI_PASSIVE`, wildcard address

---
- 질문: 클라이언트와 서버가 호출하는 `socket()`은 같은 함수인데, 만들어지는 소켓도 같은 의미일까?
- 배경/맥락: `socket()` 직후에는 빈 통신 endpoint지만 이후 `connect()`로 active socket, `bind()`/`listen()`으로 passive socket이 된다는 점이 궁금했음.
- 메모(선택): active socket, passive socket

---
- 질문: `listen()`은 기존 소켓 안에 새로운 큐를 생성하는 함수일까?
- 배경/맥락: 서버가 연결 요청을 기다릴 때 데이터 버퍼가 아니라 연결 대기열을 만든다는 점을 구분하고 싶었음.
- 메모(선택): SYN queue, accept queue

---
- 질문: `listen()`의 큐는 일반 소켓의 data buffer와 본질적으로 같은 것일까?
- 배경/맥락: 일반 소켓은 byte 데이터를 줄 세우지만 listening socket은 연결 요청 정보를 줄 세운다는 점이 궁금했음.
- 메모(선택): data buffer vs connection queue

---
- 질문: `accept()`는 FIFO 순서로 큐에서 무엇을 꺼내오는 걸까?
- 배경/맥락: accept queue에 완성된 소켓 객체가 들어 있는지, 연결 메타데이터가 들어 있는지 구분하고 싶었음.
- 메모(선택): completed connection metadata

---
- 질문: `accept()`는 큐에서 꺼낸 뒤 새로운 connected socket을 만드는 걸까?
- 배경/맥락: "꺼낸다"와 "새 소켓을 복제/생성한다"는 표현 사이의 관계가 헷갈렸음.
- 메모(선택): pop metadata, allocate connected socket

---
- 질문: 왜 `accept()`는 listening socket을 그대로 쓰지 않고 새로운 connected socket과 새 FD를 만들까?
- 배경/맥락: 한 소켓은 계속 새 연결을 기다리고, 다른 소켓은 특정 클라이언트와 1:1로 데이터를 주고받는 구조가 궁금했음.
- 메모(선택): listening FD vs connected FD

---
- 질문: `accept()`로 새 connected socket이 생기면 서버는 새로운 포트를 사용하는 걸까?
- 배경/맥락: 여러 클라이언트가 모두 서버의 80번 포트로 접속하는데 어떻게 각 연결이 구분되는지 궁금했음.
- 메모(선택): same server port, 4-tuple

---
- 질문: 커널은 같은 서버 포트로 들어온 패킷을 어떤 connected socket의 receive buffer에 넣을지 어떻게 결정할까?
- 배경/맥락: destination port만으로는 구분이 안 되므로 `{source IP, source port, destination IP, destination port}` 조합을 본다는 점이 궁금했음.
- 메모(선택): demultiplexing, 4-tuple

---
- 질문: `accept()` 이후 connected socket을 기록하는 커널 hash table의 이름은 무엇일까?
- 배경/맥락: 4-tuple로 패킷을 빠르게 찾으려면 커널 전역 검색 구조에 소켓이 등록되어야 한다고 생각했음.
- 메모(선택): Linux 기준 `tcp_hashinfo`, `ehash`, `listening_hash`

---
- 질문: `accept()` 과정은 클라이언트에게 데이터를 보내기 위한 과정일까, 서버와 클라이언트를 연결하기 위한 과정일까?
- 배경/맥락: accept 이후에야 `read()`/`write()`로 실제 HTTP 데이터를 주고받는다는 점을 구분하고 싶었음.
- 메모(선택): connection establishment

---
- 질문: "목적지 소켓"이라는 말에서 목적지는 클라이언트 쪽 소켓일까, 서버 내부의 수신 소켓일까?
- 배경/맥락: 패킷이 서버 랜카드에 도착한 뒤 커널이 찾아야 하는 최종 수신함이 무엇인지 헷갈렸음.
- 메모(선택): destination socket inside server kernel

<br>
<br>

## [ 11.5 Web Servers ]

---
- 질문: 웹 서버는 클라이언트의 `connect()` 요청을 받기 위해 어떤 순서로 준비해야 할까?
- 배경/맥락: 서버가 `socket()` 이후 `bind()` -> `listen()` -> `accept()`를 호출하는 이유와 각 단계의 목적을 정리하고 싶었음.
- 메모(선택): server socket lifecycle

---
- 질문: listening FD와 connected FD는 웹 서버 안에서 각각 어떤 역할을 할까?
- 배경/맥락: 하나는 새 연결 요청을 기다리고, 다른 하나는 특정 클라이언트와 데이터를 주고받는다는 점을 분리하고 싶었음.
- 메모(선택): accept loop

---
- 질문: connected FD를 얻은 뒤 웹 서버는 어떤 방식으로 HTTP request를 읽고 response를 보낼까?
- 배경/맥락: OS 레벨의 연결이 끝난 뒤 application layer의 HTTP 규약으로 넘어가는 지점이 궁금했음.
- 메모(선택): `read()`, `write()`, HTTP

---
- 질문: 여러 클라이언트가 동시에 접속하면 웹 서버는 connected socket들을 어떻게 관리할까?
- 배경/맥락: accept로 새 FD가 계속 생길 때 fork, thread, event loop 같은 처리 방식이 필요할 것 같았음.
- 메모(선택): concurrent server

---
- 질문: 웹 서버가 정적 컨텐츠와 동적 컨텐츠를 처리할 때 connected FD는 어떻게 사용될까?
- 배경/맥락: 파일을 그대로 보내는 경우와 CGI 같은 프로그램의 출력 결과를 보내는 경우가 FD 관점에서 어떻게 달라지는지 궁금했음.
- 메모(선택): static content, dynamic content, CGI

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 질문: Tiny Web Server 같은 구현에서는 `getaddrinfo()`, `socket()`, `bind()`, `listen()`, `accept()`, `read()`, `write()`가 어떤 순서로 연결될까?
- 배경/맥락: 개별 함수의 역할은 배웠지만, 실제 웹 서버 코드 안에서 전체 흐름으로 보고 싶었음.
- 메모(선택): Tiny lifecycle

---
- 질문: Tiny Web Server에서 클라이언트별 connected FD는 언제 닫히고, listening FD는 언제까지 유지될까?
- 배경/맥락: listening socket과 connected socket의 생명주기가 다르다는 점을 실제 코드 흐름에 맞춰 확인하고 싶었음.
- 메모(선택): `close()`, FD lifecycle

---
- 질문: CGI 프로그램을 실행할 때 `dup2()`로 표준출력을 connected socket 쪽으로 바꾸는 이유는 무엇일까?
- 배경/맥락: 동적 컨텐츠 생성 과정에서 외부 프로그램의 stdout이 클라이언트 응답으로 이어지는 구조가 궁금했음.
- 메모(선택): CGI, file descriptor redirection

<br>
<br>

## [ 11.7 Summary ]

---
- 질문: 도메인을 입력한 순간부터 TCP 연결이 성립할 때까지의 전체 흐름을 한 번에 어떻게 정리할 수 있을까?
- 배경/맥락: DNS, DHCP, routing table, resolver, socket, file descriptor, connect가 모두 연결되어 있어 중간 정리가 필요했음.
- 메모(선택): end-to-end flow

---
- 질문: 지금까지 타파한 키워드와 아직 남은 키워드는 무엇일까?
- 배경/맥락: 학습이 진행되면서 DNS, IP, BSD socket, file descriptor는 다뤘고 HTTP, MIME, CGI, proxy 등은 남아 있는지 확인하고 싶었음.
- 메모(선택): progress check

---
- 질문: 소켓과 file descriptor까지 이해한 뒤에는 HTTP, MIME type, 웹 컨텐츠, 프록시 서버를 어떤 순서로 학습하면 좋을까?
- 배경/맥락: OS/네트워크 연결 계층을 지나 application layer의 실제 웹 데이터 규약으로 넘어갈 준비를 하고 있었음.
- 메모(선택): next topics, HTTP, MIME, proxy
