# Keywords

학습 중 떠오른 핵심 키워드를 정리하는 공간입니다.

- 각 챕터 안에서 가장 중요하다고 느끼는 개념만 남겼습니다.
- 한 블록에는 가능하면 하나의 키워드만 적어 주세요.
- 필요하면 같은 블록을 복사해서 개인 메모를 덧붙여도 됩니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 키워드: [*] 클라이언트-서버 모델 (client-server model)
- 메모: 네트워크 프로그램을 요청하는 쪽과 자원을 관리하는 쪽의 관계로 보는 기본 틀이다.
- 연결 개념(선택): client, server, 역할 분리

---
- 키워드: [*] 트랜잭션 (transaction)
- 메모: 요청, 해석, 자원 처리, 응답으로 이어지는 한 번의 상호작용 단위다.
- 연결 개념(선택): request-response, service, flow

---
- 키워드: 자원 (resource)
- 메모: 서버가 실제로 관리하고 응답으로 연결하는 대상이다.
- 연결 개념(선택): file, data, service

<br>
<br>

## [ 11.2 Networks ]

---
- 키워드: [*] 네트워크는 I/O 장치 (network as an I/O device)
- 메모: 프로그램 입장에서 네트워크는 결국 바이트를 읽고 쓰는 대상이다.
- 연결 개념(선택): Unix I/O, adapter, memory

---
- 키워드: 프로토콜 (protocol)
- 메모: 누구에게 보내고 어떻게 전달할지를 함께 정하는 통신 규칙이다.
- 연결 개념(선택): naming scheme, delivery mechanism, interoperability

---
- 키워드: [*] encapsulation
- 메모: 상위 계층 데이터가 하위 계층에서 헤더가 붙은 더 큰 단위로 포장되는 과정이다.
- 연결 개념(선택): packet, frame, layering

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 키워드: [*] IP 주소 (IP address)
- 메모: 네트워크가 해석하는 호스트 식별자다.
- 연결 개념(선택): host, routing, IPv4

---
- 키워드: [*] DNS (Domain Name System)
- 메모: 사람이 쓰는 이름을 네트워크 주소와 연결하는 이름 해석 체계다.
- 연결 개념(선택): domain name, resolution, hostname

---
- 키워드: port
- 메모: 한 호스트 안에서 어떤 서비스 종단점에 접근할지 구분하는 번호다.
- 연결 개념(선택): service endpoint, well-known port, ephemeral port

---
- 키워드: socket address
- 메모: `IP address + port`를 묶은 통신 종단점 주소다.
- 연결 개념(선택): endpoint, host, service

---
- 키워드: socket pair
- 메모: 한 연결을 유일하게 식별하는 두 종단점 주소의 쌍이다.
- 연결 개념(선택): client, server, connection identity

---
- 키워드: hton / ntoh (`host to network` / `network to host`)
- 메모: 서로 다른 호스트가 같은 정수 바이트열을 해석하게 만드는 변환 방향이다.
- 연결 개념(선택): network byte order, host byte order, header

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 키워드: [*] `socket`
- 메모: 네트워크 통신의 출발점이 되는 소켓 디스크립터 생성 함수다.
- 연결 개념(선택): descriptor, endpoint, setup

---
- 키워드: [*] `connect` / `bind` / `listen` / `accept`
- 메모: 클라이언트의 연결 수립과 서버의 연결 수락을 이루는 핵심 생애주기 함수들이다.
- 연결 개념(선택): client path, server path, connection lifecycle

---
- 키워드: [*] listening descriptor (`listenfd`)
- 메모: 서버가 계속 연결 요청을 기다리는 입구 디스크립터다.
- 연결 개념(선택): passive socket, accept loop, server socket

---
- 키워드: [*] connected descriptor (`connfd`)
- 메모: 성립된 한 연결에서 실제 읽기와 쓰기를 담당하는 디스크립터다.
- 연결 개념(선택): session, per-connection I/O, client channel

