# Questions

## [ 11.1 The Client-Server Programming Model ]

---
- 질문: 네트워크 프로그램의 실제 주체는 누구이며, client와 server는 어떤 기준으로 나뉠까?
- 배경/맥락: browser, resolver, client process, server process가 한 흐름에 섞여 등장하면 “누가 통신을 시작하는가”와 “누가 서비스를 제공하는가”가 쉽게 흐려진다.
- 생각거리 1: 같은 호스트 안에서 client와 server가 만나는 경우와, 서로 다른 호스트에서 만나는 경우는 무엇이 같고 무엇이 다를까?
- 생각거리 2: “application이 통신을 시작한다”는 문장을 browser, resolver, client process의 역할로 나누어 다시 말하면 어떻게 정리될까?
- 메모(힌트): host보다 process를 먼저 떠올리고, 구분 기준을 “요청 시작”과 “자원 관리”에 두면 정리가 쉽다.

---
- 질문: 한 번의 transaction을 client와 server 양쪽 흐름으로 끊기지 않게 설명할 수 있을까?
- 배경/맥락: 11장은 Echo, HTTP, Tiny를 통해 같은 상호작용을 서로 다른 높이에서 반복해서 보여 주므로, 요청-처리-응답의 한 바퀴를 말로 풀어낼 수 있어야 한다.
- 생각거리 1: Echo, HTTP, Tiny를 모두 `request -> processing -> response` 틀에 올려놓으면 무엇이 공통으로 남을까?
- 생각거리 2: client 쪽 흐름과 server 쪽 흐름은 어디까지 대칭적이고, 어디서부터 역할이 갈라질까?
- 메모(힌트): 세부 함수 이름보다 먼저 “누가 먼저 시작하고, 누가 자원을 해석하고, 무엇을 돌려주는가”를 따라가 보자.

---
- 질문: 같은 대상을 system, protocol, API, 자료구조처럼 다르게 부를 때 무엇을 기준으로 구분해야 할까?
- 배경/맥락: DNS, socket, HTTP 같은 용어는 설명 관점에 따라 전혀 다른 층위의 말처럼 들려서, 학습 순서 자체가 흔들리기 쉽다.
- 생각거리 1: BSD sockets, IP, TCP, HTTP, DNS, file descriptor를 한 문장으로 나열하지 말고 층위별로 다시 묶으면 어떻게 정리될까?
- 생각거리 2: DNS가 “이름 해석 시스템”이면서 동시에 “프로토콜”이라고 불리는 이유는 무엇일까?
- 메모(힌트): 서비스 모델, 전송 규약, 프로그래머 인터페이스, 메모리 표현이라는 네 층위를 분리해 보면 겹침이 줄어든다.

<br>
<br>

## [ 11.2 Networks ]

---
- 질문: 네트워크를 I/O 장치로 보면 kernel, file descriptor, system call의 역할은 어떻게 정리될까?
- 배경/맥락: 네트워크를 특별한 공간으로만 보면 소켓이 낯설게 남지만, I/O의 연장으로 보면 Unix I/O와의 공통 구조가 드러난다.
- 생각거리 1: 디스크 파일을 읽을 때, 키보드 입력을 받을 때, 네트워크 소켓을 읽고 쓸 때 프로세스와 커널의 역할은 각각 어떻게 닮아 있을까?
- 생각거리 2: “개발자는 `read`와 `write`만 알면 된다”는 말이 디스크에는 더 잘 맞고, 소켓에는 왜 추가 설명이 필요할까?
- 메모(힌트): 공통 인터페이스는 같지만, short count와 지연 같은 실행 특성은 I/O 대상마다 다를 수 있다.

