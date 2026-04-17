# Questions

네 사람의 질문을 통합한 문서입니다.

- 같은 핵심을 묻는 질문은 하나로 합쳤습니다.
- 서로 다른 관심사는 배경과 메모에 묶어 두었습니다.
- 답을 바로 적기보다, 함께 보면서 생각의 방향을 정리하기 좋은 질문 위주로 남겼습니다.

## [ 11.1 The Client-Server Programming Model ]

---
- 질문: [*] 서버를 “응답하는 쪽”보다 “자원을 관리하는 쪽”으로 이해하면 무엇이 더 선명해질까?
- 배경/맥락: 서버의 본질을 자원 관리 책임으로 보면 HTTP, CGI, Tiny가 같은 축에 놓인다.
- 메모(선택): 같은 호스트와 다른 호스트의 경우에도 역할 분리는 유지되는지 같이 떠올려 보기

---
- 질문: [*] 한 번의 트랜잭션 (transaction)을 클라이언트와 서버 양쪽 흐름으로 끊기지 않게 설명할 수 있는가?
- 배경/맥락: 11장 전체는 결국 한 번의 연결과 한 번의 요청-응답을 여러 높이에서 반복해 보여 준다.
- 메모(선택): client flow와 server flow가 어디까지 같고 어디서 갈라지는지 정리해 보기

<br>
<br>

## [ 11.2 Networks ]

---
- 질문: [*] 네트워크를 I/O 장치 (network as an I/O device)로 보면 소켓이 왜 덜 낯설어질까?
- 배경/맥락: 디스크나 터미널과 마찬가지로 바이트를 읽고 쓰는 대상으로 보면 네트워크 프로그래밍이 더 구체적으로 보인다.
- 메모(선택): 어댑터, 메모리, `read`/`write` 관점으로 떠올려 보기

---
- 질문: 와이파이에 연결되었다는 것과 실제 통신이 가능하다는 것은 왜 다른 말일까?
- 배경/맥락: 실제 네트워크 진입에는 `DHCP`, subnet mask, default gateway, routing table 같은 설정이 함께 맞물린다.
- 메모(선택): local DNS server, next hop, network unreachable를 같이 연결해 보기

---
- 질문: 프로토콜 (protocol)과 encapsulation을 함께 이해하면 “내 프로그램의 데이터가 실제로 어디를 어떻게 지나가는가”가 어떻게 달라져 보일까?
- 배경/맥락: 계층과 포장 구조를 보면 frame, packet, header의 역할이 한 화면에 들어온다.
- 메모(선택): naming scheme과 delivery mechanism을 분리해서 생각해 보기

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 질문: [*] 사용자가 도메인을 입력한 순간부터 IP 주소를 얻기까지, 브라우저와 OS resolver와 local DNS server는 각각 어떤 역할을 할까?
- 배경/맥락: 이름 해석은 “DNS가 알아서 한다”로 끝나지 않고, cache와 resolver와 실제 질의가 함께 엮여 있다.
- 메모(선택): browser cache, OS cache, UDP 53, cache hit 시 생략되는 단계 떠올려 보기

---
- 질문: [*] IP 주소, port, socket address, socket pair를 구분해 설명하면 “서버를 찾는다”는 말은 정확히 무엇이 될까?
- 배경/맥락: 호스트 식별, 서비스 식별, 연결 식별은 서로 다른 층위의 문제다.
- 메모(선택): 같은 서버 포트로 여러 클라이언트가 붙는 상황을 예로 들어 보기

---
- 질문: struct가 메모리에 연속적으로 놓여 있는데도 왜 serialization과 byte order 변환이 필요할까?
- 배경/맥락: padding, endian, wire format 차이를 넘겨야 서로 다른 시스템이 같은 메시지를 이해할 수 있다.
- 메모(선택): `sizeof(struct)`, `hton`, `ntoh`, network byte order를 같이 생각해 보기

---
- 질문: connection을 reliable byte stream으로 이해하면 이후 HTTP를 보는 눈이 어떻게 달라질까?
- 배경/맥락: TCP connection의 추상은 HTTP message boundary 문제와 바로 연결된다.
- 메모(선택): “바이트 스트림은 목적지를 자동으로 알지 않는다”는 점도 함께 떠올려 보기

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 질문: [*] 왜 소켓 인터페이스는 `socket` 하나로 끝나지 않고 `connect`, `bind`, `listen`, `accept`로 나뉘어 있을까?
- 배경/맥락: 소켓은 한 번에 완성되는 객체가 아니라 상태가 정해져 가는 통신 종단점이다.
- 메모(선택): active socket, passive socket, listening socket을 나란히 그려 보기

