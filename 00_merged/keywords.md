# Keywords

## [ 11.1 The Client-Server Programming Model ]

---
- 키워드: 클라이언트-서버 모델 (client-server model)
- 메모: 11장의 출발점은 누가 요청을 시작하고 누가 자원을 관리하는지의 역할 구분이다. client와 server는 물리적인 기계보다 process의 역할 차이로 이해하는 편이 정확하다. 이 틀을 먼저 잡아야 이후의 socket, HTTP, Tiny가 모두 같은 구조 안에 놓인다.
- 연결 개념(선택): client, server, process, role

---
- 키워드: 트랜잭션 (transaction)
- 메모: transaction은 요청, 해석, 자원 처리, 응답으로 이어지는 한 번의 상호작용 단위다. Echo의 한 줄 왕복도, HTTP request/response 한 쌍도 이 단위로 읽을 수 있다. 11장은 이 한 바퀴를 점점 더 구체적인 코드와 인터페이스로 내려오는 장이다.
- 연결 개념(선택): request-response, flow, interaction

---
- 키워드: 자원 (resource)
- 메모: 서버를 “응답하는 프로그램”이 아니라 “자원을 관리하는 프로그램”으로 보면 11장의 구조가 훨씬 선명해진다. 웹 서버에게는 파일과 실행 파일이, CGI에게는 실행 환경과 표준 출력 경로가 자원이 된다. 이 관점은 네트워크 함수와 운영체제 자원이 왜 함께 등장하는지도 자연스럽게 설명해 준다.
- 연결 개념(선택): file, process, service, management

---
- 키워드: 추상화 수준 (abstraction level)
- 메모: DNS, socket, HTTP 같은 대상은 system, protocol, API, 자료구조라는 서로 다른 층위에서 동시에 설명될 수 있다. 그래서 용어가 바뀐다고 대상이 완전히 달라지는 것이 아니라, 보는 높이가 바뀌는 것이라고 이해해야 한다. 11장을 읽을 때는 서비스 모델, 전송 규약, 프로그래머 인터페이스를 계속 구분해서 보는 습관이 중요하다.
- 연결 개념(선택): system, protocol, API, data structure

<br>
<br>

## [ 11.2 Networks ]

---
- 키워드: 네트워크는 I/O 장치 (network as an I/O device)
- 메모: 네트워크는 프로그램 입장에서 바이트를 읽고 쓰는 I/O 대상이며, 디스크나 터미널과 같은 공통 추상 안에 놓인다. 이 관점을 잡아야 소켓이 file descriptor로 보이고, 연결 뒤의 통신이 `read`와 `write`로 이어지는 이유가 자연스럽다. 11장은 새로운 세계 하나를 더 배우는 장이 아니라, Unix I/O가 네트워크로 확장되는 장에 가깝다.
- 연결 개념(선택): Unix I/O, descriptor, `read`/`write`

---
- 키워드: Kernel / file descriptor / system call
- 메모: 커널은 프로세스와 하드웨어 사이에서 자원을 중재하고, file descriptor는 그 자원을 가리키는 정수 핸들 역할을 한다. 프로세스는 시스템 콜을 통해 커널에게 읽기, 쓰기, 연결 같은 작업을 요청한다. 소켓 역시 이 공통 틀 안에서 다뤄지므로, 네트워크 프로그래밍은 커널과의 대화를 이해하는 일이기도 하다.
- 연결 개념(선택): kernel, FD, system call, resource mediation

---
- 키워드: 네트워크 진입 상태 (`DHCP` / subnet mask / default gateway / routing table)
- 메모: “와이파이에 붙었다”는 사실만으로는 실제 통신 준비가 끝난 것이 아니다. OS는 DHCP를 통해 주소와 기본 경로, DNS 정보 등을 받아야 하고, routing table까지 맞아야 외부와 통신할 수 있다. 이 키워드는 11.2의 네트워크 바닥을 현실의 접속 상태와 연결해 준다.
- 연결 개념(선택): DHCP, subnet, gateway, route

