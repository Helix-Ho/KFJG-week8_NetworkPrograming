# Questions

네 사람의 질문을 다시 통합한 문서입니다.

- 개별 문서의 궁금증과 탐구 흐름을 먼저 중심에 두고 묶었습니다.
- 빠지기 쉬운 11장 핵심 질문은 보완하되, 질문의 출발점은 각자의 호기심에 두었습니다.
- 중복되는 질문은 합치고, 서로 다른 관심사는 배경과 메모에 함께 담았습니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 질문: [*] “application이 통신을 시작한다”는 말에서 실제 주체는 누구이며, client와 server는 어떤 기준으로 나뉠까?
- 배경/맥락: browser, resolver, client process, server process가 한 흐름에 섞여 등장할 때 주체가 흐려지기 쉽다.
- 메모(선택): 같은 호스트 안의 통신과 다른 호스트 사이 통신도 함께 떠올려 보기

---
- 질문: [*] 한 번의 트랜잭션 (transaction)을 클라이언트와 서버 양쪽 흐름으로 끊기지 않게 설명할 수 있는가?
- 배경/맥락: 11장 전체는 결국 한 번의 요청과 한 번의 연결을 다양한 높이에서 반복해서 보여 준다.
- 메모(선택): request, 처리, response 순서를 말로 풀어 보기

---
- 질문: 같은 대상을 system, protocol, API, 자료구조처럼 다르게 부를 때 무엇을 기준으로 구분해야 할까?
- 배경/맥락: DNS나 socket 같은 용어는 보는 층위에 따라 전혀 다르게 설명될 수 있다.
- 메모(선택): abstraction level이라는 관점으로 정리해 보기

<br>
<br>

## [ 11.2 Networks ]

---
- 질문: [*] 와이파이에 연결되었다는 것과 실제 네트워크 통신이 가능한 상태는 왜 다를까?
- 배경/맥락: 실제 통신 가능 상태는 물리 연결뿐 아니라 IP 설정, gateway, routing 정보까지 갖춰졌는지를 포함한다.
- 메모(선택): `DHCP`, subnet mask, default gateway, routing table을 함께 떠올려 보기

---
- 질문: 네트워크를 I/O 장치 (network as an I/O device)로 보면 무엇이 더 구체적으로 보일까?
- 배경/맥락: 네트워크를 특별한 공간이 아니라 바이트를 읽고 쓰는 대상으로 보면 소켓과 OS의 역할이 선명해진다.
- 메모(선택): adapter, memory, `read`/`write`

---
- 질문: 프로토콜 (protocol)과 encapsulation을 함께 보면 “내 프로그램의 데이터가 실제로 어떻게 이동하는가”가 어떻게 달라져 보일까?
- 배경/맥락: 계층과 포장 구조를 보면 packet, frame, header가 각자 무엇을 담당하는지 이어서 이해할 수 있다.
- 메모(선택): naming scheme과 delivery mechanism을 분리해 보기

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 질문: [*] 사용자가 도메인을 입력한 순간부터 IP 주소를 얻기까지, 브라우저와 OS resolver와 local DNS server는 각각 무엇을 할까?
- 배경/맥락: 이름 해석은 브라우저 혼자도, DNS 서버 혼자도 처리하지 않고 cache와 resolver와 실제 질의가 함께 엮인다.
- 메모(선택): browser cache, OS cache, local DNS, UDP 53

---
- 질문: [*] IP 주소, port, socket address, socket pair를 구분해 설명하면 “서버를 찾는다”는 말은 정확히 무엇이 될까?
- 배경/맥락: 호스트 식별, 서비스 식별, 연결 식별은 서로 다른 층위의 문제다.
- 메모(선택): 같은 서버 포트에 여러 클라이언트가 붙는 상황을 예로 들어 보기

---
- 질문: struct가 메모리에 연속적으로 놓여 있는데도 왜 serialization과 byte order 변환이 필요할까?
- 배경/맥락: 메모리 표현과 wire format이 같지 않기 때문에 padding과 endian 문제를 넘어야 한다.
- 메모(선택): `sizeof(struct)`, padding, `hton`, `ntoh`

