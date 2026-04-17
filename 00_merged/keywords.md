# Keywords

네 사람의 키워드를 통합한 문서입니다.

- 중복되는 키워드는 하나로 합쳤습니다.
- 공통 핵심은 본문에 두고, 서로 다른 관점은 메모와 연결 개념에 묶었습니다.
- 11장 전체를 다시 읽을 때 길을 잃지 않도록 핵심 축 위주로 정리했습니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 키워드: [*] 클라이언트-서버 모델 (client-server model)
- 메모: 네트워크 프로그램을 요청하는 쪽과 자원을 관리하는 쪽의 관계로 보는 기본 틀이며, 두 프로세스는 같은 호스트에 있을 수도 있고 다른 호스트에 있을 수도 있다.
- 연결 개념(선택): client, server, role separation, localhost

---
- 키워드: [*] 트랜잭션 (transaction)
- 메모: 요청, 해석, 자원 처리, 응답으로 이어지는 한 번의 상호작용 단위다.
- 연결 개념(선택): request-response, service flow, end-to-end

---
- 키워드: 자원 / 서비스 (resource / service)
- 메모: 서버는 자원을 관리하고, 서비스는 그 자원에 대한 규칙 있는 접근 방식으로 드러난다.
- 연결 개념(선택): file, database, response

<br>
<br>

## [ 11.2 Networks ]

---
- 키워드: [*] 네트워크는 I/O 장치 (network as an I/O device)
- 메모: 프로그램 입장에서 네트워크는 결국 바이트를 읽고 쓰는 대상이며, 소켓이 파일 디스크립터처럼 보이는 이유도 여기서 나온다.
- 연결 개념(선택): Unix I/O, adapter, memory

---
- 키워드: [*] 프로토콜 / 캡슐화 (protocol / encapsulation)
- 메모: 프로토콜은 누구에게 보낼지와 어떻게 전달할지를 함께 정하고, 캡슐화는 그 데이터가 계층을 내려가며 packet과 frame으로 포장되는 과정이다.
- 연결 개념(선택): naming scheme, delivery mechanism, packet, frame

---
- 키워드: 네트워크 진입 설정 (`DHCP` / subnet mask / default gateway / routing table)
- 메모: 실제 통신 가능 상태는 단순 와이파이 연결보다 넓은 개념이며, IP 설정과 기본 경로가 잡혀야 외부 네트워크로 나갈 수 있다.
- 연결 개념(선택): local DNS server, interface, next hop

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 키워드: [*] DNS / resolver (`Domain Name System` / resolver)
- 메모: 사람이 입력한 이름을 네트워크 주소로 풀어 주는 체계이며, 실제 조회는 브라우저, OS resolver, local DNS server, cache가 함께 엮여 일어난다.
- 연결 개념(선택): browser cache, OS cache, local DNS server

---
- 키워드: [*] IP 주소 / 포트 / 소켓 주소 / 소켓 쌍 (IP address / port / socket address / socket pair)
- 메모: 호스트 식별, 서비스 식별, 통신 종단점, 연결 식별이 각각 다른 층위의 개념임을 구분해야 한다.
- 연결 개념(선택): endpoint, well-known port, ephemeral port, 4-tuple

---
- 키워드: [*] byte order / serialization
- 메모: 메모리 안의 struct를 그대로 보내는 것이 아니라, padding과 endian 차이를 넘어 wire format으로 직렬화해야 서로 다른 시스템이 같은 값을 해석할 수 있다.
- 연결 개념(선택): hton, ntoh, network byte order, padding

---
- 키워드: [*] 연결 (connection)
- 메모: TCP connection은 point-to-point, full-duplex, reliable byte stream으로 이해하는 것이 11장 전체의 해석 기준이 된다.
- 연결 개념(선택): TCP, byte stream, HTTP

---
- 키워드: IPv4 / IPv6
- 메모: 교재의 많은 예시는 IPv4 중심이지만, 실제 인터페이스는 주소 체계에 덜 묶이도록 설계되어 있다는 점도 같이 봐야 한다.
- 연결 개념(선택): protocol-independent code, `getaddrinfo`

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 키워드: [*] 소켓 (socket)
- 메모: 네트워크 통신의 끝점(endpoint)을 표현하는 커널 객체이며, 프로그램은 그 객체를 file descriptor를 통해 다룬다.
- 연결 개념(선택): kernel object, endpoint, socket descriptor

---
- 키워드: [*] BSD 소켓 인터페이스 (BSD sockets interface)
- 메모: `socket`, `connect`, `bind`, `listen`, `accept`로 이어지는 사실상의 표준 API이며, 오래된 `sockaddr *` 형태가 남아 있는 것은 역사와 호환성의 결과다.
- 연결 개념(선택): `sockaddr`, API, system call, compatibility

---
- 키워드: [*] `connect` / `bind` / `listen` / `accept`
- 메모: 클라이언트의 능동 연결과 서버의 수동 수락을 이루는 핵심 생애주기 함수들이며, 이 순서를 흐름으로 이해해야 한다.
- 연결 개념(선택): active open, passive open, connection lifecycle

---
- 키워드: [*] listening descriptor (`listenfd`) / connected descriptor (`connfd`)
- 메모: 서버의 입구와 개별 대화 채널을 분리하는 설계로, 왜 `accept()`가 새 FD를 돌려주는지도 여기서 설명된다.
- 연결 개념(선택): listening socket, connected socket, accept queue