---
- 질문: “네트워크에 연결되었다”는 것은 정확히 어떤 상태를 뜻할까?
- 배경/맥락: 와이파이에 접속했다는 사실만으로는 실제 통신 가능 상태가 완성되지 않으며, OS는 여러 네트워크 설정을 함께 받아야 한다.
- 생각거리 1: DHCP가 할당하는 IP address, subnet mask, default gateway, local DNS server 주소는 각각 무엇을 결정할까?
- 생각거리 2: routing table과 subnet mask가 함께 있을 때, OS는 어떻게 “내부망으로 바로 보낼지”와 “gateway로 넘길지”를 구분할까?
- 메모(힌트): 물리 연결, 주소 설정, 경로 정보, 이름 해석 경로가 모두 준비되어야 “통신 가능 상태”가 된다.

---
- 질문: protocol과 encapsulation을 함께 보면 내 프로그램의 데이터가 실제로 어떻게 이동하는지가 어떻게 달라져 보일까?
- 배경/맥락: 11.2는 세부 하드웨어 암기보다, 계층과 포장 구조를 통해 데이터 이동을 이해하도록 이끈다.
- 생각거리 1: IP가 없을 때 DHCP broadcast를 보낼 수 있다는 사실은 link layer와 network layer의 차이를 어떻게 보여 줄까?
- 생각거리 2: application data가 frame과 packet을 거치며 이동할 때, 각 계층은 “누구에게”와 “어떻게”라는 두 문제를 어떻게 나눠 맡을까?
- 메모(힌트): protocol은 naming과 delivery를 정하고, encapsulation은 각 계층이 자기 헤더를 덧붙이며 그 약속을 실어 나르는 과정이다.

<br>
<br>

## [ 11.3 The Global IP Internet ]

---
- 질문: 도메인 이름을 입력한 순간부터 IP 주소를 얻기까지 browser, resolver, local DNS server는 각각 무엇을 할까?
- 배경/맥락: 이름 해석은 브라우저 혼자도, DNS 서버 혼자도 처리하지 않고 cache, resolver, DNS 질의가 함께 엮여 이루어진다.
- 생각거리 1: browser cache나 OS cache에 도메인-IP 매핑이 있으면 어떤 통신이 생략되고, 없으면 누가 어떤 순서로 질의를 시작할까?
- 생각거리 2: local DNS server는 “최종 목적지 서버”와 어떻게 다르며, 왜 OS는 최종 웹 서버 IP는 몰라도 local DNS 주소는 알고 있을까?
- 메모(힌트): local DNS는 최종 서비스 대상이 아니라, 이름을 물어보기 위해 먼저 찾아가는 중간 목적지다.

---
- 질문: IP address, port, socket address, socket pair를 구분하면 “서버를 찾는다”는 말은 어떻게 달라질까?
- 배경/맥락: 호스트 식별, 서비스 식별, 연결 식별은 서로 다른 층위의 문제인데, 초반에는 모두 “주소”처럼 뭉쳐 보이기 쉽다.
- 생각거리 1: client도 port가 필요한데, 왜 server만 well-known port를 명시적으로 드러내고 client는 ephemeral port를 자동 할당받는 경우가 많을까?
- 생각거리 2: 같은 server port 80 위에 여러 client connection이 동시에 붙을 수 있다는 사실은 socket pair의 필요성을 어떻게 보여 줄까?
- 메모(힌트): host를 찾는 일, host 안의 service를 찾는 일, 실제 connection을 구분하는 일은 단계가 다르다.

---
- 질문: struct가 메모리에 연속적으로 놓여 있는데도 왜 serialization과 byte order 변환이 필요할까?
- 배경/맥락: 구조체를 그대로 보내면 될 것 같지만, wire format은 메모리 표현과 다를 수 있다는 점이 자주 놓친다.
- 생각거리 1: `sizeof(struct)`에 padding이 포함될 수 있다는 사실은 “메모리에 놓인 모습”과 “전송해야 할 바이트열”의 차이를 어떻게 드러낼까?
- 생각거리 2: endian과 network byte order를 이해하면, 같은 숫자가 서로 다른 머신에서 왜 다르게 해석될 수 있는지 어떻게 설명할 수 있을까?
- 메모(힌트): 네트워크는 타입이 아니라 byte sequence를 전달하므로, 메모리 표현을 곧바로 wire format으로 간주하면 위험하다.

