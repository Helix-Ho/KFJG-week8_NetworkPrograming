# Keywords

네 사람의 키워드를 다시 통합한 문서입니다.

- 개별 문서의 질문형 관점과 탐구 흐름을 먼저 중심에 두고 묶었습니다.
- 빠지기 쉬운 11장 핵심 축은 메모와 연결 개념에서 보완했습니다.
- 중복 키워드는 합치고, 서로 다른 시선은 메모와 연결 개념에 함께 담았습니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 키워드: [*] 클라이언트-서버 모델 (client-server model)
- 메모: 네트워크 통신의 출발점은 누가 요청을 시작하고 누가 자원을 관리하는지의 역할 구분이며, 두 프로세스는 같은 호스트 안에 있을 수도 있다.
- 연결 개념(선택): client, server, localhost, remote host

---
- 키워드: [*] 트랜잭션 흐름 (transaction flow)
- 메모: 한 번의 요청, 처리, 응답을 클라이언트와 서버 양쪽 흐름으로 끊기지 않게 설명하는 것이 11장 전체 이해의 시작점이다.
- 연결 개념(선택): request-response, service flow, end-to-end

---
- 키워드: 추상화 수준 (abstraction level)
- 메모: 같은 대상을 system, protocol, API, 자료구조처럼 다르게 부르는 이유는 어느 층위에서 보느냐가 다르기 때문이다.
- 연결 개념(선택): system, protocol, API, data structure

<br>
<br>

## [ 11.2 Networks ]

---
- 키워드: [*] 네트워크는 I/O 장치 (network as an I/O device)
- 메모: 네트워크는 프로그램 입장에서 바이트를 읽고 쓰는 I/O 대상이며, 소켓과 file descriptor가 자연스럽게 연결되는 바탕이다.
- 연결 개념(선택): Unix I/O, adapter, memory, `read`/`write`

---
- 키워드: 네트워크 진입 설정 (`DHCP` / subnet mask / default gateway / routing table)
- 메모: 실제 통신 가능 상태는 단순 연결 여부가 아니라 IP 설정, 기본 경로, 로컬 DNS 설정까지 갖춰진 상태를 뜻한다.
- 연결 개념(선택): interface, next hop, network profile, local DNS server

---
- 키워드: [*] 프로토콜 / 계층 / 캡슐화 (protocol / layering / encapsulation)
- 메모: 프로토콜은 누구에게 어떻게 보낼지를 정하고, 캡슐화는 데이터가 계층을 내려가며 packet과 frame으로 포장되는 과정을 뜻한다.
- 연결 개념(선택): naming scheme, delivery mechanism, packet, frame

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 키워드: [*] DNS / resolver (`Domain Name System` / resolver)
- 메모: 이름 해석은 브라우저, OS resolver, local DNS server, cache가 함께 엮여 이루어지며, “DNS가 알아서 한다”로 끝나지 않는다.
- 연결 개념(선택): browser cache, OS cache, UDP 53, local DNS server

---
- 키워드: [*] IP 주소 / port / socket address / socket pair
- 메모: 호스트 식별, 서비스 식별, 통신 종단점, 연결 식별은 각각 다른 개념이며 이를 구분해야 같은 서버 포트에 여러 연결이 붙는 구조가 보인다.
- 연결 개념(선택): endpoint, well-known port, ephemeral port, 4-tuple

---
- 키워드: [*] byte order / serialization / padding
- 메모: struct가 메모리에 연속적으로 놓여 있어도 padding과 endian 차이 때문에 그대로 전송하지 못하고 wire format으로 직렬화해야 한다.
- 연결 개념(선택): hton, ntoh, network byte order, `sizeof(struct)`

---
- 키워드: [*] TCP / UDP
- 메모: DNS 조회와 웹 통신이 서로 다른 목적과 전송 특성을 가진 프로토콜 위에서 이루어진다는 점을 구분해야 한다.
- 연결 개념(선택): DNS over UDP, HTTP over TCP, port space

---
- 키워드: [*] 연결 (connection)
- 메모: TCP connection은 point-to-point, full-duplex, reliable byte stream으로 이해할 때 이후 HTTP와 소켓 동작을 읽기 쉬워진다.
- 연결 개념(선택): byte stream, stream semantics, HTTP

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 키워드: [*] BSD 소켓 인터페이스 (BSD sockets interface)
- 메모: `socket`, `connect`, `bind`, `listen`, `accept`로 이어지는 표준적 API 묶음이며, 오래된 `sockaddr *` 형태가 유지되는 것은 호환성과 역사 때문이다.
- 연결 개념(선택): API, system call, `sockaddr`, compatibility

---
- 키워드: [*] 소켓 / endpoint / socket descriptor / file descriptor
- 메모: 소켓은 커널 안의 통신 객체이고 endpoint는 통신의 끝점이며, 프로그램은 그 객체를 file descriptor라는 정수 핸들로 다룬다.
- 연결 개념(선택): kernel object, FD table, handle, socket address

---
- 키워드: [*] `socket` / `connect`
- 메모: 클라이언트는 소켓 객체를 만든 뒤 `connect()`로 목적지 IP와 port에 능동적으로 연결을 시작하며, 이 과정에서 단기 포트가 자동 할당되기도 한다.
- 연결 개념(선택): active open, implicit bind, ephemeral port

---
- 키워드: [*] `bind` / `listen` / `accept`
- 메모: 서버는 수신 주소를 묶고, 기다리는 소켓으로 바꾸고, 완성된 연결 하나를 꺼내 개별 대화 채널로 전환한다.
- 연결 개념(선택): passive open, accept queue, server lifecycle