---
- 키워드: [*] `getaddrinfo`
- 메모: 이름과 서비스 문자열을 주소 후보 리스트로 바꿔 주는 현대적 인터페이스이며, 클라이언트는 name resolution에, 서버는 `AI_PASSIVE`와 wildcard address에 활용한다.
- 연결 개념(선택): `sockaddr`, `AI_PASSIVE`, protocol-independent code

---
- 키워드: ephemeral port / implicit bind
- 메모: 클라이언트는 보통 `bind()`를 직접 호출하지 않아도 `connect()` 과정에서 단기 포트가 자동 할당된다.
- 연결 개념(선택): client port, auto bind, socket pair

---
- 키워드: 소켓 버퍼 / 큐 (socket buffer / queue)
- 메모: 일반 connected socket의 데이터 버퍼와 listening socket의 연결 대기열은 역할이 다르며, `listen()`과 `accept()`는 이 차이를 드러낸다.
- 연결 개념(선택): receive buffer, send buffer, accept queue

<br>
<br>

## [ 11.5 Web Servers ]

---
- 키워드: [*] HTTP 메시지 구조 (HTTP message structure)
- 메모: request line, headers, blank line, body라는 구조를 이해해야 connected FD 위에서 어떤 텍스트를 읽고 써야 하는지가 보인다.
- 연결 개념(선택): request, response, `Content-Length`

---
- 키워드: [*] URL / URI / `Host` header
- 메모: 클라이언트는 접속 대상을 보고, 서버는 해석할 자원을 보며, `Host` header는 같은 서버 주소 위에서도 어느 도메인을 향한 요청인지 알려 준다.
- 연결 개념(선택): browser view, server view, virtual hosting

---
- 키워드: [*] 정적 콘텐츠 / 동적 콘텐츠 (static content / dynamic content)
- 메모: 파일을 그대로 보내는지, 프로그램 실행 결과를 보내는지의 차이며, connected FD는 두 경우 모두 최종 응답 통로가 된다.
- 연결 개념(선택): file serving, generated output, response body

---
- 키워드: [*] CGI / `dup2`
- 메모: CGI는 자식 프로세스의 표준 출력을 클라이언트와 연결된 소켓으로 돌려 응답의 일부로 만드는 고전적 모델이다.
- 연결 개념(선택): `QUERY_STRING`, `fork`, `execve`, stdout redirection

---
- 키워드: 프록시 / 프록시 캐시 / 프록시 체인 (proxy / proxy cache / proxy chain)
- 메모: 브라우저와 원본 서버 사이에 중간 서버가 끼어도 결국 request-response와 connected FD의 흐름이 반복된다는 관점으로 이해할 수 있다.
- 연결 개념(선택): browser, proxy, origin server

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 키워드: [*] Tiny
- 메모: sockets, Rio, HTTP, file I/O, process control이 한 프로그램 안에서 어떻게 결합되는지를 보여 주는 교육용 웹 서버다.
- 연결 개념(선택): iterative server, HTTP/1.0, CS:APP

---
- 키워드: [*] `main` / `doit`
- 메모: `main`은 서버의 뼈대를, `doit`는 한 번의 HTTP transaction 처리 흐름을 보여 준다.
- 연결 개념(선택): `Accept`, `Close`, request parsing, dispatch

---
- 키워드: `serve_static` / `serve_dynamic`
- 메모: 파일을 body로 보내는 경로와 CGI 프로그램 출력을 body로 보내는 경로를 대비해 보여 준다.
- 연결 개념(선택): static path, CGI path, response generation

---
- 키워드: `mmap` / `dup2`
- 메모: Tiny는 정적 파일 전송에는 `mmap`을, 동적 응답 연결에는 `dup2`를 써서 여러 장의 내용을 한 코드 안에 묶어 보여 준다.
- 연결 개념(선택): memory mapping, stdout redirection, file descriptor

---
- 키워드: robust server
- 메모: Tiny는 본질을 잘 보여 주지만, `SIGPIPE`, `EPIPE`, 잘못된 입력, 동시성 같은 현실 문제를 더 다뤄야 비로소 견고한 서버가 된다.
- 연결 개념(선택): error handling, concurrency, premature close

<br>
<br>

## [ 11.7 Summary ]

---
- 키워드: [*] 파일 디스크립터 (file descriptor)
- 메모: 소켓도 결국 Unix I/O의 공통 추상 안에서 다뤄지는 열린 파일 객체라는 사실이 11장의 바닥을 이룬다.
- 연결 개념(선택): handle, FD table, Unix I/O

---
- 키워드: [*] short count / Rio (`Robust I/O`)
- 메모: 네트워크 I/O에서는 한 번에 원하는 양을 다 읽거나 쓰지 못해도 자연스럽기 때문에, Rio 같은 안전한 래퍼가 반복해서 등장한다.
- 연결 개념(선택): `rio_readlineb`, `rio_writen`, partial read

---
- 키워드: [*] 바이트 스트림 / 메시지 경계 (byte stream / message boundary)
- 메모: TCP는 메시지를 자동으로 잘라 주지 않으므로 blank line, 길이, EOF 같은 규칙을 애플리케이션이 직접 세워야 한다.
- 연결 개념(선택): framing, `Content-Length`, EOF

---
- 키워드: [*] 동시성으로의 다리 (bridge to concurrency)
- 메모: 11장은 한 연결의 문법을 다루고, 그 위에 여러 connected FD를 동시에 관리하는 문제가 다음 장으로 이어진다.
- 연결 개념(선택): iterative server, concurrent server, scalability