---
- 질문: DNS는 왜 주로 UDP를 쓰고, 웹 통신은 왜 TCP의 reliable byte stream 위에 놓일까?
- 배경/맥락: 이름을 묻는 통신과 실제 서비스를 받는 통신은 목적도 다르고, 요구하는 전송 특성도 다르다.
- 생각거리 1: TCP 53번과 UDP 53번이 서로 다른 port space로 취급된다는 사실은 “같은 번호, 다른 프로토콜”을 어떻게 이해하게 해 줄까?
- 생각거리 2: TCP가 reliable byte stream을 제공한다는 말은 HTTP message boundary를 누가 책임지는지와 어떻게 연결될까?
- 메모(힌트): 짧은 질의-응답에 적합한 전송과, 지속적인 바이트 스트림에 적합한 전송을 분리해서 생각해 보자.

<br>
<br>

## [ 11.4 The Sockets Interface ]

---
- 질문: socket, endpoint, socket descriptor, file descriptor, kernel object는 각각 어떻게 연결될까?
- 배경/맥락: “소켓”이라는 말이 주소, 커널 객체, 정수 핸들, API 전체를 섞어 가리키기 때문에 11.4에서 가장 먼저 정리해야 하는 축이다.
- 생각거리 1: file descriptor table은 입출력 객체 자체를 담는 것일까, 아니면 커널 객체를 가리키는 참조를 담는 것일까?
- 생각거리 2: 소켓은 단순 FIFO 큐일까, 아니면 송수신 버퍼와 상태를 함께 가진 state machine에 가까울까?
- 메모(힌트): 프로그램이 직접 쥐는 것은 정수 FD이고, 실제 통신 상태는 커널 안의 소켓 객체가 가진다고 보면 구조가 깔끔해진다.

---
- 질문: 왜 소켓 인터페이스는 `socket -> connect`와 `socket -> bind -> listen -> accept`라는 두 경로로 갈라질까?
- 배경/맥락: client와 server는 같은 인터페이스를 쓰지만, 연결을 시작하는 쪽과 기다리는 쪽의 역할이 다르다.
- 생각거리 1: client는 왜 보통 `bind()`를 직접 호출하지 않고, `connect()` 과정에서 ephemeral port를 암묵적으로 할당받을까?
- 생각거리 2: `socket`, `connect`, `bind`, `listen`, `accept`는 결국 어떤 endpoint 상태를 준비하거나 완성하는 함수라고 볼 수 있을까?
- 메모(힌트): client는 active open, server는 passive open이라는 역할 차이로 읽으면 함수 분리가 자연스럽다.

---
- 질문: 왜 `accept()`는 listening FD를 그대로 쓰지 않고 새로운 connected FD를 돌려줄까?
- 배경/맥락: server는 계속 새 연결을 기다리는 입구와, 특정 client와 실제로 대화하는 채널을 동시에 유지해야 한다.
- 생각거리 1: 같은 server port로 들어온 패킷을 각 connected socket에 나눠 담는 과정은 4-tuple과 demultiplexing 관점에서 어떻게 설명할 수 있을까?
- 생각거리 2: backlog와 accept queue를 함께 보면, `listen()`과 `accept()`는 queue의 어떤 지점을 기준으로 역할을 나눠 갖는다고 볼 수 있을까?
- 메모(힌트): 입구를 남겨 둔 채 개별 대화 채널을 꺼내야 여러 client를 같은 port 위에서 처리할 수 있다.

---
- 질문: 왜 오늘날에도 소켓 인터페이스는 `sockaddr *` 같은 오래된 모양을 유지하면서, `getaddrinfo`, `sockaddr_storage`, `socklen_t`, `setsockopt` 같은 보완층을 함께 둘까?
- 배경/맥락: BSD sockets의 역사적 호환성과 현대적인 protocol-independent code 요구가 한 API 위에 겹쳐져 있다.
- 생각거리 1: `sockaddr`, `sockaddr_in`, `sockaddr_storage`, `socklen_t`를 나란히 놓으면 “범용 인터페이스”와 “실제 내용물”은 어떻게 구분될까?
- 생각거리 2: `AI_PASSIVE`, `AI_NUMERICSERV`, `SO_REUSEADDR`, `TIME_WAIT`를 함께 보면 운영체제의 호환성, 추상화, 실전 옵션은 어디서 만날까?
- 메모(힌트): 인터페이스 표면은 오래된 규약을 지키고, 실제 개발 편의는 helper와 wrapper, larger storage, option 계층이 메운다고 보면 된다.