---
- 키워드: 프로토콜 (protocol)
- 메모: 프로토콜은 누구에게 보낼지와 어떻게 전달할지를 정하는 통신 규칙이다. 11장의 흐름에서는 IP, TCP, HTTP가 각기 다른 층위의 프로토콜로 등장하며, 이들을 구분해야 역할이 겹치지 않는다. 결국 프로토콜은 바이트열에 공통 의미를 부여하는 약속이라고 볼 수 있다.
- 연결 개념(선택): naming scheme, delivery mechanism, interoperability

---
- 키워드: 계층화 / 캡슐화 (layering / encapsulation)
- 메모: application data는 아래 계층으로 내려가며 TCP segment, IP packet, Ethernet frame 같은 더 큰 단위로 포장된다. 이 캡슐화 덕분에 각 계층은 자기 문제만 해결하면서도 전체 전달 과정에 참여할 수 있다. 프로그래머가 직접 모든 헤더를 다루지 않더라도, 데이터가 계층적으로 이동한다는 감각은 반드시 필요하다.
- 연결 개념(선택): packet, frame, header, layering

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 키워드: DNS / resolver / cache
- 메모: 이름 해석은 DNS 서버 하나가 단독으로 처리하는 일이 아니라, browser cache, OS resolver, local DNS server가 함께 엮여 수행하는 과정이다. 사용자는 도메인 이름으로 시작하지만, 실제 연결 준비는 이 과정을 거쳐 IP address 후보를 얻는 데서 출발한다. 이 키워드는 “이름의 세계”와 “주소의 세계”를 잇는 첫 관문이다.
- 연결 개념(선택): domain name, cache, local DNS, UDP 53

---
- 키워드: IP address / port / socket address / socket pair
- 메모: IP address는 host를, port는 host 안의 service를, socket address는 그 둘의 결합을, socket pair는 실제 connection의 정체성을 가리킨다. 이 층위를 구분해야 같은 server port 위에 여러 connection이 동시에 올라갈 수 있는 이유도 이해된다. “서버를 찾는다”는 말은 결국 이 네 단계의 식별을 차례로 좁혀 가는 일이다.
- 연결 개념(선택): endpoint, service, 4-tuple, ephemeral port

---
- 키워드: network byte order / serialization / padding
- 메모: 네트워크는 타입이 아니라 byte sequence를 전달하므로, 메모리 안의 구조체 표현을 그대로 전송하면 padding과 endian 차이 때문에 문제가 생길 수 있다. 그래서 hton/ntoh 같은 변환과 serialization 감각이 필요하다. 이 키워드는 “메모리 표현”과 “wire format”이 다를 수 있다는 사실을 붙드는 데 중요하다.
- 연결 개념(선택): hton, ntoh, wire format, `sizeof(struct)`

---
- 키워드: TCP / UDP
- 메모: DNS 조회와 웹 통신이 서로 다른 목적과 전송 특성을 가지기 때문에, 각각 UDP와 TCP 위에 놓이는 것이 자연스럽다. 같은 port 번호라도 TCP와 UDP는 별도의 port space를 갖기 때문에 서로 다른 문맥에서 동작한다. 이 대비는 “이름을 묻는 통신”과 “서비스를 받는 통신”이 다르다는 사실을 보여 준다.
- 연결 개념(선택): DNS over UDP, HTTP over TCP, port space