---
- 키워드: [*] listening descriptor (`listenfd`) / connected descriptor (`connfd`)
- 메모: 새 연결을 기다리는 입구와 특정 클라이언트와 대화하는 채널을 분리하기 때문에, 같은 서버 포트 위에서도 여러 연결을 유지할 수 있다.
- 연결 개념(선택): listening socket, connected socket, demultiplexing

---
- 키워드: `getaddrinfo` / protocol-independent code
- 메모: 이름과 서비스 문자열을 주소 후보 리스트로 바꾸어 주며, 클라이언트와 서버가 같은 함수를 서로 다른 목적에 맞게 쓴다는 점이 중요하다.
- 연결 개념(선택): `AI_PASSIVE`, wildcard address, addrinfo list

---
- 키워드: 소켓 상태와 큐 (socket state and queue)
- 메모: connected socket의 송수신 버퍼와 listening socket의 연결 대기열은 역할이 다르며, 소켓은 단순 FIFO가 아니라 상태를 가진 커널 객체다.
- 연결 개념(선택): send buffer, receive buffer, state machine, accept queue

<br>
<br>

## [ 11.5 Web Servers ]

---
- 키워드: [*] HTTP 메시지 구조 (HTTP message structure)
- 메모: request line, headers, blank line, body라는 구조를 이해해야 connected FD 위에서 어떤 바이트를 읽고 써야 하는지가 드러난다.
- 연결 개념(선택): request, response, `Content-Length`

---
- 키워드: [*] URL / URI / `Host` header
- 메모: 클라이언트는 접속 대상을 보고, 서버는 해석할 자원을 보며, `Host` header는 어느 도메인을 향한 요청인지 구분하는 데 쓰인다.
- 연결 개념(선택): browser view, server view, virtual hosting

---
- 키워드: [*] 정적 콘텐츠 / 동적 콘텐츠 (static content / dynamic content)
- 메모: 파일을 그대로 보내는지, 프로그램 실행 결과를 보내는지의 차이며, 서버는 두 경우를 connected FD 위에서 응답으로 통합한다.
- 연결 개념(선택): static file, generated output, response body

---
- 키워드: [*] CGI / `dup2`
- 메모: CGI는 자식 프로세스의 표준 출력을 클라이언트와 연결된 소켓으로 돌려 응답으로 삼는 구조이며, `dup2`는 그 통로를 바꿔 붙이는 핵심이다.
- 연결 개념(선택): `QUERY_STRING`, stdout redirection, child process

---
- 키워드: 프록시 / 프록시 캐시 / 프록시 체인 (proxy / proxy cache / proxy chain)
- 메모: 중간 서버가 끼어도 결국 request-response와 connected FD 흐름이 반복되며, 캐시와 중계라는 역할이 추가될 뿐이다.
- 연결 개념(선택): browser, proxy, origin server, cache

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 키워드: [*] Tiny의 전체 생애주기 (Tiny lifecycle)
- 메모: `getaddrinfo`, `socket`, `bind`, `listen`, `accept`, `read`, `write`, `close`가 실제 웹 서버 안에서 한 흐름으로 묶여 드러난다.
- 연결 개념(선택): server setup, accept loop, end-to-end flow

---
- 키워드: [*] `main` / `doit`
- 메모: `main`은 서버의 골격을, `doit`는 한 번의 HTTP transaction을 처리하는 내부 흐름을 보여 준다.
- 연결 개념(선택): `Accept`, `Close`, request parsing, transaction

---
- 키워드: `serve_static` / `serve_dynamic`
- 메모: 파일을 body로 응답하는 경로와 CGI 프로그램의 출력을 body로 응답하는 경로를 대비해 보여 준다.
- 연결 개념(선택): file path, CGI path, response generation

---
- 키워드: `mmap` / `dup2`
- 메모: Tiny는 정적 파일 전송에는 `mmap`, 동적 응답 연결에는 `dup2`를 사용해 다른 장의 개념들을 코드 안에서 다시 묶는다.
- 연결 개념(선택): memory mapping, stdout redirection, file descriptor

---
- 키워드: robust server
- 메모: Tiny는 최소 동작 예제이기 때문에 `SIGPIPE`, `EPIPE`, 잘못된 요청, 동시성 같은 현실 문제를 보강해야 견고한 서버가 된다.
- 연결 개념(선택): error handling, concurrency, robustness

<br>
<br>

## [ 11.7 Summary ]

---
- 키워드: [*] 파일 디스크립터 (file descriptor)
- 메모: 네트워크 통신도 결국 Unix I/O의 공통 추상 안에서 다뤄진다는 점이 11장 전체의 가장 큰 연결고리다.
- 연결 개념(선택): handle, FD table, Unix I/O

---
- 키워드: [*] short count / Rio (`Robust I/O`)
- 메모: 네트워크 I/O에서는 한 번에 원하는 양을 모두 처리하지 못해도 자연스럽고, Rio는 이를 안전하게 다루기 위한 실전 도구다.
- 연결 개념(선택): partial read, partial write, `rio_readlineb`

---
- 키워드: [*] 바이트 스트림 / 메시지 경계 (byte stream / message boundary)
- 메모: TCP는 메시지를 자동으로 잘라 주지 않기 때문에 blank line, 길이, EOF 같은 규칙을 애플리케이션이 직접 세워야 한다.
- 연결 개념(선택): framing, `Content-Length`, EOF

---
- 키워드: [*] 동시성으로의 연결 (bridge to concurrency)
- 메모: 11장은 한 연결의 문법을 다루고, 그 문법을 여러 connected FD에 동시에 확장하는 문제가 다음 단계로 이어진다.
- 연결 개념(선택): iterative server, concurrent server, scalability