<br>
<br>

## [ 11.5 Web Servers ]

---
- 질문: URL, URI, `Host` header를 client 관점과 server 관점으로 나누어 보면 무엇이 달라질까?
- 배경/맥락: 같은 문자열도 browser는 접속 대상을 보고, server는 해석할 자원을 보며, proxy가 끼면 중간 해석 지점까지 생긴다.
- 생각거리 1: URL prefix와 suffix를 나누어 보면 client가 사용하는 부분과 server가 사용하는 부분은 어떻게 갈라질까?
- 생각거리 2: `Host` header와 proxy 존재를 함께 보면, 하나의 IP 뒤에 여러 site가 공존하는 상황은 어떻게 설명할 수 있을까?
- 메모(힌트): client는 “어디로 갈지”, server는 “무엇을 줄지”를 중심으로 URL을 읽는다.

---
- 질문: TCP byte stream 위에서 HTTP message boundary는 어떻게 만들어질까?
- 배경/맥락: TCP가 reliable하다고 해서 메시지 경계까지 자동으로 주는 것은 아니므로, HTTP는 스스로 구조 규칙을 세워야 한다.
- 생각거리 1: request line, headers, blank line, body를 순서대로 놓으면 parser는 어디서 멈추고 어디서부터 body를 읽기 시작해야 할까?
- 생각거리 2: `Content-Length`, blank line, line-oriented reading은 각각 어떤 종류의 경계 정보를 제공할까?
- 메모(힌트): HTTP는 “텍스트 줄 구조 + 길이 정보”로 byte stream 위에 메시지 틀을 세운다.

---
- 질문: 정적 콘텐츠와 동적 콘텐츠는 server 입장에서 어떻게 다르며, CGI와 `dup2()`는 왜 필요한가?
- 배경/맥락: file serving과 program execution은 응답 생성 방식이 다르지만, 결국 둘 다 connected FD 위로 흘러가야 한다.
- 생각거리 1: static file과 CGI output을 비교하면, 둘은 각각 body를 어디서 얻고 어떤 운영체제 자원을 더 많이 동원할까?
- 생각거리 2: CGI child의 stdout을 connected socket으로 돌리는 구조는 “누가 응답을 더 잘 아는가”라는 질문에 어떤 답을 줄까?
- 메모(힌트): 정적 콘텐츠는 읽을 파일을, 동적 콘텐츠는 실행할 프로그램을 자원으로 삼는다고 보면 차이가 선명해진다.

<br>
<br>

## [ 11.6 Putting It Together: The Tiny Web Server ]

---
- 질문: Tiny는 `open_listenfd`에서 `close(connfd)`까지 한 요청을 어떤 순서로 처리할까?
- 배경/맥락: Tiny는 11장의 여러 함수와 개념을 실제 server code의 end-to-end 흐름으로 다시 묶어 보여 주는 예제다.
- 생각거리 1: `main`과 `doit`를 나누어 보면, “server의 골격”과 “한 번의 transaction 처리”는 각각 무엇을 맡을까?
- 생각거리 2: listening FD와 connected FD는 Tiny 안에서 각각 언제 생성되고 언제까지 살아남을까?
- 메모(힌트): `Accept -> doit -> Close`를 한 줄 spine으로 두고, 그 안에 request parsing과 resource dispatch를 끼워 넣어 보자.