---
- 질문: [*] socket, endpoint, socket address, socket descriptor, file descriptor는 각각 무엇이 다를까?
- 배경/맥락: 같은 “소켓”이라는 말이 주소, 커널 객체, 정수 핸들, API를 섞어 가리킬 때가 많아 헷갈리기 쉽다.
- 메모(선택): kernel object, FD table, socket pair를 함께 떠올려 보기

---
- 질문: 왜 오늘날에도 소켓 인터페이스는 `sockaddr *` 같은 오래된 형태를 유지하고, 대신 `getaddrinfo()` 같은 추상화를 덧붙이는 걸까?
- 배경/맥락: BSD socket API의 역사와 호환성, 현대적인 protocol-independent code의 필요가 함께 얽혀 있다.
- 메모(선택): API 규격, system call, compatibility, `AI_PASSIVE`

---
- 질문: [*] 왜 `accept()`는 listening FD를 그대로 쓰지 않고 새로운 connected FD를 돌려줄까?
- 배경/맥락: 서버는 계속 새 연결을 기다리는 입구와, 특정 클라이언트와 대화하는 채널을 동시에 유지해야 한다.
- 메모(선택): same server port, 4-tuple demultiplexing, accept queue를 함께 생각해 보기

---
- 질문: 클라이언트는 왜 보통 `bind()`를 직접 호출하지 않으며, “단기 포트(ephemeral port)”는 어떤 역할을 할까?
- 배경/맥락: 서버는 well-known port를 점유하지만 클라이언트는 `connect()` 과정에서 포트가 자동 할당되는 경우가 많다.
- 메모(선택): implicit bind, socket pair, unique client process

---
- 질문: 소켓은 단순한 FIFO 큐일까, 아니면 더 많은 상태를 가진 커널 객체일까?
- 배경/맥락: TCP 소켓은 송수신 버퍼뿐 아니라 연결 상태, 재전송, 순서 보장 같은 더 무거운 책임을 가진다.
- 메모(선택): send buffer, receive buffer, state machine, system call

<br>
<br>

## [ 11.5 Web Servers ]

---
- 질문: [*] URL / URI / `Host` header를 클라이언트 관점과 서버 관점으로 나누어 보면 무엇이 달라질까?
- 배경/맥락: 같은 문자열도 브라우저는 접속 대상을, 서버는 해석할 자원을 중심으로 읽는다.
- 메모(선택): virtual hosting과 proxy 상황도 같이 떠올려 보기

---
- 질문: [*] HTTP에서 blank line, `Content-Length`, body는 왜 중요할까?
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

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 질문: [*] Tiny Web Server 안에서 `getaddrinfo()`, `socket()`, `bind()`, `listen()`, `accept()`, `read()`, `write()`는 어떤 순서로 이어질까?
- 배경/맥락: 개별 함수의 의미를 실제 웹 서버의 end-to-end 흐름으로 다시 묶어 보는 질문이다.
- 메모(선택): `main`, `doit`, `Accept -> doit -> Close`

---
- 질문: [*] `serve_static`과 `serve_dynamic`은 둘 다 HTTP response를 만들지만 무엇이 가장 다를까?
- 배경/맥락: 하나는 파일을 body로 삼고, 다른 하나는 프로그램 실행 결과를 body로 삼는다.
- 메모(선택): `mmap`, `dup2`, CGI path, file path

---
- 질문: Tiny를 읽고 나면 왜 “동작하는 서버”와 “견고한 서버”를 구분하게 될까?
- 배경/맥락: Tiny는 본질을 잘 보여 주는 최소 구현이지만, 현실 서버가 다뤄야 할 오류와 확장성 문제는 의도적으로 많이 생략되어 있다.
- 메모(선택): `SIGPIPE`, `EPIPE`, bad request, concurrency

<br>
<br>

## [ 11.7 Summary ]

---
- 질문: [*] 소켓을 파일 디스크립터라는 공통 추상으로 이해해야 하는 이유는 무엇일까?
- 배경/맥락: 11장은 네트워크를 배우는 장이면서 동시에 Unix I/O가 네트워크로 확장되는 장이다.
- 메모(선택): file descriptor table, handle, kernel object를 같이 떠올려 보기

---
- 질문: [*] short count와 Rio (Robust I/O)를 함께 이해하지 않으면 어떤 오해가 생길까?
- 배경/맥락: 네트워크 I/O에서는 한 번에 원하는 양을 모두 처리하지 못해도 자연스럽고, 이를 다루는 습관이 중요하다.
- 메모(선택): partial read, partial write, `rio_readlineb`, `rio_writen`

---
- 질문: [*] TCP를 reliable byte stream으로 이해하지 못하면 왜 메시지 경계 (message boundary)와 동시성 문제에서 함께 헷갈리게 될까?
- 배경/맥락: 11장은 한 연결의 문법을, 다음 단계는 여러 connected FD를 동시에 다루는 구조를 가리킨다.
- 메모(선택): blank line, EOF, `Content-Length`, iterative server, concurrent server