---
- 질문: DNS에서는 보통 UDP를 쓰고, 웹 통신은 TCP 연결 위에서 이루어진다는 사실이 전체 흐름을 어떻게 나눠 보여 줄까?
- 배경/맥락: 이름을 묻는 통신과 실제 서비스를 받는 통신은 목적과 전송 특성이 다르다.
- 메모(선택): DNS over UDP, HTTP over TCP, port space

---
- 질문: connection을 reliable byte stream으로 이해하면 이후 HTTP를 보는 눈이 어떻게 달라질까?
- 배경/맥락: TCP의 추상은 HTTP message boundary 문제와 바로 이어진다.
- 메모(선택): 바이트 스트림과 메시지의 차이를 같이 생각해 보기

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 질문: [*] socket, endpoint, socket address, socket descriptor, file descriptor는 각각 무엇이 다를까?
- 배경/맥락: 같은 “소켓”이라는 말이 주소, 커널 객체, 정수 핸들, API를 섞어 가리킬 때가 많다.
- 메모(선택): kernel object, socket pair, FD table을 한 그림에 놓아 보기

---
- 질문: [*] 왜 소켓 인터페이스는 `socket` 하나로 끝나지 않고 `connect`, `bind`, `listen`, `accept`로 나뉘어 있을까?
- 배경/맥락: 소켓은 한 번에 완성되는 대상이 아니라 역할과 상태가 단계적으로 정해지는 객체다.
- 메모(선택): active socket, passive socket, listening socket

---
- 질문: 왜 오늘날에도 소켓 인터페이스는 `sockaddr *` 같은 오래된 형태를 유지하고, 대신 `getaddrinfo()` 같은 추상화를 덧붙이는 걸까?
- 배경/맥락: BSD socket API의 역사와 호환성, 현대적인 protocol-independent code의 필요가 함께 얽혀 있다.
- 메모(선택): API, system call, compatibility, `AI_PASSIVE`

---
- 질문: [*] 왜 `accept()`는 listening FD를 그대로 쓰지 않고 새로운 connected FD를 돌려줄까?
- 배경/맥락: 서버는 계속 새 연결을 기다리는 입구와, 특정 클라이언트와 실제로 대화하는 채널을 동시에 유지해야 한다.
- 메모(선택): same server port, 4-tuple, demultiplexing, accept queue

---
- 질문: 클라이언트는 왜 보통 `bind()`를 직접 호출하지 않으며, 단기 포트(ephemeral port)는 어떤 역할을 할까?
- 배경/맥락: 서버는 well-known port를 명시적으로 점유하지만, 클라이언트 포트는 `connect()` 과정에서 자동 할당되는 경우가 많다.
- 메모(선택): implicit bind, unique client process, socket pair

---
- 질문: 소켓은 단순한 FIFO 큐일까, 아니면 더 많은 상태를 가진 커널 객체일까?
- 배경/맥락: 송수신 버퍼뿐 아니라 연결 상태, 재전송, 순서 보장, 큐 관리까지 포함하면 소켓은 더 무거운 객체처럼 보인다.
- 메모(선택): send buffer, receive buffer, state machine, system call

<br>
<br>

## [ 11.5 Web Servers ]

---
- 질문: [*] URL / URI / `Host` header를 클라이언트 관점과 서버 관점으로 나누어 보면 무엇이 달라질까?
- 배경/맥락: 같은 문자열도 브라우저는 접속 대상을, 서버는 해석할 자원을 중심으로 읽는다.
- 메모(선택): virtual hosting과 proxy 상황도 같이 떠올려 보기

---
- 질문: [*] HTTP에서 blank line, `Content-Length`, body는 왜 중요한가?
- 배경/맥락: TCP가 reliable byte stream을 준다고 해서 메시지 경계까지 자동으로 주는 것은 아니다.
- 메모(선택): request line, headers, blank line, body 순서를 같이 적어 보기

---
- 질문: 정적 콘텐츠와 동적 콘텐츠는 서버 입장에서 무엇이 가장 다르며, connected FD는 두 경우에 어떻게 쓰일까?
- 배경/맥락: 파일을 보내는 일과 프로그램 실행 결과를 보내는 일은 응답 생성 방식이 다르지만 결국 하나의 FD로 흘러간다.
- 메모(선택): static file, CGI, response body