---
- 키워드: [*] `getaddrinfo`
- 메모: 이름과 서비스 문자열을 실제 소켓 주소 후보들로 바꿔 주는 현대적 인터페이스다.
- 연결 개념(선택): protocol-independent, addrinfo list, resolution

---
- 키워드: Rio (Robust I/O)
- 메모: short count와 줄 단위 텍스트 I/O를 안전하게 다루기 위한 robust I/O 계층이다.
- 연결 개념(선택): `Rio_readlineb`, `Rio_writen`, buffered I/O

<br>
<br>

## [ 11.5 Web Servers ]

---
- 키워드: [*] HTTP (HyperText Transfer Protocol)
- 메모: 연결 위에서 요청과 응답을 주고받는 웹의 애플리케이션 프로토콜이다.
- 연결 개념(선택): request, response, web

---
- 키워드: URL / URI (`Uniform Resource Locator` / `Uniform Resource Identifier`)
- 메모: 클라이언트는 접속 대상을 보고, 서버는 해석할 자원 대상을 본다.
- 연결 개념(선택): host, port, path

---
- 키워드: [*] blank line
- 메모: HTTP에서 헤더와 본문의 경계를 알려 주는 구조적 구분선이다.
- 연결 개념(선택): parser, header termination, body

---
- 키워드: [*] 정적 콘텐츠 / 동적 콘텐츠 (static content / dynamic content)
- 메모: 파일을 그대로 보내는지, 프로그램 실행 결과를 보내는지의 차이다.
- 연결 개념(선택): file serving, CGI, generated output

---
- 키워드: CGI (Common Gateway Interface)
- 메모: 자식 프로세스의 출력을 HTTP 응답과 연결하는 고전적 동적 콘텐츠 모델이다.
- 연결 개념(선택): `QUERY_STRING`, `fork`, `execve`

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 키워드: [*] Tiny
- 메모: sockets, Rio, HTTP, file I/O, process control이 한 프로그램에 모인 교육용 웹 서버다.
- 연결 개념(선택): iterative server, HTTP/1.0, CS:APP

---
- 키워드: [*] `doit`
- 메모: 한 번의 HTTP transaction을 읽고 해석하고 응답으로 분기하는 Tiny의 핵심 함수다.
- 연결 개념(선택): request parsing, dispatch, transaction

---
- 키워드: `serve_static` / `serve_dynamic`
- 메모: 파일 응답과 CGI 실행 응답이라는 두 서비스 경로를 보여 주는 함수 쌍이다.
- 연결 개념(선택): file path, CGI path, response generation

---
- 키워드: `mmap`
- 메모: 정적 파일을 메모리에 매핑해 네트워크로 보내는 Tiny의 대표적인 구현 포인트다.
- 연결 개념(선택): file mapping, static response, virtual memory

<br>
<br>

## [ 11.7 Summary ]

---
- 키워드: [*] 파일 디스크립터 (file descriptor)
- 메모: 소켓도 결국 Unix I/O의 공통 추상 안에서 다뤄진다는 관점을 준다.
- 연결 개념(선택): socket, Unix I/O, abstraction

---
- 키워드: [*] short count
- 메모: `read`나 `write`가 요청한 양보다 적게 처리하고도 정상일 수 있다는 네트워크 I/O의 현실이다.
- 연결 개념(선택): partial read, partial write, network reality

---
- 키워드: [*] 바이트 스트림 (byte stream)
- 메모: TCP가 제공하는 기본 추상이며, 애플리케이션이 그 위에 메시지 의미를 직접 세운다.
- 연결 개념(선택): TCP, stream, message construction

---
- 키워드: [*] 메시지 경계 (message boundary)
- 메모: 네트워크가 자동으로 주지 않기 때문에 blank line, 길이, EOF 같은 규칙으로 직접 정해야 한다.
- 연결 개념(선택): framing, `Content-Length`, EOF