---
- 키워드: 연결 / reliable byte stream (connection / reliable byte stream)
- 메모: TCP connection은 point-to-point, full-duplex, reliable byte stream이라는 추상으로 이해하는 것이 핵심이다. 특히 byte stream이라는 표현을 붙들어야 HTTP의 message boundary를 누가 책임지는지도 보이기 시작한다. 즉, TCP는 메시지 객체가 아니라 순서 있는 바이트 흐름을 제공한다.
- 연결 개념(선택): stream semantics, HTTP, framing

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 키워드: BSD sockets interface
- 메모: BSD sockets interface는 `socket`, `connect`, `bind`, `listen`, `accept`로 이어지는 대표적인 네트워크 프로그래밍 API 묶음이다. 오늘날에도 `sockaddr *` 같은 오래된 표면이 유지되는 것은 호환성과 역사 때문이다. 11.4는 이 인터페이스를 연결 생애주기로 읽는 장이라고 보면 된다.
- 연결 개념(선택): API, system call, compatibility, sockets

---
- 키워드: 소켓 / endpoint / file descriptor / kernel object
- 메모: 프로그램이 직접 쥐는 것은 정수 file descriptor이지만, 실제 통신 상태를 가진 것은 커널 안의 소켓 객체다. endpoint라는 말은 통신의 종단점을 뜻하고, socket은 그 종단점을 커널이 관리 가능한 객체로 만든 것이다. 이 키워드를 이해해야 “소켓은 주소인가, 객체인가, 핸들인가” 같은 혼란이 줄어든다.
- 연결 개념(선택): FD table, handle, endpoint, kernel state

---
- 키워드: `socket -> connect` / ephemeral port
- 메모: client는 `socket()`으로 partially opened endpoint를 만든 뒤, `connect()`로 목적지 address:port에 능동적으로 다가간다. 이 과정에서 local client port는 보통 커널이 ephemeral port로 자동 할당한다. 따라서 client 경로는 “도구 생성”과 “연결 완성”의 두 단계로 읽는 편이 명확하다.
- 연결 개념(선택): active open, implicit bind, client path

---
- 키워드: `bind -> listen -> accept` / backlog
- 메모: server는 `bind()`로 local address와 port를 점유하고, `listen()`으로 연결 요청을 기다리는 소켓으로 바꾼 뒤, `accept()`로 실제 대화용 연결을 꺼낸다. backlog는 이 과정에서 순간적으로 대기할 수 있는 연결 요청의 크기를 조정하는 값이다. 이 키워드는 server가 왜 client보다 더 많은 단계를 거치는지 설명해 준다.
- 연결 개념(선택): passive open, accept queue, server path

---
- 키워드: listening descriptor / connected descriptor
- 메모: `listenfd`는 다음 연결을 계속 기다리는 입구이고, `connfd`는 특정 client와 실제로 데이터를 주고받는 채널이다. 이 둘을 분리하기 때문에 같은 server port 위에서도 여러 connection을 동시에 유지할 수 있다. 12장 동시성 server의 구조적 출발점도 이 분리에서 나온다.
- 연결 개념(선택): listenfd, connfd, demultiplexing, concurrency

---
- 키워드: `sockaddr` family / `getaddrinfo` / `socklen_t` / `sockaddr_storage` / `SO_REUSEADDR`
- 메모: `sockaddr`는 범용 인터페이스이고, `sockaddr_in`은 IPv4 내용물, `sockaddr_storage`는 더 큰 주소 체계를 위한 저장소다. `getaddrinfo`는 이런 주소 표현을 protocol-independent하게 다루도록 돕고, `socklen_t`는 구조체 길이를 안전하게 전달하게 해 준다. `SO_REUSEADDR`은 여기에 운영 현실을 더하는 대표적인 socket option이다.
- 연결 개념(선택): casting, protocol-independent code, AI_PASSIVE, TIME_WAIT

<br>
<br>

## [ 11.5 Web Servers ]

---
- 키워드: HTTP 메시지 구조 (HTTP message structure)
- 메모: HTTP는 request line, headers, blank line, body라는 구조로 byte stream 위에 메시지 틀을 세운다. TCP가 메시지 경계를 자동으로 주지 않기 때문에, 이 구조를 이해해야 어떤 바이트를 어디까지 읽고 써야 하는지가 보인다. web server를 읽는 핵심은 결국 이 framing을 읽는 일이다.
- 연결 개념(선택): request, response, blank line, `Content-Length`