---
- 질문: CGI 프로그램의 출력을 `dup2()`로 connected socket 쪽에 붙이는 이유는 무엇일까?
- 배경/맥락: 부모 서버와 자식 프로그램이 누가 어떤 응답 정보를 더 잘 아는지 나눠 갖는 구조가 CGI의 핵심이다.
- 메모(선택): stdout redirection, `QUERY_STRING`, child process

---
- 질문: 프록시와 프록시 캐시는 원본 웹 서버와 무엇이 같고 무엇이 다를까?
- 배경/맥락: 브라우저와 서버 사이에 중간 서버가 끼어도 request-response 구조가 반복된다는 점을 함께 볼 수 있다.
- 메모(선택): proxy chain, cache, origin server

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 질문: [*] Tiny Web Server 안에서 `getaddrinfo()`, `socket()`, `bind()`, `listen()`, `accept()`, `read()`, `write()`, `close()`는 어떤 순서로 이어질까?
- 배경/맥락: 개별 함수의 의미를 실제 서버 코드의 end-to-end 흐름으로 다시 묶어 보는 질문이다.
- 메모(선택): `main`, `doit`, `Accept -> doit -> Close`

---
- 질문: [*] `serve_static`과 `serve_dynamic`은 둘 다 HTTP response를 만들지만 무엇이 가장 다를까?
- 배경/맥락: 하나는 파일을 body로 삼고, 다른 하나는 프로그램 실행 결과를 body로 삼는다.
- 메모(선택): `mmap`, `dup2`, file path, CGI path

---
- 질문: listening FD와 connected FD는 Tiny 안에서 각각 언제까지 살아 있고 언제 닫힐까?
- 배경/맥락: 서버의 입구와 개별 요청 처리 채널의 생명주기를 코드 흐름 안에서 구분해 보는 질문이다.
- 메모(선택): long-lived FD, per-client FD, `close()`

---
- 질문: Tiny를 읽고 나면 왜 “동작하는 서버”와 “견고한 서버”를 구분하게 될까?
- 배경/맥락: Tiny는 본질을 잘 보여 주지만 현실 서버가 다뤄야 할 오류와 확장성 문제는 많이 생략되어 있다.
- 메모(선택): `SIGPIPE`, `EPIPE`, bad request, concurrency

<br>
<br>

## [ 11.7 Summary ]

---
- 질문: [*] 도메인을 입력한 순간부터 TCP 연결이 성립할 때까지의 전체 흐름을 한 번에 어떻게 설명할 수 있을까?
- 배경/맥락: DNS, resolver, socket, file descriptor, `connect()`가 결국 하나의 긴 서사를 이룬다.
- 메모(선택): 이름 해석, 목적지 결정, 연결 수립 순서를 이어서 말해 보기

---
- 질문: [*] 소켓을 파일 디스크립터라는 공통 추상으로 이해해야 하는 이유는 무엇일까?
- 배경/맥락: 11장은 네트워크를 배우는 장이면서 동시에 Unix I/O가 네트워크로 확장되는 장이다.
- 메모(선택): handle, FD table, kernel object를 같이 떠올려 보기

---
- 질문: [*] short count와 Rio (Robust I/O)를 함께 이해하지 않으면 어떤 오해가 생길까?
- 배경/맥락: 네트워크 I/O에서는 한 번에 원하는 양을 모두 처리하지 못해도 자연스럽고, 이를 다루는 습관이 중요하다.
- 메모(선택): partial read, partial write, `rio_readlineb`, `rio_writen`

---
- 질문: [*] TCP를 reliable byte stream으로 이해하지 못하면 왜 메시지 경계 (message boundary)와 동시성 문제에서 함께 헷갈리게 될까?
- 배경/맥락: 11장은 한 연결의 문법을, 다음 단계는 여러 connected FD를 동시에 다루는 구조를 가리킨다.
- 메모(선택): blank line, EOF, `Content-Length`, iterative server, concurrent server