---
- 질문: `serve_static`과 `serve_dynamic`은 둘 다 HTTP response를 만들지만, 무엇을 어떻게 body로 삼을까?
- 배경/맥락: Tiny는 file serving과 CGI execution을 나란히 놓아 “응답을 만든다”는 말의 두 가지 전형을 대비시킨다.
- 생각거리 1: `mmap`이 static path에서 하는 일은 무엇이며, 단순 `read`와 비교하면 어떤 점이 더 잘 보일까?
- 생각거리 2: `dup2`와 CGI child는 dynamic path에서 어떤 방식으로 “프로그램 출력 = response body”를 만들어 낼까?
- 메모(힌트): 하나는 파일 내용을, 다른 하나는 실행 결과를 body로 삼는다고 먼저 붙들면 구현 디테일이 따라온다.

---
- 질문: Tiny를 읽고 나면 왜 “동작하는 server”와 “견고한 server”를 구분하게 될까?
- 배경/맥락: Tiny는 본질을 드러내기 위해 최소 구현으로 쓰였으므로, 현실 server가 다뤄야 할 문제들을 일부러 많이 생략한다.
- 생각거리 1: `SIGPIPE`, `EPIPE`, bad request, early client close 같은 문제는 Tiny 코드 바깥에서 어떤 종류의 보강을 요구할까?
- 생각거리 2: iterative server 구조는 왜 동시성 장으로 넘어가는 직접적인 동기가 될까?
- 메모(힌트): Tiny는 “작동 원리 시연용”으로는 훌륭하지만, 운영 현실 전체를 담은 server는 아니라는 점을 계속 의식하자.

<br>
<br>

## [ 11.7 Summary ]

---
- 질문: 도메인을 입력한 순간부터 TCP 연결이 성립하고 첫 response를 읽기까지의 전체 흐름을 한 번에 설명할 수 있을까?
- 배경/맥락: DNS, resolver, socket, `connect()`, `accept()`, Rio, HTTP는 흩어진 키워드가 아니라 하나의 긴 서사를 이룬다.
- 생각거리 1: 이름 해석용 통신과 실제 서비스용 통신은 어느 지점에서 갈라지고, 다시 어디서 이어질까?
- 생각거리 2: 이 전체 서사에서 client와 server는 각각 어느 단계에서 서로를 “알게” 되고, 어느 단계부터 데이터를 주고받기 시작할까?
- 메모(힌트): browser의 이름 입력부터 server의 resource access와 response writing까지를 한 줄 이야기로 이어 보자.

---
- 질문: 소켓을 file descriptor라는 공통 추상으로 보면 10장의 Unix I/O와 11장은 어떻게 연결될까?
- 배경/맥락: 11장은 네트워크만의 별도 세계라기보다, Unix I/O 모델이 네트워크 대상까지 확장되는 장이기도 하다.
- 생각거리 1: `Close`와 `Freeaddrinfo`를 비교하면 커널 자원 반납과 사용자 메모리 반납은 어떻게 구분될까?
- 생각거리 2: 연결이 열린 뒤에도 Rio와 short count 이해가 필요한 이유는 “소켓도 I/O 대상”이라는 사실과 어떻게 맞물릴까?
- 메모(힌트): 공통 추상은 FD이지만, 실행 특성은 네트워크 특유의 부분 처리와 종료 신호를 함께 고려해야 한다.

---
- 질문: TCP를 reliable byte stream으로 이해하면 메시지 경계 문제와 12장 동시성 문제는 어떻게 이어질까?
- 배경/맥락: 11장은 한 연결의 문법을 가르치고, 12장은 같은 문법을 여러 연결에 동시에 펼치는 방법을 다룬다.
- 생각거리 1: blank line, `Content-Length`, EOF 같은 규칙은 왜 byte stream 위에서 애플리케이션이 직접 세워야 할 규칙일까?
- 생각거리 2: `listenfd`와 여러 `connfd`를 동시에 다루는 문제는 왜 바로 concurrency question으로 이어질까?
- 메모(힌트): “한 연결 안의 메시지 구성”과 “여러 연결을 동시에 관리”는 서로 다른 문제지만 같은 byte-stream 모델 위에 놓여 있다.