---
- 키워드: URL / URI / `Host` header
- 메모: client는 URL에서 접속 대상을 보고, server는 해석할 자원을 보며, `Host` header는 어떤 domain을 향한 요청인지 더 분명히 해 준다. 같은 문자열이라도 client와 server가 보는 중심이 다르기 때문에 두 관점을 나누어 읽어야 한다. proxy나 virtual hosting까지 생각하면 이 차이는 더 중요해진다.
- 연결 개념(선택): browser view, server view, virtual hosting

---
- 키워드: 정적 콘텐츠 / 동적 콘텐츠 (static content / dynamic content)
- 메모: 정적 콘텐츠는 file을 읽어 body로 보내고, 동적 콘텐츠는 program을 실행해 그 출력을 body로 보낸다. 둘 다 최종적으로는 HTTP response이지만, 자원을 해석하는 방식과 운영체제 자원을 동원하는 방식은 다르다. 이 대비는 Tiny의 `serve_static`과 `serve_dynamic`으로 다시 구체화된다.
- 연결 개념(선택): file serving, CGI, response body

---
- 키워드: CGI / `dup2`
- 메모: CGI는 child process의 표준 출력을 connected socket으로 돌려 응답을 만드는 고전적 동적 콘텐츠 모델이다. 여기서 `dup2`는 stdout과 socket 사이의 통로를 바꿔 붙이는 핵심 연산이다. 이 키워드는 “프로그램 출력이 곧 HTTP body가 된다”는 사실을 가장 투명하게 보여 준다.
- 연결 개념(선택): `QUERY_STRING`, stdout redirection, child process

---
- 키워드: 프록시 / 프록시 캐시 / 프록시 체인 (proxy / proxy cache / proxy chain)
- 메모: 프록시는 browser와 origin server 사이에 끼어 request-response를 대신 중계하거나 저장하는 중간 server다. 프록시 캐시는 이전 응답을 저장해 원본 server 대신 다시 돌려줄 수 있고, 프록시 체인은 이런 중간 단계가 여러 개 이어진 경우를 뜻한다. 이 키워드는 “중간 server가 들어와도 구조는 여전히 request-response”라는 점을 보여 준다.
- 연결 개념(선택): browser, proxy, cache, origin server

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 키워드: Tiny의 전체 생애주기 (Tiny lifecycle)
- 메모: Tiny는 `open_listenfd`, `accept`, `doit`, `close`로 이어지는 흐름 안에 11장의 핵심 개념을 실제 code로 묶어 보여 준다. 이 키워드는 개별 함수의 의미를 실제 server의 한 사이클 안에서 다시 읽게 만든다. Tiny를 보면 11장이 “흩어진 API 설명”이 아니라 “한 프로그램 이야기”로 접힌다.
- 연결 개념(선택): end-to-end flow, accept loop, request handling

---
- 키워드: `main` / `doit`
- 메모: `main`은 server의 골격을, `doit`는 한 번의 HTTP transaction 처리기를 보여 준다. 이 둘을 나누어 보면 “반복해서 연결을 받는 구조”와 “한 연결 안에서 요청을 해석하고 응답을 만드는 구조”가 분리되어 보인다. Tiny를 읽을 때 가장 먼저 잡아야 할 축도 바로 이 분리다.
- 연결 개념(선택): skeleton, transaction, `Accept -> doit -> Close`

---
- 키워드: `serve_static` / `serve_dynamic`
- 메모: `serve_static`은 file을 body로 삼는 경로이고, `serve_dynamic`은 CGI output을 body로 삼는 경로다. 둘 다 response를 만들지만, 무엇을 자원으로 삼고 누가 body를 생산하는지가 다르다. Tiny는 이 대비를 통해 web server의 두 기본 응답 경로를 가장 단순하게 보여 준다.
- 연결 개념(선택): file path, CGI path, response generation

---
- 키워드: `mmap` / `dup2`
- 메모: Tiny는 정적 file 전송에는 `mmap`을, 동적 응답 연결에는 `dup2`를 사용해 다른 장의 개념들을 실제 server code 안에 다시 불러온다. `mmap`은 file을 memory처럼 다루게 하고, `dup2`는 child stdout을 socket과 이어 붙인다. 이 두 키워드는 Tiny가 단순한 network 예제 이상으로 시스템 프로그래밍 종합판이라는 점을 보여 준다.
- 연결 개념(선택): file mapping, stdout redirection, OS resources

---
- 키워드: robust server / iterative limit
- 메모: Tiny는 교육용 최소 구현이라서 `SIGPIPE`, `EPIPE`, bad request, concurrency 같은 현실 문제를 거의 다루지 않는다. 그래서 “동작하는 server”와 “견고한 server”의 차이를 분명히 보여 주는 예제가 된다. 동시에 iterative structure의 한계는 12장 동시성으로 넘어가는 직접적인 동기가 된다.
- 연결 개념(선택): error handling, iterative server, concurrency

<br>
<br>

## [ 11.7 Summary ]

---
- 키워드: end-to-end flow
- 메모: 11장을 관통하는 가장 큰 축은 도메인 이름 입력부터 TCP 연결, request 처리, response 전송, 연결 종료까지의 한 줄 서사다. DNS, socket, Rio, HTTP, CGI, Tiny는 이 서사의 서로 다른 구간을 설명한다. 이 키워드를 붙들면 흩어진 용어들이 하나의 이야기 안에서 다시 정렬된다.
- 연결 개념(선택): name resolution, connect, accept, response

---
- 키워드: 파일 디스크립터 (file descriptor)
- 메모: file descriptor는 10장의 Unix I/O와 11장의 network programming을 잇는 가장 큰 공통 추상이다. 디스크 file, pipe, terminal, socket이 모두 열린 I/O 대상으로 보인다는 사실이 여기서 나온다. 네트워크를 낯선 API 묶음이 아니라 Unix I/O의 확장으로 읽게 해 주는 핵심 키워드다.
- 연결 개념(선택): Unix I/O, FD, socket, handle

---
- 키워드: short count / Rio (`Robust I/O`)
- 메모: network I/O에서는 한 번의 `read`나 `write`가 요청한 양을 모두 처리하지 못해도 정상일 수 있고, 이를 short count라고 부른다. Rio는 이런 현실을 안전하게 감싸면서도 line-oriented protocol을 읽기 쉽게 만들어 준다. 따라서 short count와 Rio는 연결이 열린 뒤의 진짜 실전 감각을 담당하는 한 쌍이다.
- 연결 개념(선택): partial read, partial write, `rio_readlineb`

---
- 키워드: 바이트 스트림 / 메시지 경계 (byte stream / message boundary)
- 메모: TCP는 순서 있는 byte stream을 제공하지만, request나 response 같은 메시지 경계는 자동으로 주지 않는다. 그래서 HTTP는 blank line과 `Content-Length`, Echo는 개행과 EOF 같은 규칙을 스스로 세워야 한다. 이 키워드는 “전송 계층의 추상”과 “애플리케이션 프로토콜의 규칙”을 구분하게 만든다.
- 연결 개념(선택): framing, EOF, `Content-Length`, HTTP

---
- 키워드: 동시성으로의 연결 (bridge to concurrency)
- 메모: 11장은 한 연결의 문법을 가르치고, 12장은 그 문법을 여러 connected FD에 동시에 적용하는 방법을 다룬다. `listenfd`와 `connfd`를 분리하는 설계, iterative server의 한계, multiple client 처리 문제는 모두 이 다리 위에 놓인다. 따라서 11장을 잘 이해한다는 것은 이미 12장의 문제의식까지 준비되었다는 뜻이기도 하다.
- 연결 개념(선택): iterative server, concurrent server, scalability
