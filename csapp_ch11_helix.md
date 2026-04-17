# CS:APP 11장 Network Programming Helix

## 0. 이 학습서의 사용법

이 문서는 11장을 **한 번에 납작하게 요약한 문서**가 아니라, 같은 대상을 여러 번 다른 해상도로 다시 도는 **나선형 학습서**다. 처음에는 “한 번의 연결이 어떻게 태어나고 끝나는가”만 붙들고, 그다음에는 네트워크의 바닥 구조를 보고, 다시 주소·이름·포트·소켓을 보고, 다시 HTTP와 Tiny로 되돌아온다. 이렇게 같은 대상을 반복해서 보면 키워드가 서로 엮이기 시작한다.

권장 순서는 다음과 같다.

1. **Helix 1**과 **Helix 2**로 큰 그림을 잡는다.
2. **Helix 3**과 **Helix 4**에서 주소, 포트, 소켓 생애주기를 굳힌다.
3. **Helix 5**에서 Echo 예제로 흐름을 손에 익힌다.
4. **Helix 6**과 **Helix 7**에서 HTTP와 Tiny로 11장을 통합한다.
5. 마지막 **Helix 8**에서 12장 동시성으로 이어지는 질문을 만든다.

---

## 1. 11장 전체 지도

CS:APP 11장은 다음 순서로 전개된다.

- **클라이언트-서버 모델** (11.1)
- **네트워크를 프로그래머의 I/O 환경으로 보는 시각** (11.2)
- **Global IP Internet: 주소, 이름, 연결** (11.3)
- **Sockets interface** (11.4)
- **Web servers, HTTP, CGI** (11.5)
- **Tiny Web Server** (11.6)
- **전체 요약** (11.7)

이 장의 핵심은 네트워크 이론 전체를 배우는 것이 아니다. 핵심은 **C 프로그램이 인터넷 위에서 다른 프로그램과 통신하도록 만드는 법**을 배우는 것이다. 그래서 11장은 프로토콜 내부의 모든 세부보다, **프로그래머가 직접 손대는 인터페이스와 흐름**에 집중한다.

11장의 중심 질문을 한 문장으로 적으면 이렇다.

> **한 프로세스가 다른 호스트의 프로세스와 연결을 맺고, 바이트 스트림을 주고받아, 요청-응답 서비스를 구현하려면 무엇을 알아야 하는가?**

이 질문에 답하는 가장 좋은 방법은 “한 번의 웹 요청”을 처음부터 끝까지 보는 것이다.

```text
브라우저가 URL을 안다
-> 이름을 IP 주소로 푼다
-> 서버의 host:port를 알아낸다
-> client socket을 만든다
-> connect로 서버에 접근한다
-> server는 listenfd로 기다리다 accept한다
-> 양쪽은 connected descriptor로 읽고 쓴다
-> HTTP request/response를 주고받는다
-> 서버는 정적 파일을 보내거나 CGI를 실행한다
-> 연결을 닫는다
```

이 한 줄의 서사를 11장 전체가 조금씩 다른 높이에서 다시 설명한다.

---

## 2. 11장을 읽기 전에 꼭 붙들어야 할 선수개념

### 2.1 파일 디스크립터와 Unix I/O (10.1, 10.2)

11장을 처음 읽을 때 가장 중요한 전제는 **소켓도 파일 디스크립터로 다룬다**는 사실이다. 디스크 파일, 터미널, 파이프, 네트워크 소켓은 운영체제 입장에서 모두 “열린 I/O 대상”이다. 네트워크 연결이 성립한 뒤 프로그램이 하는 일은 결국 `read`, `write`, `close` 또는 그 래퍼를 사용하는 것이다.

즉, 11장은 “전혀 새로운 I/O 세계”가 아니라, **10장의 Unix I/O가 네트워크라는 대상에 적용되는 장**이다.

### 2.2 short count와 Rio (10.4, 10.5)

네트워크 소켓에서는 `read`와 `write`가 요청한 바이트 수를 한 번에 다 처리하지 못할 수 있다. 이것은 버그가 아니라 정상 동작이다. 이 문제를 안전하게 다루기 위해 CS:APP는 **Rio**를 사용한다.

- `rio_readn`, `rio_writen`: 정확히 n바이트를 안정적으로 처리하려는 함수
- `rio_readinitb`, `rio_readlineb`: 줄 단위 텍스트 프로토콜을 안정적으로 읽기 위한 버퍼드 인터페이스

HTTP와 Echo 예제에서 Rio가 반복적으로 등장하는 이유는, 11장의 많은 예제가 **텍스트 줄 단위 프로토콜**을 다루기 때문이다.

### 2.3 표준 I/O가 네트워크에 잘 맞지 않는 이유 (10.10, 10.11)

초보자는 종종 “그냥 `fgets`, `fprintf` 쓰면 안 되나?”라고 생각한다. 가능은 하지만, 표준 I/O는 네트워크 소켓에서 불편한 점이 있다.

- 입력/출력 버퍼링이 네트워크 프로토콜 흐름을 흐릴 수 있다.
- 동일한 소켓에 여러 스트림을 얹을 때 종료 동작이 혼란스러울 수 있다.
- 소켓은 `lseek`가 가능한 파일이 아니다.

그래서 11장은 네트워크 프로그래밍의 실전 기본기로 **Rio + sockets** 조합을 강조한다.

### 2.4 프로세스와 리다이렉션 (8.4, 9.8, 10.9)

CGI와 Tiny를 읽으려면 다음 조합을 이해해야 한다.

- `fork`: 자식 프로세스 생성
- `execve`: 다른 프로그램 실행
- `dup2`: 표준 입력/출력을 다른 디스크립터에 연결
- `wait`: 자식 종료 수거
- `mmap`: 파일을 메모리에 매핑

11장은 8장, 9장, 10장의 내용이 **하나의 실제 서버 코드 안에서 만나는 장**이기도 하다.

---

## 3. 가장 먼저 익혀야 할 핵심 키워드 15개

1. **host**: 네트워크에 연결된 컴퓨터
2. **process**: 실행 중인 프로그램. 실제로 통신하는 주체는 host가 아니라 process다.
3. **client**: 요청을 시작하는 쪽
4. **server**: 자원을 관리하며 요청에 응답하는 쪽
5. **transaction**: 요청과 응답이 오가는 한 번의 상호작용
6. **IP address**: host 또는 그 host의 네트워크 어댑터를 가리키는 주소
7. **port**: host 안에서 특정 서비스 종단점을 식별하는 번호
8. **socket address**: `IP address + port`
9. **socket**: 연결의 종단점이 프로그램에게 파일 디스크립터로 제시된 것
10. **listening descriptor**: 서버가 연결 요청을 기다리는 디스크립터
11. **connected descriptor**: 실제 데이터 송수신에 쓰는 디스크립터
12. **connection**: 두 프로세스 사이의 reliable byte stream
13. **DNS**: 이름과 주소를 연결하는 분산 데이터베이스
14. **Rio**: short count를 안전하게 다루는 robust I/O 인터페이스
15. **CGI**: 클라이언트-서버-자식 프로세스 사이 정보 전달 규칙

이 15개가 헷갈리면 11장은 계속 흐린다. 이 용어들을 외우려 하지 말고, **서로 어떻게 연결되는지** 붙들어야 한다.

```text
도메인 이름
-> IP 주소
-> host
-> port
-> socket address
-> socket
-> connection
-> connected descriptor
-> Rio
-> HTTP 메시지
```

---

# Helix 1. 한 번의 연결을 이야기로 보기 (11.1, 11.3.3, 11.4, 11.5.3, 11.6)

## 1. 이 바퀴의 질문

- 네트워크 프로그램은 결국 무엇을 하는 프로그램인가?
- 11장 전체를 꿰는 한 줄의 서사는 무엇인가?
- 왜 이 장은 함수 암기보다 흐름 이해가 중요할까?

## 2. 핵심 서사: “서비스를 받으러 가는 프로그램”과 “기다리는 프로그램”

모든 네트워크 애플리케이션은 **클라이언트-서버 모델** 위에 있다 (11.1). 여기서 서버는 단지 답장을 보내는 프로그램이 아니다. 서버는 **자원을 관리하는 프로그램**이다. 웹 서버는 파일을 관리하고, FTP 서버는 파일을 저장하고 꺼내 주며, 메일 서버는 스풀 파일을 읽고 갱신한다.

클라이언트-서버 트랜잭션은 다음 네 단계로 요약할 수 있다.

1. 클라이언트가 요청을 시작한다.
2. 서버가 요청을 해석하고 자원을 조작한다.
3. 서버가 응답을 만든다.
4. 클라이언트가 응답을 받아 사용한다.

이제 이 추상 모델을 네트워크 프로그래밍의 구체적 서사로 내려오면 다음이 된다.

```text
클라이언트
1) 서버 이름과 포트를 안다
2) socket을 만든다
3) connect로 연결을 시도한다
4) request를 write 한다
5) response를 read 한다
6) close 한다

서버
1) socket을 만든다
2) bind로 well-known port를 자기 것으로 묶는다
3) listen으로 기다리는 소켓이라고 선언한다
4) accept로 연결을 받아들인다
5) request를 read 한다
6) 자원을 처리한다
7) response를 write 한다
8) close 한다
```

이 흐름을 계속 기억하면, `socket`, `connect`, `bind`, `listen`, `accept`, `rio_readlineb`, `rio_writen`은 산발적인 함수가 아니라 **하나의 생애주기**를 구성하는 단계로 보인다.

## 3. 요청-응답 축과 연결 생애주기 축

11장을 이해할 때는 두 축을 동시에 잡아야 한다.

### 3.1 요청-응답 축

이 축은 **서비스 모델**이다.

```text
요청 -> 서버 해석 -> 자원 조작 -> 응답
```

이 축을 따라가면 웹 서버, DB 서버, 캐시 서버, 메신저 서버가 모두 같은 구조를 가진다는 사실이 보인다.

### 3.2 연결 생애주기 축

이 축은 **통신 메커니즘**이다.

```text
주소 결정 -> socket -> connect/accept -> read/write -> close
```

이 축을 따라가면 소켓 함수들이 왜 그 순서로 등장하는지 보인다.

11장 전체는 이 두 축이 만나는 장이다. 하나는 “무슨 서비스를 하느냐”이고, 다른 하나는 “그 서비스를 어떻게 연결 위에서 실현하느냐”다.

## 4. 11장을 읽는 가장 좋은 마음가짐

네트워크 프로그래밍 API는 처음 보면 크고 복잡하고 지저분해 보인다. 실제로 그렇다. 한 번 읽고 끝나는 장이 아니다. 같은 코드와 같은 함수 설명을 여러 번 왕복해야 한다. 그래서 예제 코드가 특히 중요하다. 11장의 Echo와 Tiny는 단순한 예제가 아니라, **복잡한 API를 몸에 붙이기 위한 압축 모델**이다.

함수의 정의를 먼저 외우려 하면 길을 잃는다. 대신 다음 질문을 반복해야 한다.

- 지금 나는 **누구의 관점**에서 보고 있는가? 클라이언트인가, 서버인가?
- 지금 이 함수는 **연결 생애주기의 어느 단계**를 담당하는가?
- 지금 이 I/O는 **파일 I/O처럼 보이지만 실제로는 어떤 프로토콜 메시지**를 다루는가?

## 5. 생각거리

### 생각거리 1 | 자가점검

**Q1. 서버를 “응답하는 쪽”보다 “자원을 관리하는 쪽”으로 이해해야 하는 이유는 무엇인가?**  
**해설.** 응답은 결과의 모양이고, 자원 관리는 서버의 책임이다. 웹 서버는 파일을, 데이터베이스 서버는 테이블과 인덱스를, 메일 서버는 메시지 저장소를 관리한다. 그래서 “요청을 처리한다”는 말은 결국 “자원에 어떤 연산을 적용해 어떤 결과를 돌려주는가”를 뜻한다. 이 관점을 잡으면 HTTP, CGI, Tiny, 그리고 다음 장의 동시성 서버까지 하나의 축 위에 놓인다.

**Q2. 클라이언트와 서버의 관점에서 한 번의 연결이 어떻게 진행되는지, 순서를 끊기지 않게 설명해 보라.**  
**해설.** 클라이언트는 서버의 이름과 포트를 알고, socket을 만든 뒤 connect로 다가가고, 요청을 쓰고, 응답을 읽고, 연결을 닫는다. 서버는 socket을 만들고, bind로 자기 주소를 묶고, listen으로 기다리는 소켓으로 바꾸고, accept로 새 연결을 받아들이고, 요청을 읽고, 자원을 처리해 응답을 쓰고, 연결을 닫는다. 이 순서를 말로 자연스럽게 설명할 수 있어야 이후의 함수들이 “흩어진 이름”이 아니라 “한 서사의 단계”로 보인다.

**Q3. 요청-응답 축과 연결 생애주기 축은 어떻게 다른가?**  
**해설.** 요청-응답 축은 서비스의 논리를 본다. 즉, “클라이언트가 무엇을 요구했고 서버가 무엇을 돌려주는가”를 묻는다. 반면 연결 생애주기 축은 그 서비스가 실현되는 통신 절차를 본다. 즉, “주소를 찾고, 연결을 만들고, 읽고, 쓰고, 닫는 과정”을 묻는다. 11장은 이 두 축이 만나는 장이기 때문에, 하나만 보면 절반만 이해한 셈이 된다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 11장은 소켓 함수보다 먼저 클라이언트-서버 모델을 보여 주는가?**  
**해설.** API를 먼저 외우면 절차는 남지만 목적이 사라진다. 반대로 모델을 먼저 이해하면 `connect`는 “요청을 시작하는 준비”, `accept`는 “요청을 받아들이는 준비”, `rio_readlineb`는 “메시지를 텍스트 단위로 읽는 수단”으로 자리를 잡는다. 강의도 “A client-server transaction”에서 출발한 뒤 sockets interface로 내려오는데, 그 흐름은 함수의 의미를 서비스 구조 속에 고정하려는 의도라고 볼 수 있다.

**Q2. 왜 `rio_readlineb`, `rio_writen` 같은 I/O 함수가 단순한 부속물이 아니라 11장의 중심에 가까운가?**  
**해설.** 소켓 API가 연결을 열고 닫는 뼈대라면, Rio는 그 연결 위에서 실제 메시지를 안전하게 다루는 손이다. Echo와 HTTP 예제에서 중요한 것은 “연결이 있다”는 사실만이 아니라, 그 연결 위에서 짧게 읽히거나 끊기기 쉬운 데이터를 어떻게 안정적으로 읽고 쓰느냐이다. 특히 강의의 세션 도식에서 `rio_readlineb`와 `rio_writen`이 `connect`와 `accept`만큼 비중 있게 놓이는 이유는, 네트워크 프로그래밍이 결국 “연결 위의 I/O”이기 때문이다. 

**Q3. 왜 `close`와 EOF를 이야기의 마지막이 아니라 구조의 일부로 이해해야 하는가?**  
**해설.** 많은 초심자는 연결의 시작에만 집중하고 종료를 부차적인 정리로 본다. 하지만 서버는 상대가 연결을 닫았다는 사실을 읽기 0 반환값으로 해석하고 제어 흐름을 바꾼다. 즉, 종료는 단순한 뒷정리가 아니라, 상대에게 “이 대화는 끝났다”는 신호를 주는 프로토콜적 사건이다. Echo와 Tiny를 제대로 읽으려면 시작뿐 아니라 끝도 서사 속에 포함해야 한다.

### 생각거리 3 | 확장과 전이

**Q1. 클라이언트와 서버가 같은 호스트에 있거나, 역할이 순간마다 바뀌는 P2P 시스템에서도 이 장의 관점이 유효한가?**  
**해설.** 유효하다. 클라이언트-서버 모델의 핵심은 물리적 위치가 아니라 역할 분리다. 같은 호스트 안에서도 한 프로세스가 자원을 관리하고 다른 프로세스가 요청하면 여전히 클라이언트-서버다. P2P도 완전히 다른 세계라기보다, 한 프로세스가 어떤 순간에는 클라이언트처럼, 다른 순간에는 서버처럼 행동하는 구조로 보는 편이 정확하다.

**Q2. 이 Helix의 큰 그림은 웹 서버 말고 어떤 시스템에도 그대로 적용될까?**  
**해설.** 적용된다. 모바일 앱의 백엔드 API, 데이터베이스 서버, 캐시 서버, 메시지 큐, 파일 저장 서비스도 모두 “클라이언트가 요청하고 서버가 자원을 관리해 응답한다”는 구조를 갖는다. 차이는 자원의 종류와 프로토콜의 형태일 뿐, 역할 분리와 연결 생애주기라는 뼈대는 그대로다. 그래서 11장을 잘 이해하면 웹을 넘어 시스템 전반을 보는 눈이 생긴다.

**Q3. 왜 이 한 번의 연결 서사가 이미 12장의 동시성 문제를 예고하는가?**  
**해설.** 지금까지의 서사는 “한 연결을 어떻게 처리하는가”를 설명했다. 하지만 실제 서버는 한 연결만 오지 않는다. 누군가 오래 머물면 다른 누군가는 기다려야 하느냐, `accept` 이후 처리를 어떻게 분기하느냐, 읽기와 쓰기에서 누가 block되느냐 같은 질문이 곧 동시성 문제다. 즉, 11장은 단일 연결의 문법을 가르치고, 12장은 그 문법을 여러 연결 위에 동시에 펼치는 법을 가르친다.

---

# Helix 2. 네트워크를 I/O 장치로 이해하기 (11.2)

## 1. 이 바퀴의 질문

- 프로그래머 관점에서 네트워크는 정확히 무엇인가?
- LAN, bridge, router, packet, encapsulation은 왜 필요한가?
- IP, UDP, TCP는 각각 어떤 문제를 푸는가?

## 2. 네트워크는 호스트에게 또 하나의 I/O 장치다

11.2의 출발점은 단순하다. **호스트에게 네트워크는 또 하나의 I/O 장치다.**

- 디스크가 바이트를 읽고 쓰는 대상이라면
- 네트워크도 바이트를 보내고 받는 대상이다.

호스트 내부에서 보면, 네트워크 어댑터가 I/O 버스에 붙어 있고, 네트워크로부터 들어온 데이터는 어댑터를 거쳐 메모리로 복사된다. 나갈 때는 메모리의 데이터가 반대로 어댑터를 통해 네트워크로 나간다. 이 시각이 중요한 이유는, 네트워크를 신비한 세계가 아니라 **운영체제와 하드웨어가 다루는 현실적인 I/O 대상**으로 보게 해 주기 때문이다.

이 관점을 잡으면 왜 소켓이 파일 디스크립터로 보이는지 자연스럽다. 연결이 성립한 뒤 프로그램은 결국 바이트를 읽고 쓰는 일을 하기 때문이다.

## 3. LAN에서 internet까지

### 3.1 Ethernet segment

가장 아래에는 LAN이 있다. 그 대표가 Ethernet이다. Ethernet segment는 여러 host가 hub와 wire로 연결된 구조라고 생각할 수 있다.

- 각 adapter는 MAC 주소를 가진다.
- host는 frame이라는 덩어리로 비트를 보낸다.
- hub는 한 포트에서 받은 비트를 다른 모든 포트로 복사한다.
- 모든 host가 모든 bit를 보지만, 목적지 host만 그 frame을 실제로 읽는다.

초심자에게 중요한 포인트는 이것이다. **같은 LAN 안에서는 비교적 단순한 복사와 전달이 일어난다.**

### 3.2 Bridged Ethernet

hub만으로 네트워크를 키우면 비효율적이다. 그래서 bridge가 등장한다.

bridge는 점점 학습한다.

- 어느 host가 어느 포트 쪽에 있는지 배우고
- 필요할 때만 frame을 특정 방향으로 전달한다.

즉, hub가 “무조건 복사”라면, bridge는 “관찰하고 선택적으로 전달”이다.

### 3.3 Router와 internet

서로 다른 LAN, 심지어 서로 다른 네트워크 기술을 이어 붙이려면 router가 필요하다. router는 한 네트워크에서 다른 네트워크로 packet을 넘겨주는 specialized computer다.

여기서 중요한 구분이 있다.

- **internet**: 여러 네트워크를 연결한 일반 개념
- **Internet**: 그중 가장 유명한 구현, 즉 Global IP Internet

네트워크 프로그래밍은 본질적으로 “하나의 동일한 네트워크”만 상대하지 않는다. 오히려 **서로 다른 네트워크들이 프로토콜로 봉합된 세계**를 상대한다.

## 4. 왜 protocol이 필요한가

서로 다른 네트워크 기술이 연결되려면 두 가지가 필요하다.

### 4.1 Naming scheme

누구에게 보내는지 식별해야 한다. 네트워크마다 자체 주소 체계가 다르면 서로를 찾을 수 없다. 그래서 인터넷 프로토콜은 host를 가리키는 **통일된 주소 체계**를 제공해야 한다.

### 4.2 Delivery mechanism

데이터를 어떤 단위로 포장하고 어떻게 옮길지도 통일해야 한다. 그래서 등장하는 것이 **packet**이다. packet은 보통

- header: source, destination, size 등 제어 정보
- payload: 실제 데이터

로 이루어진다.

## 5. 패킷, 프레임, 캡슐화

11.2의 핵심 장면은 **encapsulation** 이다.

```text
사용자 데이터
-> 인터넷 packet의 payload
-> LAN frame의 payload
-> router에서 LAN frame header 교체
-> 목적지 host에서 header 제거
-> 다시 사용자 데이터
```

하나의 계층에서 payload였던 것이, 더 아래 계층에서는 더 큰 단위의 일부가 된다. 이 사고는 네트워크뿐 아니라 시스템 전반에서 반복된다.

## 6. IP, UDP, TCP를 한 화면에 놓기

11.3은 주로 TCP 연결을 중심으로 설명하지만, 네트워크 프로그래밍 큰 그림을 위해서는 IP/UDP/TCP를 함께 보는 것이 좋다.

- **IP**: host-to-host packet delivery의 기초. 신뢰적이지 않다.
- **UDP**: process-to-process datagram delivery. connectionless, unreliable.
- **TCP**: process-to-process reliable byte stream over connections.

11장은 결국 TCP 중심의 장이다. 왜냐하면 sockets, Echo, HTTP, Tiny가 모두 **SOCK_STREAM**, 즉 TCP connection을 전제로 하기 때문이다.

## 7. 프로그래머에게 중요한 결론

네트워크는 크게 보면 복잡하지만, 프로그래머는 다음 사실만 먼저 잡으면 된다.

1. 네트워크는 I/O 장치다.
2. 인터넷은 서로 다른 네트워크들의 네트워크다.
3. packet과 encapsulation이 차이를 가려 준다.
4. 프로그래머는 주로 TCP connection과 sockets interface를 통해 이 세계를 쓴다.

## 8. 생각거리

### 생각거리 1 | 자가점검

**Q1. 프로그래머 관점에서 네트워크를 “또 하나의 I/O 장치”라고 부르는 이유는 무엇인가?**  
**해설.** 프로그램은 네트워크를 디스크나 터미널처럼 바이트를 읽고 쓰는 대상으로 다룬다. 운영체제 내부에서는 네트워크 어댑터와 버스를 통해 데이터가 메모리로 들어오고 다시 나가므로, 하드웨어와 커널 관점에서도 분명한 I/O 장치다. 이 시각을 잡아야 소켓이 왜 파일 디스크립터처럼 보이는지가 자연스럽게 이해된다.

**Q2. 허브, 브리지, 라우터의 차이를 간단히 설명해 보라.**  
**해설.** 허브는 받은 비트를 거의 무차별적으로 다른 포트로 복사한다. 브리지는 어떤 호스트가 어느 쪽에 있는지 학습해 필요한 방향으로만 프레임을 전달한다. 라우터는 그보다 더 높은 수준에서 서로 다른 네트워크를 이어 주며, 패킷을 어느 다음 네트워크로 넘길지 결정한다. 강의가 이 차이를 강조하는 이유는, “모두 비슷한 상자”처럼 보이는 장비들이 실제로는 서로 다른 계층의 문제를 푼다는 점을 보여 주기 위해서다.

**Q3. frame, packet, encapsulation은 각각 무엇을 뜻하는가?**  
**해설.** frame은 LAN 같은 링크 수준에서 오가는 전송 단위이고, packet은 인터넷 수준에서 주소와 전달 정보를 담는 전송 단위다. encapsulation은 상위 계층의 데이터가 하위 계층에서는 payload가 되고, 그 앞뒤에 새 헤더가 붙어 더 큰 단위로 포장되는 현상이다. 이 개념을 이해하면 “내 프로그램의 바이트”가 실제 네트워크 안에서 어떤 모양으로 움직이는지 그릴 수 있다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 internet에는 naming scheme과 delivery mechanism이 둘 다 필요할까?**  
**해설.** 이름 체계만 있으면 누구에게 보내야 하는지는 알 수 있지만, 어떤 단위로 어떻게 전달할지는 정해지지 않는다. 반대로 전달 메커니즘만 있으면 바이트 덩어리를 옮길 수는 있어도, 최종 수신자를 일관되게 식별할 수 없다. 그래서 강의와 교재는 internet을 “서로 다른 네트워크를 이어 주는 프로토콜의 결합”으로 설명하면서 이름과 전달을 함께 묶어 본다.

**Q2. 왜 라우터는 프레임 헤더를 벗기고 새로 붙이지만, 애플리케이션은 여전히 ‘같은 데이터가 갔다’고 느끼는가?**  
**해설.** 링크 계층의 포장은 구간마다 바뀔 수 있지만, 그 안쪽의 더 높은 수준 데이터는 목적지를 향해 계속 이어지기 때문이다. 라우터는 “다음 hop까지 어떻게 보낼까”를 다루고, 애플리케이션은 “종단 간에 무엇을 보냈나”를 본다. 즉, 다른 계층의 규칙이 동시에 작동하므로 중간에서는 포장이 바뀌어도 끝에서 보는 의미는 유지된다.

**Q3. 왜 11.2는 네트워크 하드웨어의 세부보다 ‘계층’과 ‘캡슐화’를 강조하는가?**  
**해설.** 이 장의 목적은 네트워크 장비를 설계하는 것이 아니라, 그 위에서 프로그램을 짜는 사람이 어느 수준까지 이해해야 하는지를 정하는 데 있다. 프로그래머는 모든 전기적 세부를 알 필요는 없지만, 패킷이 계층을 오르내리며 포장과 해제가 일어난다는 사실은 알아야 IP, TCP, 소켓, HTTP가 하나의 그림으로 연결된다. 계층을 놓치면 위와 아래가 분리되고, 전체 장이 파편화된다.

### 생각거리 3 | 확장과 전이

**Q1. CS:APP가 UDP보다 TCP 중심으로 11장을 전개하는 이유는 무엇일까?**  
**해설.** 책의 목표가 “단순한 웹 서버를 직접 만들어 보는 것”이기 때문이다. HTTP와 Echo 같은 예제를 처음 접할 때는 reliable byte stream이라는 추상이 학습 비용을 크게 낮춘다. 반대로 UDP는 메시지 손실, 재전송, 순서 보장 부재 같은 문제를 프로그래머가 더 직접 떠안게 만든다. 즉, 11장은 네트워크 프로그래밍의 입문 문턱을 넘기 위해 TCP를 선택한 셈이다.

**Q2. 클라우드나 컨테이너 환경처럼 물리 네트워크가 잘 보이지 않는 시대에도 11.2의 관점이 여전히 유효한가?**  
**해설.** 유효하다. 가상 NIC, 가상 스위치, overlay network 같은 기술이 중간을 많이 감추더라도, 결국 데이터는 종단점, 패킷, 경로, 헤더라는 개념을 통해 이동한다. 추상화가 높아졌다고 해서 네트워크의 계층성과 I/O 성격이 사라지는 것은 아니다. 오히려 보이지 않기 때문에 더 기본 모델이 필요하다.

**Q3. 애플리케이션이 ‘느리다’고 느낄 때 왜 이 장의 packet/capsulation 관점이 도움이 될까?**  
**해설.** 느림은 단순히 코드가 느린 것만이 아니라, 전송 경로의 지연, 재전송, 중간 장비의 혼잡, 응답 본문의 크기, 연결 설정 비용 때문에도 생긴다. 네트워크를 단순한 `read`/`write` 대상으로만 보면 이런 병목을 놓치기 쉽다. 반대로 프레임, 패킷, 라우터, 경로, 헤더를 떠올리면 “어디서 지연이 생길 수 있는가”를 더 구조적으로 질문할 수 있다.

---

# Helix 3. 주소, 이름, 포트, 연결의 정체 (11.3.1~11.3.3)

## 1. 이 바퀴의 질문

- “서버를 찾는다”는 말은 정확히 무슨 뜻인가?
- IP 주소, 도메인 이름, 포트, socket address, socket pair는 어떻게 이어지는가?
- 왜 network byte order가 필요한가?

## 2. IP address: host를 식별하는 32비트 값 (11.3.1)

CS:APP 11장은 주로 IPv4를 중심으로 설명한다. IPv4 주소는 **32비트 부호 없는 정수**다. 네트워크 프로그램에서는 이를 단순 `uint32_t` 값이 아니라 보통 다음 구조체로 다룬다.

```c
struct in_addr {
    uint32_t s_addr;   /* network byte order */
};
```

여기서 중요한 사실은 두 가지다.

1. IP 주소는 32비트 값이다.
2. 이 값은 메모리 안에서도 **network byte order**, 즉 big-endian으로 저장된다.

## 3. 왜 network byte order가 필요한가

host마다 바이트 순서가 다를 수 있다. 어떤 기계는 little-endian, 어떤 기계는 big-endian이다. 만약 packet header의 정수 값을 모두 자기 방식대로 쓰면 상대는 같은 byte sequence를 전혀 다른 숫자로 해석할 수 있다.

그래서 네트워크 위를 오가는 정수 값은 관례적으로 **big-endian**으로 통일한다.

- `htonl`, `htons`: host -> network
- `ntohl`, `ntohs`: network -> host

이 원리는 IP 주소뿐 아니라 포트 번호 같은 header 정수에도 적용된다.

## 4. dotted decimal notation과 주소 변환

사람은 32비트 정수를 그대로 기억하지 않는다. 그래서 IP 주소를 다음처럼 쓴다.

```text
128.2.194.242
```

이는 사실 4바이트를 10진수로 적은 표기다. 프로그램은 다음 계열 함수로 표현을 오간다.

- `inet_pton`: printable string -> network address
- `inet_ntop`: network address -> printable string

실전에서는 이후에 더 중요한 함수로 `getaddrinfo`와 `getnameinfo`가 등장하지만, `inet_pton`/`inet_ntop`은 “주소를 사람이 보는 형식과 기계가 쓰는 형식 사이에서 변환한다”는 감각을 만들어 준다.

## 5. Domain name과 DNS (11.3.2)

클라이언트는 보통 `128.2.194.242` 대신 `www.example.com` 같은 이름을 사용한다. 이 이름이 **domain name**이다. 도메인 이름은 계층 구조를 갖는다.

```text
unnamed root
-> .com, .edu, .gov, .net ...
-> cmu.edu
-> cs.cmu.edu
-> www.cs.cmu.edu
```

이 이름과 IP 주소의 대응을 관리하는 거대한 분산 데이터베이스가 **DNS**다.

입문 단계에서 DNS를 너무 복잡하게 보지 않아도 된다. 먼저 다음만 기억하면 충분하다.

- 이름은 사람이 읽기 좋다.
- 주소는 네트워크가 다루기 좋다.
- DNS는 둘 사이를 연결한다.

하지만 한 걸음 더 들어가면 중요한 성질이 있다.

- 하나의 host entry는 여러 domain name과 여러 IP address를 묶을 수 있다.
- 하나의 이름이 여러 IP로 풀릴 수 있다.
- 여러 이름이 같은 IP를 가리킬 수도 있다.
- `localhost`는 늘 loopback 주소 `127.0.0.1`에 대응한다.

이 성질 때문에 `getaddrinfo`가 단일 주소가 아니라 **후보 목록**을 반환하는 것이다.

## 6. Port: host 안에서 서비스를 찾는 번호

IP 주소만으로는 충분하지 않다. IP 주소는 host를 찾지만, host 안에는 여러 서버 process가 동시에 있을 수 있다. 그래서 **port**가 필요하다.

- host는 “어느 컴퓨터인가”를 식별한다.
- port는 “그 컴퓨터 안의 어느 서비스인가”를 식별한다.

예를 들어 웹 서버는 흔히 80, 메일 서버는 25 같은 **well-known port**를 쓴다. 반면 클라이언트는 대개 커널이 임시로 골라 준 **ephemeral port**를 사용한다.

## 7. Socket address와 socket pair (11.3.3)

`address + port`를 묶은 것이 **socket address**다.

```text
client socket address = 128.2.194.242:51213
server socket address = 208.216.181.15:80
```

그리고 한 connection은 이 둘을 묶은 **socket pair**로 유일하게 식별된다.

```text
(128.2.194.242:51213, 208.216.181.15:80)
```

이 점이 중요하다. 서버는 같은 포트 80을 유지하면서도 수많은 클라이언트와 동시에 다른 connection을 가질 수 있다. 서버의 정체성을 포트만으로 보지 말고, **실제 connection의 정체성은 양 끝점의 쌍**이라는 것을 기억해야 한다.

## 8. Connection의 세 가지 성질

CS:APP의 인터넷 connection은 다음 세 성질로 요약된다.

1. **point-to-point**: 특정 두 process를 잇는다.
2. **full-duplex**: 양방향 데이터 흐름이 가능하다.
3. **reliable**: 심각한 재난이 없다면 순서를 유지한 byte stream이 도착한다.

여기서 아주 중요한 표현이 있다.

> **TCP connection은 메시지 객체가 아니라 reliable byte stream을 제공한다.**

즉, 네트워크는 “한 번의 `write`가 한 번의 `read`와 1:1 대응한다”는 보장을 주지 않는다. 그래서 short count, Rio, line protocol, message framing 문제가 등장한다.

## 9. 꼬리를 무는 이해 사슬

다음 질문 사슬을 자기 말로 설명할 수 있어야 한다.

```text
Q. 서버를 찾는다는 건 무슨 뜻인가?
A. 이름을 주소로 바꾸는 것이다.

Q. 주소만 알면 충분한가?
A. 아니다. host 안의 service를 찾는 port가 더 필요하다.

Q. 그러면 실제 연결 대상은 무엇인가?
A. socket address다.

Q. connection의 identity는 무엇인가?
A. client와 server의 socket pair다.
```

## 10. 생각거리

### 생각거리 1 | 자가점검

**Q1. IP address, domain name, port, socket address는 각각 무엇을 식별하는가?**  
**해설.** IP address는 네트워크 위의 호스트를 식별하고, domain name은 사람이 쓰기 쉬운 이름으로 그 호스트나 서비스를 가리킨다. port는 한 호스트 안에서 어떤 서비스 종단점을 찾을지 구분하는 번호다. socket address는 결국 “어느 호스트의 어느 포트인가”를 함께 묶은 주소다. 이 네 단계를 구분해야 `www.example.com:80`이 무엇을 뜻하는지 흐릿하지 않게 보인다.

**Q2. 왜 network byte order를 따로 정하고, 왜 주소 문자열과 이진 주소 사이를 변환해야 하는가?**  
**해설.** 서로 다른 머신이 정수 바이트를 같은 순서로 해석하려면 공통 규칙이 필요하기 때문이다. TCP/IP는 그 규칙으로 big-endian, 곧 network byte order를 택한다. 한편 사람은 `128.2.194.242` 같은 문자열을 기억하지만, 시스템 호출은 이진 주소 구조를 기대한다. 그래서 주소는 항상 “사람이 읽는 표현”과 “커널이 쓰는 표현” 사이를 오가게 된다.

**Q3. 인터넷 connection의 세 가지 핵심 성질은 무엇이며, 왜 중요한가?**  
**해설.** 교재는 connection을 point-to-point, full-duplex, reliable한 바이트 스트림으로 정리한다. point-to-point라는 말은 특정 두 프로세스의 대화라는 뜻이고, full-duplex는 양방향 송수신이 가능하다는 뜻이며, reliable은 순서와 전달을 어느 정도 믿고 프로그래밍할 수 있다는 뜻이다. Echo와 HTTP를 처음 배울 때 이 세 성질을 한꺼번에 붙들면 TCP가 제공하는 추상이 훨씬 분명해진다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 도메인 이름과 IP 주소를 굳이 분리해 두는가?**  
**해설.** 사람은 이름을 기억하고, 네트워크는 수치 주소를 전달에 사용한다. 둘을 분리하면 실제 서버 주소나 배치가 바뀌어도 사용자는 같은 이름을 계속 쓸 수 있다. 즉, 이름은 인간 친화적인 인터페이스이고 주소는 네트워크 친화적인 식별자다. 이 분리가 있어야 서비스 이전, 장애 대응, 로드밸런싱 같은 운영상의 유연성이 생긴다.

**Q2. 하나의 도메인 이름이 여러 IP 주소와 연결될 수 있다는 사실은 서버 설계에 어떤 의미를 가지는가?**  
**해설.** “한 이름 = 한 서버”라는 단순한 그림이 실제 서비스에서는 잘 맞지 않는다는 뜻이다. 같은 이름 뒤에 여러 서버를 두면 부하 분산과 장애 우회가 가능하고, 지역에 따라 다른 서버를 보여 주는 구조도 만들 수 있다. 따라서 DNS는 단순한 전화번호부가 아니라, 서비스 배치를 감추고 조정하는 중요한 추상화 계층이다.

**Q3. 왜 연결의 정체성을 server port 하나가 아니라 socket pair로 이해해야 하는가?**  
**해설.** 서버는 같은 well-known port를 유지한 채 여러 클라이언트와 동시에 대화할 수 있다. 이때 각 연결을 구분하는 것은 서버 포트 하나가 아니라 `(client addr:port, server addr:port)`의 쌍이다. 이 관점을 놓치면 “서버 포트 80이니 연결도 하나”처럼 오해하게 된다. 반대로 socket pair를 이해하면 왜 `accept`가 클라이언트마다 다른 connected descriptor를 주는지가 자연스럽다.

### 생각거리 3 | 확장과 전이

**Q1. `localhost`와 loopback 주소를 적극적으로 쓰는 습관이 왜 중요한가?**  
**해설.** 네트워크 프로그램을 가장 먼저 자기 기계 안에서 검증할 수 있기 때문이다. 외부 DNS, 방화벽, 공인망 지연 같은 변수를 떼어 내면 프로그램 자체의 논리 오류를 훨씬 빨리 찾을 수 있다. 학습과 디버깅에서는 “먼저 localhost에서 성립하는가”가 좋은 출발점이 된다.

**Q2. 교재는 IPv4를 중심으로 설명하면서도 왜 protocol-independent code를 강조하는가?**  
**해설.** 현재 설명을 단순하게 유지하되, 코드 자체는 특정 주소 체계에 너무 일찍 묶이지 않게 하려는 전략이다. 강의도 IPv4를 예시로 들면서 `getaddrinfo`와 `getnameinfo`를 통해 주소 표현을 추상화하라고 권한다. 즉, 개념 학습은 간결하게, 구현 습관은 오래 가도록 설계한 셈이다.

**Q3. TCP connection을 “reliable byte stream”으로 이해하면 무엇이 보이고, 무엇이 가려지는가?**  
**해설.** 이 추상은 HTTP와 Echo를 배우기에 매우 강력하다. 프로그래머는 패킷 손실이나 재정렬을 직접 관리하지 않고도 순서 있는 바이트 흐름을 읽고 쓸 수 있다. 하지만 동시에 메시지 경계가 없다는 사실, 네트워크 지연과 끊김이 완전히 사라지지 않는다는 사실은 가려지기 쉽다. 그래서 “도움이 되는 추상”이면서도 “완전히 모든 현실을 숨기지는 않는 추상”이라고 이해해야 한다.

---

# Helix 4. Sockets interface를 생애주기로 읽기 (11.4.1~11.4.8)

## 1. 이 바퀴의 질문

- 소켓 인터페이스는 왜 이렇게 함수가 많은가?
- 클라이언트와 서버는 각각 어떤 순서로 상태를 바꾸는가?
- `getaddrinfo`, `open_clientfd`, `open_listenfd`는 왜 중요한가?

## 2. 소켓은 무엇인가

커널 관점에서 socket은 communication endpoint다. 프로그램 관점에서 socket은 **열린 파일**이며 **file descriptor**로 보인다. 이중 시각이 중요하다.

- 커널: 네트워크 통신 종단점
- 프로그램: `int fd`로 다루는 열린 I/O 대상

즉, 11장은 네트워크를 배우는 장이면서 동시에 **Unix file descriptor abstraction의 확장**을 보는 장이다.

## 3. Socket address structures (11.4.1)

인터넷 전용 주소 구조체는 다음처럼 생긴다.

```c
struct sockaddr_in {
    uint16_t       sin_family;   /* AF_INET */
    uint16_t       sin_port;     /* network byte order */
    struct in_addr sin_addr;     /* network byte order */
    unsigned char  sin_zero[8];
};
```

하지만 `connect`, `bind`, `accept`는 이를 직접 받지 않고 다음 generic 구조를 받는다.

```c
typedef struct sockaddr SA;

struct sockaddr {
    uint16_t sa_family;
    char     sa_data[14];
};
```

왜 이렇게 설계되었을까? 소켓 인터페이스가 설계될 당시 C에는 오늘날의 `void *` 같은 일반 포인터가 없었다. 그래서 “모든 프로토콜 주소 구조를 받는 공통 인터페이스”가 필요했고, 그 결과가 `sockaddr *`다.

초보자가 꼭 기억할 사실은 다음이다.

- `sockaddr_in`은 인터넷 주소를 담는 **실제 내용물**이다.
- `sockaddr *`는 API가 받는 **공통 인터페이스 형식**이다.
- 그래서 인터넷 코드에서는 캐스팅이 등장한다.

## 4. `socket`: 시작이지만 아직 끝이 아니다 (11.4.2)

```c
int socket(int domain, int type, int protocol);
```

전통적으로 TCP 연결용 소켓은 다음처럼 만든다.

```c
int fd = socket(AF_INET, SOCK_STREAM, 0);
```

하지만 여기서 반환된 디스크립터는 아직 **partially opened**다. 즉, 연결된 상태가 아니다. 이 디스크립터가 클라이언트의 종단점이 될지, 서버의 listen 종단점이 될지는 아직 정해지지 않았다.

이것을 파일과 비교하면 차이가 보인다.

- `open("file")`은 이미 특정 파일과 연결된다.
- `socket(...)`은 아직 “무슨 상대와 어떤 역할로 통신할지” 확정되지 않는다.

그래서 이후 단계가 필요하다.

## 5. 클라이언트 경로: `connect` (11.4.3)

클라이언트는 `connect`로 partially opened socket을 실제 연결 종단점으로 만든다.

```c
int connect(int clientfd, const struct sockaddr *addr, socklen_t addrlen);
```

이 함수의 의미를 초심자는 종종 “서버에게 문을 두드린다” 정도로 이해한다. 더 정확하게는 다음과 같다.

> **`connect`는 클라이언트 쪽 디스크립터를 특정 server socket address에 대응하는 connected endpoint로 완성한다.**

성공하면 이제 `clientfd`는 읽기와 쓰기에 사용할 수 있다. 그리고 클라이언트 포트는 보통 커널이 자동으로 할당한다.

### 5.1 클라이언트 소켓 생애주기

```text
getaddrinfo
-> socket
-> connect
-> rio_read / rio_write
-> close
```

## 6. 서버 경로: `bind`, `listen`, `accept` (11.4.4~11.4.6)

서버는 세 단계로 간다.

### 6.1 `bind`

```c
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```

이 디스크립터가 **어떤 local address:port를 대표할지** 커널에게 알린다.

### 6.2 `listen`

```c
int listen(int sockfd, int backlog);
```

커널에게 “이 소켓은 클라이언트처럼 connect를 할 소켓이 아니라, 연결 요청을 기다리는 소켓이다”라고 알린다. 즉, **능동 소켓이 아니라 리스닝 소켓**으로 바꾼다.

### 6.3 `accept`

```c
int accept(int listenfd, struct sockaddr *addr, socklen_t *addrlen);
```

이 함수가 매우 중요하다. `accept`는 기존 `listenfd`를 connected 상태로 바꾸지 않는다. 대신, **새로운 connected descriptor**를 만들어 반환한다.

여기서 구분이 생긴다.

- **listening descriptor**: 연결 요청을 기다리는 종단점
- **connected descriptor**: 실제 데이터 송수신용 종단점

이 구분은 단지 API 모양의 문제가 아니다. 12장의 concurrent server를 가능하게 하는 핵심 구조다.

```text
하나의 listenfd는 계속 살아 있다
-> accept 할 때마다 새로운 connfd가 태어난다
-> 각 connfd는 각 클라이언트와의 대화를 담당한다
```

## 7. 왜 listening descriptor와 connected descriptor를 구분하는가 (11.4.6)

이 질문은 꼭 자기 힘으로 답해야 한다.

listenfd와 connfd를 구분하지 않으면 서버는 “다음 클라이언트를 기다리는 역할”과 “현재 클라이언트와 대화하는 역할”을 같은 객체에 실어야 한다. 그러면 동시성이 매우 어려워진다.

반대로 둘이 분리되면,

- listenfd는 계속 `accept`만 담당하고
- 각 connfd는 각 요청 처리 로직으로 넘겨줄 수 있다.

즉, **동시성 서버의 구조적 가능성**이 이 구분에서 나온다.

## 8. `getaddrinfo`: 현대적인 이름/서비스 변환 (11.4.7)

예전 코드에서는 `gethostbyname`, `getservbyname` 같은 함수를 썼다. 현대적인 코드는 `getaddrinfo`를 쓴다.

```c
int getaddrinfo(const char *host, const char *service,
                const struct addrinfo *hints,
                struct addrinfo **result);
```

이 함수의 장점은 세 가지다.

1. **reentrant**: threaded code에서 안전하다.
2. **protocol-independent**: IPv4/IPv6를 가리지 않는다.
3. **표현과 실행을 분리**한다: 문자열 입력을 실제 socket address 후보 목록으로 바꾼다.

결과가 linked list인 이유는 현실 때문이다.

- 하나의 이름이 여러 주소로 풀릴 수 있고
- 어떤 주소는 현재 환경에서 실패할 수 있으며
- 클라이언트와 서버 모두 “성공하는 첫 후보”를 시도해야 하기 때문이다.

### 8.1 클라이언트용 `getaddrinfo` 패턴

```c
struct addrinfo hints, *listp, *p;
int clientfd;

memset(&hints, 0, sizeof(hints));
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_NUMERICSERV | AI_ADDRCONFIG;
Getaddrinfo(host, port, &hints, &listp);

for (p = listp; p != NULL; p = p->ai_next) {
    clientfd = socket(p->ai_family, p->ai_socktype, p->ai_protocol);
    if (clientfd < 0) continue;
    if (connect(clientfd, p->ai_addr, p->ai_addrlen) != -1) break;
    Close(clientfd);
}
Freeaddrinfo(listp);
```

이 코드가 보여 주는 핵심은 다음이다.

- 주소는 하나가 아니라 후보군이다.
- 네트워크 프로그래밍은 종종 “첫 번째 성공하는 후보를 찾는 작업”이다.

### 8.2 서버용 `getaddrinfo` 패턴

```c
struct addrinfo hints, *listp, *p;
int listenfd, optval = 1;

memset(&hints, 0, sizeof(hints));
hints.ai_socktype = SOCK_STREAM;
hints.ai_flags = AI_PASSIVE | AI_ADDRCONFIG | AI_NUMERICSERV;
Getaddrinfo(NULL, port, &hints, &listp);

for (p = listp; p != NULL; p = p->ai_next) {
    listenfd = socket(p->ai_family, p->ai_socktype, p->ai_protocol);
    if (listenfd < 0) continue;

    setsockopt(listenfd, SOL_SOCKET, SO_REUSEADDR,
               (const void *)&optval, sizeof(int));

    if (bind(listenfd, p->ai_addr, p->ai_addrlen) == 0) break;
    Close(listenfd);
}

Listen(listenfd, 1024);
Freeaddrinfo(listp);
```

여기서 중요한 포인트는 세 가지다.

- `host = NULL` + `AI_PASSIVE`: wildcard address 생성
- `SO_REUSEADDR`: 서버 재시작 시 “Address already in use” 완화
- `Listen(...)`: 마지막 단계까지 가야 listenfd가 완성된다

## 9. `getnameinfo`: 역방향 변환 (11.4.7)

`accept`로 받은 클라이언트 주소를 사람이 읽는 문자열로 바꾸고 싶을 때 쓴다.

```c
char host[MAXLINE], serv[MAXLINE];
Getnameinfo((SA *)&clientaddr, clientlen,
            host, MAXLINE, serv, MAXLINE, 0);
printf("Accepted connection from (%s, %s)\n", host, serv);
```

이 함수는 네트워크 프로그래밍에서 “지금 누구와 대화 중인가?”를 눈에 보이게 해 준다.

## 10. `open_clientfd`와 `open_listenfd`: 왜 중요한가 (11.4.8)

CS:APP는 반복되는 보일러플레이트 코드를 helper function으로 감싼다.

- `open_clientfd`: 클라이언트 연결을 여는 패턴을 캡슐화
- `open_listenfd`: 서버 listenfd 생성 패턴을 캡슐화

입문자에게 이 함수들이 중요한 이유는 단순 편의 때문만이 아니다. 이 함수들은 “네트워크 프로그램이 보통 어떤 절차를 밟는가”를 **관용적 usage pattern**으로 보여 준다.

강의에서 특히 강조되는 메시지도 여기 있다.

> `getaddrinfo`는 다소 복잡하지만, 실전에서는 몇 가지 사용 패턴만 익히면 된다.

즉, 네트워크 프로그래밍은 모든 가능성을 한꺼번에 외우는 것이 아니라 **반복되는 패턴을 몸에 붙이는 과정**이다.

## 11. 정리: 소켓 인터페이스 한 장 도표

```text
클라이언트
문자열 host/service
-> getaddrinfo
-> socket
-> connect
-> connected descriptor
-> Rio/Unix I/O
-> close

서버
문자열 port
-> getaddrinfo(NULL, port, AI_PASSIVE)
-> socket
-> setsockopt(SO_REUSEADDR)
-> bind
-> listen
-> accept
-> connected descriptor
-> Rio/Unix I/O
-> close(connfd)
```

## 12. 생각거리

### 생각거리 1 | 자가점검

**Q1. `socket`, `connect`, `bind`, `listen`, `accept`는 연결 생애주기에서 각각 어떤 역할을 맡는가?**  
**해설.** `socket`은 통신 종단점을 만들지만, 아직 상대와의 관계가 완성된 것은 아니다. 클라이언트는 `connect`로 서버에 능동적으로 다가가고, 서버는 `bind`로 자기 주소를 묶고 `listen`으로 기다리는 소켓이라고 선언한 뒤 `accept`로 실제 연결을 받아들인다. 이 순서를 클라이언트 경로와 서버 경로로 나누어 설명할 수 있어야 소켓 인터페이스의 뼈대를 이해한 것이다.

**Q2. `sockaddr_in`의 핵심 필드는 무엇이며, 왜 실제 함수 인자는 `sockaddr *` 형태인가?**  
**해설.** 인터넷 프로그래밍에서 실질적으로 중요한 필드는 `sin_family`, `sin_port`, `sin_addr`다. 그런데 `connect`, `bind`, `accept` 같은 함수는 이를 일반화한 `sockaddr *`를 받는다. 이유는 소켓 인터페이스가 특정 주소 체계 하나에만 묶이지 않게 설계되었기 때문이다. 즉, 인터넷 주소는 `sockaddr_in`으로 표현되지만, API 모양은 더 넓은 세계를 염두에 두고 있다.

**Q3. `getaddrinfo`, `getnameinfo`, `open_clientfd`, `open_listenfd`는 각각 무엇을 도와주는가?**  
**해설.** `getaddrinfo`는 host/service 문자열을 실제 소켓 주소 후보 리스트로 바꿔 주고, `getnameinfo`는 그 역방향으로 주소를 사람이 읽는 host/service 문자열로 바꿔 준다. `open_clientfd`와 `open_listenfd`는 이 과정을 바탕으로 자주 쓰는 클라이언트 연결과 서버 준비 패턴을 묶은 helper다. 강의가 “복잡하지만 몇 가지 사용 패턴만 익히면 된다”고 말하는 이유가 여기에 있다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 `socket`이 끝이 아니라 시작이며, `connect` 이전의 소켓을 “partially opened”라고 부르는가?**  
**해설.** 파일은 `open`만 하면 이미 특정 객체와 연결된다. 하지만 네트워크 소켓은 생성 뒤에도 아직 누구와 이야기할지가 정해지지 않았다. 클라이언트는 `connect`를 해야 비로소 통신 가능한 connected endpoint가 된다. 그래서 `socket`은 재료 준비에 가깝고, `connect`가 실제 대화의 문을 여는 단계라고 이해하는 편이 맞다.

**Q2. 왜 서버는 listening descriptor와 connected descriptor를 구분해야 하며, `accept`는 왜 새 디스크립터를 돌려주는가?**  
**해설.** 서버는 새 손님을 계속 받아야 하는 창구와, 이미 들어온 손님 한 명과 실제 대화를 나누는 창구를 분리할 필요가 있다. `listenfd`는 연결 요청을 받는 입구이고, `connfd`는 한 연결의 송수신을 담당하는 대화 채널이다. `accept`가 새 디스크립터를 만드는 이유는, 서버가 원래의 입구를 유지한 채 다음 요청도 계속 받을 수 있게 하기 위해서다. 이 분리가 바로 다음 장의 동시성 서버 설계로 이어진다.

**Q3. 왜 `getaddrinfo`는 단일 주소가 아니라 linked list를 반환하며, 서버에서 `AI_PASSIVE`가 중요한 이유는 무엇인가?**  
**해설.** 하나의 이름이 여러 주소로 풀릴 수 있고, 어떤 주소는 현재 시스템이나 네트워크 조건에서 연결에 실패할 수 있기 때문이다. 그래서 애플리케이션은 후보군을 순회하며 가장 적절한 항목을 시험한다. 서버에서 `AI_PASSIVE`와 `host = NULL`을 함께 쓰면 특정 한 IP에 묶이지 않고 해당 호스트의 적절한 수신 주소를 얻기 쉽다. 이것이 “문자열 이름 -> 후보 구조체들 -> 실제 시스템 호출”이라는 현대적 패턴의 핵심이다.

### 생각거리 3 | 확장과 전이

**Q1. `SO_REUSEADDR`는 왜 교육용 예제를 넘어 실전 감각과도 연결되는가?**  
**해설.** 서버를 자주 내리고 다시 올리는 개발 과정에서는 포트가 잠시 재사용 불가능 상태에 남아 있는 일이 흔하다. 이때 `SO_REUSEADDR`를 설정하면 실험과 수정의 사이클이 훨씬 매끄러워진다. 단순한 편의 옵션처럼 보이지만, 실제로는 운영체제의 연결 종료 흔적과 서버 재시작 사이의 마찰을 드러내는 지점이다.

**Q2. 소켓 API가 대체로 blocking 기반이라는 사실은 이후의 프로그램 설계에 어떤 영향을 주는가?**  
**해설.** `connect`, `accept`, `read` 계열 호출은 상대가 늦거나 네트워크가 느리면 프로그램의 제어 흐름을 오래 붙잡을 수 있다. 그래서 간단한 콘솔 예제에는 잘 맞지만, 동시에 많은 연결을 처리하거나 응답성이 중요한 프로그램에서는 그대로 두기 어렵다. 결국 process, thread, event-driven I/O 같은 구조를 고민하게 되는데, 이것이 12장으로 넘어가는 실제 동인이다.

**Q3. 왜 책은 하드코딩된 `AF_INET`, `SOCK_STREAM`보다 helper와 protocol-independent 코드를 장려하는가?**  
**해설.** 처음에는 상수를 직접 적는 코드가 더 단순해 보이지만, 그 코드는 주소 체계나 운영 환경이 바뀌면 금방 낡는다. 반대로 `getaddrinfo` 기반 코드는 IPv4/IPv6 혼합 환경이나 다른 네트워크 설정에도 더 유연하다. 학습 단계에서는 단순성을 위해 구체 예를 보되, 구현 습관은 처음부터 오래 살아남는 형태로 가져가라는 뜻으로 읽으면 좋다.

---

# Helix 5. Echo Client/Server로 소켓 생애주기를 손에 익히기 (11.4.9)

## 1. 이 바퀴의 질문

- 책이 왜 하필 Echo를 예제로 드는가?
- `rio_readlineb`와 `rio_writen`은 실제 세션에서 어떻게 움직이는가?
- EOF는 네트워크에서 무슨 뜻인가?

## 2. Echo는 왜 좋은 첫 예제인가

Echo의 장점은 서비스 로직이 거의 없다는 데 있다. 서버는 받은 줄을 그대로 되돌려 줄 뿐이다. 그래서 복잡한 비즈니스 로직이 사라지고, **통신 구조만 선명하게 남는다.**

즉, Echo는 “소켓 API를 배우기 위한 순수한 회로도”다.

## 3. Echo client의 흐름

### 3.1 핵심 골격

```c
int clientfd = Open_clientfd(host, port);
rio_t rio;
char buf[MAXLINE];

Rio_readinitb(&rio, clientfd);
while (Fgets(buf, MAXLINE, stdin) != NULL) {
    Rio_writen(clientfd, buf, strlen(buf));
    Rio_readlineb(&rio, buf, MAXLINE);
    Fputs(buf, stdout);
}
Close(clientfd);
```

### 3.2 이 코드를 어떻게 읽어야 하는가

이 코드는 두 개의 I/O 세계를 이어 준다.

- 표준 입력 `stdin`
- 네트워크 연결 `clientfd`

클라이언트는 사람의 입력을 받아 네트워크에 보내고, 네트워크에서 돌아온 응답을 화면에 보여 준다. 그래서 Echo client는 일종의 **입력원 하나를 더 가진 필터 프로그램**처럼 이해할 수 있다.

### 3.3 `Rio_readinitb`의 의미

`rio_readinitb(&rio, clientfd)`는 “이 connected descriptor를 버퍼드 Rio 객체로 다루겠다”고 선언하는 단계다. 이후 `rio_readlineb`는 같은 연결에서 줄 단위 텍스트를 안정적으로 읽는다.

## 4. Echo server의 흐름

### 4.1 main 루프

```c
int listenfd = Open_listenfd(port);
while (1) {
    clientlen = sizeof(clientaddr);
    connfd = Accept(listenfd, (SA *)&clientaddr, &clientlen);
    Getnameinfo((SA *)&clientaddr, clientlen,
                hostname, MAXLINE, portstr, MAXLINE, 0);
    echo(connfd);
    Close(connfd);
}
```

이 루프는 거의 모든 서버의 원형이다.

```text
accept loop + per-connection service
```

### 4.2 echo 함수

```c
void echo(int connfd) {
    size_t n;
    char buf[MAXLINE];
    rio_t rio;

    Rio_readinitb(&rio, connfd);
    while ((n = Rio_readlineb(&rio, buf, MAXLINE)) != 0) {
        Rio_writen(connfd, buf, n);
    }
}
```

여기서 주목할 것은 두 가지다.

1. `connfd` 하나가 한 클라이언트와의 대화 전체를 담당한다.
2. `Rio_readlineb(...) != 0`가 끝나는 지점이 곧 **EOF**다.

## 5. 네트워크에서 EOF가 뜻하는 것

디스크 파일에서 EOF는 파일 끝이다. 네트워크에서 EOF는 다르다. 네트워크 연결에서 EOF는 보통 **반대편 프로세스가 자기 쪽 연결을 닫았음**을 뜻한다.

즉,

- EOF는 문자 하나가 아니다.
- “EOF 패킷” 같은 것이 날아오는 것도 아니다.
- 커널이 상태를 감지하고 read 계열 함수가 0을 반환함으로써 알려 준다.

이 차이를 분명히 알아야 `close`와 세션 종료를 이해할 수 있다.

## 6. 왜 Echo server는 iterative server인가

Echo server는 한 번에 한 클라이언트만 처리한다.

```text
accept
-> echo(connfd)
-> close(connfd)
-> 다음 accept
```

따라서 현재 클라이언트 처리가 끝나기 전에는 다음 클라이언트를 받지 못한다. 이 구조는 단순하지만 현실 서비스에는 한계가 있다. 바로 이 한계가 12장의 동시성 장으로 넘어가는 동기다.

## 7. Echo를 통해 11장을 다시 보면

Echo는 사실 11장의 축소판이다.

- `Open_clientfd` / `Open_listenfd`: helper patterns
- `Accept`: 연결 수락
- `Getnameinfo`: 연결 상대 식별
- `Rio_readlineb`: 텍스트 프로토콜 입력
- `Rio_writen`: 응답 출력
- `Close`: 세션 종료

여기서 서비스 로직만 바꾸면 HTTP 서버, proxy, application server로 확장된다.

## 8. 생각거리

### 생각거리 1 | 자가점검

**Q1. Echo client가 한 번의 반복에서 수행하는 핵심 단계는 무엇인가?**  
**해설.** 표준 입력에서 한 줄을 읽고, 그 줄을 서버에 쓰고, 서버가 돌려준 한 줄을 다시 읽어 표준 출력에 쓴다. 구조는 단순하지만, 이 안에 로컬 입력, 네트워크 전송, 원격 응답, 로컬 출력이 모두 들어 있다. 그래서 Echo client는 “사람의 입력을 네트워크로 보내고 다시 사람에게 돌려주는 필터”처럼 이해할 수 있다.

**Q2. Echo server의 기본 뼈대는 어떻게 생겼는가?**  
**해설.** 서버는 `Open_listenfd`로 리스닝 소켓을 열고, 무한 루프 안에서 `Accept`를 기다리며, 연결이 오면 클라이언트 정보를 출력하고, `echo(connfd)`로 서비스를 수행한 뒤 `Close(connfd)`를 호출한다. 이 구조는 “accept loop + per-connection service”라는 서버의 전형적인 형태를 보여 준다. 앞으로 웹 서버나 프록시를 읽을 때도 이 뼈대가 자꾸 반복된다는 점이 중요하다.

**Q3. 네트워크 연결에서 EOF를 감지한다는 것은 정확히 무엇을 뜻하는가?**  
**해설.** 상대편이 자기 쪽 연결을 닫았고, 그 결과 읽기 함수가 0을 반환한다는 뜻이다. 이것은 어떤 특별한 ‘EOF 문자’가 전송된 것이 아니라, 커널이 연결 종료 상태를 읽기 결과로 알려 주는 것이다. 이 차이를 이해해야 Echo와 Tiny에서 루프가 왜 끝나는지, `close`가 왜 상대에게 의미 있는 신호인지 정확히 해석할 수 있다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 Echo 예제는 네트워크 프로그래밍의 좋은 첫 종합 예제가 되는가?**  
**해설.** 서비스 로직이 거의 없어서 통신 구조만 선명하게 남기 때문이다. 주소 해석, 연결 수립, 한 줄 단위 I/O, 종료 처리라는 최소 회로가 예제 전체를 이끈다. 강의가 Echo 세션 도식을 반복해서 보여 주는 이유도, 복잡한 HTTP나 Tiny보다 먼저 “연결 위의 대화” 자체를 손에 익히게 하려는 데 있다.

**Q2. Echo에서 `rio_readlineb`가 핵심인 이유와, 그 선택이 암묵적으로 전제하는 것은 무엇인가?**  
**해설.** Echo는 줄 단위로 끊기는 텍스트 입력을 주고받기 때문에 `rio_readlineb`가 잘 맞는다. 이 함수는 short count와 버퍼링 문제를 감추면서 “한 줄이 하나의 의미 단위”라는 프로토콜 감각을 살려 준다. 동시에 이 선택은 메시지 경계를 개행으로 정하는 텍스트 프로토콜을 전제한다. 그래서 Echo를 이해하면 HTTP 같은 line-oriented protocol로 자연스럽게 넘어갈 수 있다.

**Q3. 왜 클라이언트가 명시적으로 `Close(clientfd)`를 호출하는 습관이 중요할까?**  
**해설.** 프로세스 종료 시 커널이 대부분을 정리해 준다고 해도, 코드가 자기 자원의 생애주기를 분명히 드러내기 때문이다. 더 중요한 것은 이 `close`가 서버 쪽에서 EOF로 감지되어 대화 종료의 신호가 된다는 점이다. 즉, `close`는 단순 정리 명령이 아니라 연결 서사의 마지막 동사다.

### 생각거리 3 | 확장과 전이

**Q1. Echo 서버를 바이너리 프로토콜 서버로 바꾸려면 가장 먼저 무엇이 달라져야 할까?**  
**해설.** 줄 단위 사고를 버리고 메시지 경계를 다른 방식으로 정의해야 한다. 예를 들어 고정 길이 헤더에 본문 길이를 넣거나, `rio_readn`으로 정확한 바이트 수를 읽는 식의 프레이밍이 필요해진다. 이는 “TCP는 메시지가 아니라 바이트 스트림을 준다”는 사실을 더 강하게 체감하게 만든다.

**Q2. Echo server가 iterative server라는 사실은 실무적으로 어떤 한계를 뜻하는가?**  
**해설.** 한 클라이언트가 오래 머무르면 뒤의 클라이언트는 `accept` 단계에서 대기해야 한다는 뜻이다. 서비스가 단순할 때는 괜찮지만, 느린 클라이언트나 긴 작업이 섞이면 처리량과 응답성이 급격히 나빠진다. 그래서 Echo는 구조를 배우기에는 좋지만, 실제 다중 사용자 서비스를 위해서는 동시성 설계가 뒤따라야 한다.

**Q3. Echo 예제에서 `Getnameinfo`로 클라이언트 정보를 로그처럼 찍는 습관은 왜 중요한가?**  
**해설.** 서버가 지금 누구와 대화하고 있는지 관찰할 수 있게 해 주기 때문이다. 학습 단계에서는 단순한 출력 같지만, 실제 운영에서는 접속 추적, 장애 분석, 오남용 탐지의 기본 자료가 된다. 즉, Echo는 기능적으로 단순하지만, 관찰 가능성(observability)의 출발점까지 슬쩍 보여 주는 예제다.

---

# Helix 6. HTTP, 정적 콘텐츠, 동적 콘텐츠, CGI (11.5.1~11.5.4)

## 1. 이 바퀴의 질문

- 웹 서버는 sockets 위에 무엇을 얹은 것인가?
- URL, HTTP request, response, CGI는 어떻게 이어지는가?
- 왜 Tiny는 HTTP/1.0으로 이해하는 것이 좋은가?

## 2. Web basics: 연결 위에 얹힌 텍스트 프로토콜

웹 브라우저와 웹 서버는 **HTTP**라는 텍스트 기반 애플리케이션 프로토콜로 통신한다.

초보자에게 중요한 감각은 이것이다.

> 웹은 “특별한 GUI 시스템”이 아니라, 결국 **연결 위에서 텍스트 줄과 바이트 본문을 주고받는 시스템**이다.

그래서 `telnet`, `nc`, `curl -v` 같은 도구로 직접 HTTP 요청을 손으로 보내 볼 수 있다.

## 3. HTML, hyperlinks, MIME type (11.5.1~11.5.2)

웹을 웹답게 만드는 것은 HTML과 hyperlink다. 하지만 서버 입장에서 보면 본질은 더 단순하다.

서버가 보내는 것은 결국 **MIME type이 붙은 바이트열**이다.

- `text/html`
- `text/plain`
- `image/png`
- `image/jpeg`
- `image/gif`

브라우저는 `Content-Type` 헤더를 보고 받은 바이트를 어떻게 해석할지 결정한다. 즉, 웹 서버는 단지 파일을 보내는 것이 아니라 **그 파일이 어떤 의미의 데이터인지도 함께 말해 준다.**

## 4. URL을 두 개의 관점으로 보기 (11.5.2)

예를 들어 다음 URL을 보자.

```text
http://www.google.com:80/index.html
```

이 URL은 클라이언트와 서버가 서로 다른 부분에 주목한다.

### 4.1 클라이언트 관점

클라이언트는 다음을 본다.

- protocol: `http`
- host: `www.google.com`
- port: `80`

즉, **어디로 연결해야 하는가**를 본다.

### 4.2 서버 관점

서버는 다음을 본다.

- URI suffix: `/index.html`

즉, **자기 자원 중 무엇을 찾아야 하는가**를 본다.

여기서 아주 중요한 오해 방지 포인트가 있다.

- URL suffix의 `/`는 운영체제 전체 루트 디렉터리를 뜻하지 않는다.
- 그것은 서버가 정한 **content root 기준의 경로 시작점**이다.

또한 정적/동적을 URL만 보고 구분하는 표준 규칙은 없다. 서버가 정한 정책이 있을 뿐이다. Tiny는 단순히 `cgi-bin`이 들어 있으면 동적이라고 본다.

## 5. HTTP request와 response의 형태 (11.5.3)

### 5.1 Request

HTTP request는 다음 구조를 갖는다.

```text
request line
request headers
blank line
(optional body)
```

대표적인 request line은 다음과 같다.

```text
GET /index.html HTTP/1.1

```

핵심은 세 요소다.

- **method**: 무엇을 할 것인가 (`GET`, `POST` ...)
- **URI**: 무엇을 요청하는가
- **version**: 어떤 HTTP 규약을 쓰는가

### 5.2 Response

HTTP response는 다음 구조를 갖는다.

```text
response line
response headers
blank line
response body
```

예를 들어,

```text
HTTP/1.0 200 OK

Content-length: 123

Content-type: text/html



<html> ...
```

주요 response header는 다음이다.

- `Content-Length`: 본문 크기
- `Content-Type`: 본문 해석 방식

### 5.3 왜 blank line이 중요한가

헤더가 어디서 끝나고 본문이 어디서 시작하는지 알려 주기 때문이다. Tiny의 `read_requesthdrs`가 바로 이 blank line을 만날 때까지 읽는 이유가 여기에 있다.

## 6. HTTP/1.0과 HTTP/1.1을 어떻게 구분해 볼 것인가

Tiny를 이해할 때는 **HTTP/1.0** 감각이 특히 중요하다.

### 6.1 HTTP/1.0 감각

- 한 transaction마다 한 connection을 주로 쓴다.
- 응답 후 서버가 connection을 닫는 것이 자연스럽다.
- Tiny가 이 모델을 따른다.

### 6.2 HTTP/1.1 감각

- persistent connection 지원
- 여러 transaction을 같은 connection 위에서 처리 가능
- `Host` 헤더가 사실상 필수
- `Connection: keep-alive`, chunked encoding 같은 기능 등장

입문 단계에서 중요한 결론은 이것이다.

> **Tiny는 일부러 단순화를 위해 HTTP/1.0 iterative server로 설계되어 있다.**

즉, 오늘날 웹 브라우저와 완전히 동일한 현실을 재현하기보다, 웹 서버의 핵심 구조를 드러내기 위해 모델을 줄여 놓은 것이다.

## 7. 정적 콘텐츠와 동적 콘텐츠 (11.5.2, 11.5.4)

### 7.1 정적 콘텐츠

정적 콘텐츠는 서버 디스크의 파일을 그대로 읽어 보내는 것이다.

```text
URI -> 파일명 -> 파일 읽기 -> Content-Type/Length 헤더 -> 본문 전송
```

### 7.2 동적 콘텐츠

동적 콘텐츠는 프로그램을 실행해 그 출력을 보내는 것이다.

```text
URI -> 실행 파일 + 인자 -> fork/execve -> child output -> 본문 전송
```

입문자가 자주 놓치는 사실은, 서버 입장에서 둘 다 결국 **URI를 어떤 자원으로 해석하느냐의 차이**라는 점이다. 하나는 “읽을 파일”, 다른 하나는 “실행할 파일”일 뿐이다.

## 8. CGI를 구조로 이해하기 (11.5.4)

CGI는 단순히 “옛날 기술”이 아니라, 서버와 자식 프로세스 사이의 인터페이스를 노출해서 보여 주는 교육용 모델로 매우 좋다.

### 8.1 CGI가 해결해야 할 질문

동적 콘텐츠를 만들려면 다음 질문에 답해야 한다.

1. 클라이언트는 프로그램 인자를 어떻게 넘기는가?
2. 서버는 그 인자를 자식에게 어떻게 전달하는가?
3. 서버는 요청 관련 다른 정보도 어떻게 넘기는가?
4. 자식이 만든 출력을 서버는 어떻게 클라이언트에게 보낼까?

### 8.2 GET에서 인자를 어떻게 넘기는가

예를 들어,

```text
GET /cgi-bin/adder?15213&18213 HTTP/1.0
```

여기서

- `adder`는 CGI program
- `?` 뒤는 argument list
- `&`는 argument separator
- 공백은 `+` 또는 `%20`으로 encode

서버는 이 문자열을 `QUERY_STRING` 환경변수로 자식에게 넘길 수 있다.

### 8.3 자식 출력은 어떻게 클라이언트에게 가는가

핵심은 `dup2`다.

```c
setenv("QUERY_STRING", cgiargs, 1);
Dup2(connfd, STDOUT_FILENO);
Execve(filename, emptylist, environ);
```

이렇게 하면 CGI program이 표준 출력에 쓰는 모든 내용이 곧바로 클라이언트에게 간다.

### 8.4 CGI program이 헤더까지 책임지는 이유

부모 서버는 자식이 어떤 타입과 길이의 콘텐츠를 만들지 미리 모른다. 그래서 CGI 프로그램은 보통 본문뿐 아니라 적절한 헤더도 함께 생성해야 한다.

즉, 부모는 **연결과 실행 환경을 마련**하고, 자식은 **실제 콘텐츠와 그 의미를 생성**한다.

### 8.5 오늘날 CGI가 왜 덜 쓰이는가

요청마다 프로세스를 만드는 것은 비싸다. 그래서 실제 서비스는 FastCGI, Apache module, servlet, Rails controller, application server 같은 더 빠른 구조를 쓴다. 그래도 CGI를 배우는 이유는, 현대적인 프레임워크들이 감추는 핵심 원리—**프로세스 생성, 환경변수 전달, 표준 출력 리다이렉션**—를 가장 노출된 형태로 보여 주기 때문이다.

## 9. 손으로 읽는 HTTP 예시

```text
Client -> Server
GET /cgi-bin/adder?15000&213 HTTP/1.0




Server -> Client
HTTP/1.0 200 OK

Server: Tiny Web Server

Connection: close

Content-length: ...

Content-type: text/html



<html> ...
```

이 예시에서 꼭 눈여겨볼 것:

- request와 response 모두 **text line**으로 시작한다.
- blank line이 구조를 나눈다.
- body는 결국 **바이트열**이다.
- Tiny는 transaction이 끝나면 **connection을 닫는다**.

## 10. 생각거리

### 생각거리 1 | 자가점검

**Q1. URL을 클라이언트 관점과 서버 관점으로 나누어 보면 각각 무엇이 중요해지는가?**  
**해설.** 클라이언트는 prefix를 보고 어떤 프로토콜로, 어느 host와 port에 접속할지 결정한다. 서버는 suffix를 보고 어떤 자원을 찾아야 하는지, 정적인지 동적인지, 어떤 파일이나 프로그램과 연결해야 하는지를 결정한다. 같은 URL도 보는 위치에 따라 해석의 중심이 달라진다는 점이 중요하다.

**Q2. HTTP request와 response의 기본 형태를 말하고, 빈 줄이 무엇을 뜻하는지 설명해 보라.**  
**해설.** request는 request line, request headers, 빈 줄, 그리고 경우에 따라 body로 이루어지고, response는 response line, response headers, 빈 줄, body로 이루어진다. 빈 줄은 헤더가 끝났다는 신호다. 강의의 telnet 예시에서 빈 줄 하나가 없으면 서버가 요청을 아직 다 못 받았다고 생각할 수 있다는 점이 특히 잘 드러난다.

**Q3. 정적 콘텐츠, 동적 콘텐츠, `QUERY_STRING`은 각각 무엇을 뜻하는가?**  
**해설.** 정적 콘텐츠는 파일을 그대로 읽어 보내는 것이고, 동적 콘텐츠는 프로그램을 실행해 그 결과를 보내는 것이다. `QUERY_STRING`은 GET 요청에서 `?` 뒤에 붙은 인자 문자열을 CGI 프로그램에 넘기기 위한 환경변수다. 이 셋을 묶어 이해해야 URL, 서버 정책, CGI 실행이 하나의 흐름으로 이어진다.

### 생각거리 2 | 연결적 심화

**Q1. HTTP가 텍스트 기반 프로토콜인데도 이미지나 실행 결과 같은 바이너리 콘텐츠를 전달할 수 있는 이유는 무엇인가?**  
**해설.** 텍스트인 것은 메시지의 제어 구조이지, 본문 전체가 반드시 텍스트여야 한다는 뜻은 아니기 때문이다. 요청줄과 헤더는 사람이 읽을 수 있는 텍스트로 쓰이지만, body는 결국 아무 바이트열이나 될 수 있다. 서버는 `Content-Type`과 `Content-Length` 같은 헤더로 그 바이트열을 어떻게 해석해야 하는지 알려 준다.

**Q2. 왜 HTTP/1.1에서는 `Host` 헤더가 중요하며, 강의는 이를 telnet 예제에서 굳이 적어 넣을까?**  
**해설.** 같은 IP 주소와 포트 뒤에 여러 웹 사이트가 공존할 수 있기 때문에, 서버나 프록시는 이 요청이 실제로 어느 도메인을 향하는지 알아야 한다. `Host` 헤더는 바로 그 식별자 역할을 한다. 강의가 raw HTTP 예시에서 `Host: www.cmu.edu`를 강조하는 이유는, 브라우저가 자동으로 숨기는 중요한 규칙을 학습자가 직접 눈으로 보게 하기 위해서다.

**Q3. 왜 CGI에서는 부모 서버가 아닌 자식 프로그램이 `Content-Type`, `Content-Length`, 빈 줄까지 직접 출력해야 하는가?**  
**해설.** 부모는 자식이 실제로 어떤 콘텐츠를 얼마나 만들지 모른다. 반대로 CGI 프로그램은 자기 결과의 타입과 길이를 가장 정확히 알고 있다. 그래서 서버는 stdout을 연결 소켓으로 돌려 주고, CGI 자식이 그 위에 헤더와 본문을 직접 써 내려가게 한다. 교재와 강의가 이 점을 반복해서 짚는 이유는, 동적 콘텐츠가 “프로그램 출력이 곧 HTTP response의 일부가 되는 구조”임을 보여 주기 위해서다.

### 생각거리 3 | 확장과 전이

**Q1. CGI가 오늘날 주류 기술이 아니더라도 여전히 배울 가치가 큰 이유는 무엇인가?**  
**해설.** CGI는 웹 서버가 동적 콘텐츠를 생성하는 가장 노출된 형태를 보여 준다. 환경변수 전달, 표준 출력 리다이렉션, 자식 프로세스 실행이라는 기본 구조가 적나라하게 드러나기 때문이다. 현대 프레임워크는 이를 더 빠르고 편하게 감싼 것이지, 요청을 받아 코드를 실행하고 응답을 만드는 핵심 아이디어 자체가 사라진 것은 아니다.

**Q2. 왜 URL 경로를 운영체제 파일시스템 경로와 곧바로 동일시하면 위험한가?**  
**해설.** URL은 서버가 자기 정책에 따라 해석하는 가상 경로이지, 사용자가 서버 내부 디렉터리 구조를 직접 탐색하는 절대 통로가 아니다. 이 둘을 무심코 같다고 생각하면 잘못된 자원 매핑, 디렉터리 노출, path traversal 같은 보안 실수로 이어질 수 있다. `parse_uri`가 단순 문자열 처리 같아도 중요한 이유가 바로 여기에 있다.

**Q3. 왜 raw HTTP를 `telnet`이나 `curl`로 직접 보내 보는 경험이 학습에 큰 도움이 되는가?**  
**해설.** 브라우저가 가려 주는 구조를 직접 보게 되기 때문이다. 요청줄, 헤더, 빈 줄, 응답줄, 본문이 어디서 끊기는지 눈으로 확인하면 프로토콜 감각이 훨씬 선명해진다. 강의의 HTTP transaction 예시가 강력한 이유도, 웹이 거대한 프레임워크가 아니라 결국 줄과 바이트와 연결 위에서 움직인다는 사실을 체감하게 해 주기 때문이다.

---

# Helix 7. Tiny Web Server를 코드 수준에서 해부하기 (11.6, 11.7)

## 1. 이 바퀴의 질문

- Tiny는 11장의 어떤 개념들을 한 프로그램 안에 묶는가?
- `main`, `doit`, `parse_uri`, `serve_static`, `serve_dynamic`은 각각 무슨 역할을 하는가?
- Tiny의 한계는 무엇이며, 왜 이것이 12장으로 이어지는가?

## 2. Tiny를 보는 기본 관점

Tiny는 약 250줄짜리 작은 HTTP/1.0 iterative server다. 하지만 안에 들어 있는 재료는 작지 않다.

- sockets interface
- Rio
- HTTP
- file I/O
- memory mapping
- process control
- I/O redirection

즉, Tiny는 11장의 마지막 예제가 아니라 **11장 전체의 압축판**이다.

## 3. `main`: 서버의 뼈대 (11.6)

Tiny의 `main`은 서버 구조를 가장 투명하게 보여 준다.

```c
int main(int argc, char **argv) {
    int listenfd, connfd;
    socklen_t clientlen;
    struct sockaddr_storage clientaddr;
    char hostname[MAXLINE], port[MAXLINE];

    listenfd = Open_listenfd(argv[1]);
    while (1) {
        clientlen = sizeof(clientaddr);
        connfd = Accept(listenfd, (SA *)&clientaddr, &clientlen);
        Getnameinfo((SA *)&clientaddr, clientlen,
                    hostname, MAXLINE, port, MAXLINE, 0);
        doit(connfd);
        Close(connfd);
    }
}
```

여기서 `sockaddr_storage`가 등장하는 점도 의미 있다. 이것은 protocol-independent한 충분히 큰 주소 저장용 구조체다. 즉, Tiny는 인터페이스 차원에서 특정 주소 형식에 과도하게 묶이지 않으려 한다.

`main`이 우리에게 보여 주는 것은 단 하나다.

```text
open_listenfd -> accept loop -> per-connection transaction -> close
```

## 4. `doit`: 한 번의 HTTP transaction (11.6)

`doit`는 Tiny의 심장이다.

### 4.1 요청줄 읽기

```c
Rio_readinitb(&rio, fd);
Rio_readlineb(&rio, buf, MAXLINE);
sscanf(buf, "%s %s %s", method, uri, version);
```

Tiny는 request line에서 세 가지를 뽑는다.

- `method`
- `uri`
- `version`

그리고 `GET`만 지원한다. 다른 method면 `clienterror`로 501을 보낸다.

### 4.2 헤더 읽기

```c
read_requesthdrs(&rio);
```

Tiny는 대부분의 request header를 실제로 활용하지 않는다. 하지만 **어디까지 읽어야 다음 단계로 갈 수 있는가**는 중요하다. 그래서 blank line이 나올 때까지 읽는다.

### 4.3 URI 파싱과 분기

```c
is_static = parse_uri(uri, filename, cgiargs);
```

이 함수가 하는 일은 세 가지다.

1. URI를 파일 시스템 자원으로 번역한다.
2. 동적/정적 여부를 판단한다.
3. 필요하면 CGI 인자를 추출한다.

### 4.4 접근성 검사

```c
if (stat(filename, &sbuf) < 0) clienterror(...);
```

존재 여부, 읽기 권한, 실행 권한을 검사한다. 네트워크 서버도 결국 OS 위에서 파일과 프로그램을 다루는 프로세스라는 사실이 선명해진다.

### 4.5 실제 서비스

```c
if (is_static)
    serve_static(fd, filename, sbuf.st_size);
else
    serve_dynamic(fd, filename, cgiargs);
```

`doit`를 한 문장으로 요약하면 다음과 같다.

> **한 번의 request를 읽고, 어떤 자원에 대한 요청인지 결정해, 적절한 response를 만드는 함수**

## 5. `clienterror`: HTTP 오류도 결국 response다

입문자가 종종 놓치는 점은, 404나 501도 “그냥 출력문”이 아니라 **정상적인 HTTP response 구조**를 가져야 한다는 사실이다.

Tiny의 `clienterror`는 다음 원리를 보여 준다.

1. 오류 본문 HTML을 먼저 만든다.
2. 그 길이를 계산한다.
3. `HTTP/1.0 ...`, `Content-type`, `Content-length` 헤더를 붙인다.
4. 본문을 보낸다.

즉, 오류는 예외가 아니라 **정상적인 프로토콜 산출물**이다.

## 6. `read_requesthdrs`: “읽고 버린다”의 의미

Tiny는 request header 대부분을 활용하지 않는다. 그래도 읽어야 하는 이유는, request line 다음에 오는 header를 소비하지 않으면 이후 처리가 꼬이기 때문이다.

이 함수는 두 가지 학습 포인트를 준다.

- HTTP 메시지는 줄 단위로 구조화되어 있다.
- 프로토콜 파서는 “어떤 정보를 쓰는가”만큼 “어디까지 소비해야 하는가”도 중요하다.

## 7. `parse_uri`: URL 세계와 파일 세계를 잇는 번역기

Tiny는 단순한 규칙을 사용한다.

- URI에 `cgi-bin`이 있으면 동적
- 아니면 정적
- 정적이고 `/`로 끝나면 기본 파일 `home.html` 추가
- 동적이면 `?` 뒤를 `cgiargs`로 분리

이 함수는 문자열 처리처럼 보이지만 사실 서버 설계의 핵심이다. 왜냐하면 여기서 **외부 경로 체계(URL)**와 **내부 자원 체계(파일/실행 파일)**가 연결되기 때문이다.

## 8. `serve_static`: 파일을 HTTP response로 바꾸기

정적 콘텐츠는 다음 흐름이다.

```c
get_filetype(filename, filetype);
sprintf(buf, "HTTP/1.0 200 OK\r\n");
sprintf(buf, "%sServer: Tiny Web Server\r\n", buf);
sprintf(buf, "%sConnection: close\r\n", buf);
sprintf(buf, "%sContent-length: %d\r\n", buf, filesize);
sprintf(buf, "%sContent-type: %s\r\n\r\n", buf, filetype);
Rio_writen(fd, buf, strlen(buf));
```

그 다음 Tiny는 파일을 `mmap`으로 매핑하고 `rio_writen`으로 보낸다.

### 8.1 왜 `mmap`을 쓰는가

입문 단계에서는 이렇게 이해하면 충분하다.

- 파일을 메모리처럼 다룰 수 있게 해서
- 그 내용을 별도 사용자 버퍼 복사 없이 곧바로 전송하기 쉽다.

중요한 것은 세부 최적화보다, **9장의 memory mapping이 11장의 웹 서버에서 실제 쓰인다**는 사실이다.

## 9. `serve_dynamic`: CGI 실행 경로

동적 콘텐츠의 핵심은 다음 코드 골격이다.

```c
sprintf(buf, "HTTP/1.0 200 OK\r\n");
sprintf(buf, "%sServer: Tiny Web Server\r\n", buf);
Rio_writen(fd, buf, strlen(buf));

if (Fork() == 0) {
    setenv("QUERY_STRING", cgiargs, 1);
    Dup2(fd, STDOUT_FILENO);
    Execve(filename, emptylist, environ);
}
Wait(NULL);
```

여기서 반드시 이해해야 할 것:

- 부모는 연결을 들고 있다.
- 자식은 CGI program이 된다.
- 자식의 `stdout`을 `connfd`에 연결하면, CGI output이 곧 HTTP body가 된다.
- 부모는 자식을 수거한다.

### 9.1 왜 부모가 아니라 자식이 프로그램을 실행하는가

서버 본체는 다음 클라이언트를 위해 계속 살아 있어야 한다. 요청마다 별도 실행 맥락을 만들려면 자식 프로세스가 자연스럽다. CGI는 이 점을 아주 투명하게 보여 준다.

## 10. Tiny는 왜 실서버가 아닌가

Tiny는 교육용으로 훌륭하지만, 실서비스용으로는 부족하다.

- iterative server라 동시 처리 불가
- `GET`만 지원
- header 대부분 무시
- HTTP/1.0 close 모델
- 보안/권한/예외 처리 최소
- 클라이언트 조기 종료 시 `SIGPIPE`, `EPIPE` 고려 부족

하지만 이 한계들이 오히려 학습 가치를 높인다. 핵심 골격이 흐려지지 않기 때문이다.

## 11. Tiny를 통해 11장을 한 번 더 접기

```text
Tiny main
-> open_listenfd
-> Accept
-> doit
    -> request line 읽기
    -> method/uri/version 파싱
    -> headers 소비
    -> URI를 filename/cgivars로 분해
    -> stat
    -> serve_static or serve_dynamic
-> Close(connfd)
```

여기서 보이는 것은 결국 이것이다.

> **네트워크 서버는 연결을 받아, request를 프로토콜 형식으로 해석하고, OS 자원(파일·프로세스)을 조작해, 다시 response로 포장해 보내는 프로그램이다.**

이 문장이 11장의 결론이다.

## 12. 12장으로 넘어가는 문턱

Tiny를 다 읽고 나면 자연스럽게 다음 질문이 생긴다.

- 여러 클라이언트가 동시에 오면?
- 느린 클라이언트 하나가 전체를 막으면?
- CGI 자식이 오래 걸리면?
- 한 연결을 처리하는 동안 다른 연결은 누가 받지?

이 질문이 곧 12장의 질문이다. 그래서 11장은 동시성 장의 거대한 예고편이기도 하다.

## 13. 생각거리

### 생각거리 1 | 자가점검

**Q1. Tiny의 `main`이 보여 주는 서버의 기본 골격을 설명해 보라.**  
**해설.** Tiny는 포트를 받아 `Open_listenfd`로 리스닝 소켓을 열고, 무한 루프에서 `Accept`를 호출해 연결을 받고, `doit(connfd)`로 한 번의 HTTP transaction을 처리한 뒤 `Close(connfd)`로 연결을 닫는다. 구조만 보면 Echo 서버와 거의 같고, 서비스 내용만 HTTP와 파일/프로세스 조작으로 확장되었다. 즉, Tiny는 “서버의 뼈대는 단순한데, 그 안의 해석과 응답 생성이 풍부해진 예”라고 볼 수 있다.

**Q2. `doit`는 요청을 받아 어떤 순서로 한 번의 transaction을 처리하는가?**  
**해설.** 먼저 요청줄을 읽고 method, URI, version을 파싱한다. GET이 아니면 오류를 반환하고, 그렇지 않으면 헤더를 끝까지 읽고, URI를 해석해 정적/동적 콘텐츠를 판정한 뒤, 파일 속성을 확인하고, `serve_static` 또는 `serve_dynamic`으로 분기한다. 이 단계들을 말로 풀어낼 수 있으면 Tiny를 단순 암기가 아니라 흐름으로 이해한 것이다.

**Q3. `serve_static`과 `serve_dynamic`은 각각 어떤 방식으로 응답을 만든다?**  
**해설.** `serve_static`은 MIME 타입과 길이를 계산한 뒤 헤더를 보내고, 파일을 열어 `mmap`한 내용을 연결 소켓으로 쓴다. `serve_dynamic`은 기본 응답줄과 서버 헤더 일부를 보낸 뒤, 자식 프로세스를 만들어 CGI 프로그램을 실행하고 stdout을 소켓으로 리다이렉션한다. 둘은 모두 “HTTP response를 만든다”는 점은 같지만, 하나는 파일을, 다른 하나는 프로그램 실행 결과를 body로 삼는다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 `parse_uri`는 단순한 문자열 함수가 아니라 서버 정책과 보안 경계가 만나는 지점인가?**  
**해설.** `parse_uri`가 URL을 어떻게 파일명과 CGI 인자로 번역하느냐에 따라 정적/동적 판정, 기본 홈 페이지, 실행 파일 위치가 모두 결정된다. 즉, 이 함수는 URL 세계와 파일시스템 세계를 연결하는 규칙의 구현이다. 규칙이 단순하면 학습에는 좋지만, 현실에서는 바로 이 지점이 잘못되면 엉뚱한 파일 노출이나 잘못된 실행으로 이어질 수 있다.

**Q2. 왜 `serve_dynamic`에서는 부모가 헤더 일부만 쓰고, 나머지는 자식 CGI 프로그램이 마저 쓰는 구조가 자연스러운가?**  
**해설.** 부모는 “이 요청은 성공적으로 CGI 실행 경로로 들어갔다”는 최소한의 HTTP 틀만 보장할 수 있다. 하지만 실제 본문의 타입과 길이는 자식이 어떤 출력을 생성하느냐에 달려 있다. 그래서 강의와 교재는 child가 `Content-length`, `Content-type`, 빈 줄까지 직접 생성하도록 설계한다. 이것은 CGI의 불편함인 동시에, 누가 어떤 정보를 알고 있는지를 매우 정직하게 드러내는 구조다.

**Q3. Tiny가 쉽게 깨질 수 있는 이유는 무엇이며, 그 사실이 왜 교육적으로 중요한가?**  
**해설.** Tiny는 sequential server이고, 잘못된 HTTP 줄 종결(`
` vs `
`), 예기치 않은 메서드, 조기 종료된 연결, `SIGPIPE`, 충분하지 않은 검증 등 실전에서 필요한 방어를 거의 하지 않는다. 교재는 아예 “실서버가 쉽다고 오해하지 말라”고 경고한다. 이 한계를 보는 일은 실망스러운 부록이 아니라, 교육용 예제와 운영용 시스템 사이의 거리를 정확히 배우는 과정이다.

### 생각거리 3 | 확장과 전이

**Q1. Tiny를 concurrent server로 바꾸려면 가장 먼저 어디를 바꾸어야 할까?**  
**해설.** `accept` 뒤에서 `doit(connfd)`를 직접 호출하는 부분을 바꾸어야 한다. 즉, 한 연결의 처리를 프로세스, 스레드, 혹은 이벤트 루프로 넘겨 메인 루프가 다음 연결을 계속 받을 수 있게 만들어야 한다. `listenfd`와 `connfd`가 분리되어 있다는 사실이 바로 이 변형을 가능하게 하는 구조적 기반이다.

**Q2. Tiny는 왜 11장의 종착점이면서 동시에 CS:APP 여러 장이 다시 만나는 교차점인가?**  
**해설.** Tiny 안에는 8장의 process control, 9장의 `mmap`, 10장의 Rio와 파일 I/O, 11장의 sockets와 HTTP가 모두 들어 있다. 그래서 Tiny를 읽는 일은 네트워크 프로그래밍 하나만 복습하는 것이 아니라, 여러 장의 개념이 실제 프로그램 안에서 어떻게 결합되는지 보는 일이다. 이 통합성이 Tiny를 단순 예제가 아니라 “작은 시스템 프로그램”으로 만든다.

**Q3. 실전 서버를 Tiny보다 한 단계 더 진지하게 만들려면 무엇을 먼저 보강해야 할까?**  
**해설.** 최소한 robust parsing, 잘못된 요청 처리, 조기 종료된 연결 대응, `SIGPIPE`/`EPIPE` 처리, 더 나은 로그와 보안 경계가 필요하다. 그다음으로는 persistent connections, 더 많은 HTTP 메서드, 동시성, 더 엄격한 경로 검증이 따라온다. Tiny는 “본질을 보여 주는 최소 서버”이지 “바로 배포할 서버”가 아니라는 점을 끝까지 잊지 않는 것이 중요하다.

---

# Helix 8. 11장 전체 회수와 다음 장으로의 연결 (11.7, 12.1 preview)

## 1. 11장을 한 문장으로 요약하기

**네트워크 프로그램은 파일 디스크립터로 보이는 소켓을 통해, 클라이언트의 요청과 서버의 자원 조작을 reliable byte stream 위에 연결하는 프로그램이다.**

## 2. 11장에서 반드시 남겨야 할 8개의 문장

1. 모든 네트워크 애플리케이션은 클라이언트-서버 모델 위에 있다. (11.1)
2. 네트워크는 host에게 또 하나의 I/O 장치다. (11.2)
3. 인터넷은 이름, 주소, 포트, 연결을 통해 프로그래머에게 드러난다. (11.3)
4. 소켓은 연결의 종단점이자 프로그램에게는 열린 파일이다. (11.4)
5. 클라이언트는 `connect`로, 서버는 `bind`-`listen`-`accept`로 역할을 완성한다. (11.4)
6. `getaddrinfo`는 현대적인 네트워크 코드의 이름-주소 변환 중심이다. (11.4.7)
7. 웹 서버는 HTTP request를 정적 파일 읽기나 동적 프로그램 실행으로 번역한다. (11.5)
8. Tiny는 11장의 모든 재료를 한 프로그램에 묶어 보여 준다. (11.6)

## 3. 초심자가 자주 하는 오해 10가지

1. **소켓은 네트워크 전용 특별 객체다.**  
   아니다. 프로그램에게는 파일 디스크립터다.

2. **`socket()`만 하면 곧바로 통신할 수 있다.**  
   아니다. 클라이언트는 `connect`, 서버는 `bind`와 `listen`이 더 필요하다.

3. **서버 포트가 곧 연결의 identity다.**  
   아니다. 실제 connection identity는 socket pair다.

4. **한 번의 `write`는 상대의 한 번의 `read`와 대응한다.**  
   아니다. TCP는 byte stream을 제공한다.

5. **`accept`는 listenfd를 connected 상태로 바꾼다.**  
   아니다. 새 connfd를 만든다.

6. **HTTP는 브라우저 전용 마법 규칙이다.**  
   아니다. 줄과 헤더와 본문으로 된 텍스트 프로토콜이다.

7. **URL의 `/`는 운영체제 루트 디렉터리다.**  
   아니다. 서버가 정한 content root 기준이다.

8. **CGI는 웹 자체의 본질이다.**  
   아니다. 동적 콘텐츠 생성의 한 역사적 표준일 뿐이다.

9. **Tiny를 이해하면 실무 웹 서버도 거의 이해한 것이다.**  
   아니다. Tiny는 의도적으로 단순화된 교육용 서버다.

10. **11장은 네트워크 이론 장이다.**  
    아니다. 프로그래머 관점의 시스템 프로그래밍 장이다.

## 4. 스터디 운영용 권장 질문

다음 질문들을 서로에게 설명해 보라.

1. `host -> port -> socket address -> socket pair`를 예시와 함께 설명하라.
2. `listening descriptor`와 `connected descriptor`의 차이를 concurrency와 연결해 설명하라.
3. `rio_readlineb`가 Echo와 HTTP에서 왜 핵심인지 설명하라.
4. Tiny의 `doit` 흐름을 빈칸 없이 칠판에 그려 보라.
5. 정적 콘텐츠와 동적 콘텐츠를 파일 시스템/프로세스 관점에서 비교하라.
6. HTTP/1.0과 HTTP/1.1의 차이를 Tiny의 설계와 연결해 설명하라.
7. 왜 `getaddrinfo`가 linked list를 반환하는지 설명하라.
8. 왜 11장은 12장의 동시성 서버로 자연스럽게 이어지는지 설명하라.

## 5. 최종 생각거리

### 생각거리 1 | 종합 자가점검

**Q1. 브라우저가 URL을 알고 있는 상태에서 응답 본문을 화면에 받기까지를 8~10단계로 설명해 보라.**  
**해설.** 좋은 답변은 대체로 다음 고리를 포함해야 한다. 이름 해석, host:port 결정, client socket 생성, `connect`, server의 `bind`/`listen`/`accept`, request 전송, server의 요청 해석과 자원 처리, response 전송, 연결 종료다. 여기에 HTTP라면 URI 해석과 정적/동적 분기까지 들어가면 더 좋다. 11장을 제대로 이해했다는 것은 이 서사를 함수 이름과 개념 이름을 섞어 끊김 없이 말할 수 있다는 뜻이다.

**Q2. 다음 키워드를 한 문장 이상의 이야기로 엮어 보라: DNS, port, socket, connect, accept, Rio, HTTP, CGI.**  
**해설.** 단어를 나열하는 것이 아니라 관계를 말해야 한다. 예를 들면 “클라이언트는 DNS로 이름을 풀어 host를 찾고, port로 서비스 종단점을 정한 뒤 socket과 connect로 서버에 접근하며, 서버는 accept로 연결을 받고 Rio로 HTTP 요청을 읽어 CGI나 정적 파일 처리로 응답을 만든다”처럼 엮을 수 있어야 한다. 11장은 결국 이런 연결어를 만드는 장이다.

**Q3. 11장을 끝낸 시점에서 아직 남아 있는 가장 중요한 질문은 무엇인가?**  
**해설.** “한 번의 연결은 알겠는데, 여러 연결이 동시에 오면 어떻게 할 것인가?”라는 질문이 남아야 한다. 이 질문이 자연스럽게 떠오른다면 11장을 제대로 읽은 것이다. 왜냐하면 11장은 단일 연결의 문법을 가르쳤고, 12장은 그 문법을 동시성 위에 올리는 장이기 때문이다.

### 생각거리 2 | 연결적 심화

**Q1. 왜 11장은 10장의 I/O 장과 12장의 동시성 장 사이의 다리라고 할 수 있는가?**  
**해설.** 10장에서 배운 것은 “파일 디스크립터로 I/O를 다루는 법”이었고, 12장에서 배울 것은 “여러 흐름을 동시에 다루는 법”이다. 11장은 그 둘이 만나는 장소다. 소켓이 파일 디스크립터처럼 보인다는 점에서 10장과 이어지고, 한 서버가 여러 클라이언트를 처리해야 한다는 점에서 12장으로 열린다. 그래서 11장을 통과하면 I/O와 concurrency가 따로 떨어진 주제가 아니라는 사실이 보인다.

**Q2. 11장을 이해하는 세 가지 렌즈를 꼽는다면 무엇이며, 각각 무엇을 보여 주는가?**  
**해설.** 첫째는 자원/서비스 렌즈로, 서버가 무엇을 관리하고 무엇을 제공하는지 보여 준다. 둘째는 연결 생애주기 렌즈로, 주소 해석에서 `close`까지의 절차를 보여 준다. 셋째는 프로토콜 메시지 렌즈로, HTTP 같은 요청/응답 텍스트가 실제로 어떤 줄과 헤더와 본문으로 구성되는지 보여 준다. 한 렌즈만 보면 평면적이지만, 셋을 함께 쓰면 11장이 입체적으로 보인다.

**Q3. “TCP는 reliable byte stream이다”라는 문장을 정말 이해하면 Echo, HTTP, Tiny가 어떻게 다시 보이는가?**  
**해설.** Echo는 그 스트림 위에서 줄 단위 경계를 인위적으로 세운 예제로 보인다. HTTP는 같은 스트림 위에서 request line, headers, blank line, body라는 문법을 얹은 텍스트 프로토콜로 보인다. Tiny는 그 스트림을 파일 전송과 프로세스 실행 결과 전송으로 번역하는 서버로 보인다. 즉, 세 예제가 따로 있는 것이 아니라, 같은 바이트 스트림을 서로 다른 해상도에서 바라본 것임이 드러난다.

### 생각거리 3 | 확장과 전이

**Q1. 현대의 웹 프레임워크, API 서버, 프록시, 로드밸런서도 여전히 11장의 틀 안에서 볼 수 있을까?**  
**해설.** 볼 수 있다. 프레임워크가 라우팅, 헤더 파싱, 직렬화, 스레드 관리 같은 일을 많이 감싸 주더라도, 결국 연결을 받아 요청을 읽고 자원을 처리해 응답을 쓰는 구조는 그대로다. 프록시와 로드밸런서도 “앞단에서 연결을 받고, 뒤단과 새 연결을 열어, 메시지를 중계한다”는 점에서 11장의 사고방식을 확장한 형태로 이해할 수 있다.

**Q2. 새로운 텍스트 기반 네트워크 서비스를 직접 설계한다면 11장에서 무엇부터 꺼내 와야 할까?**  
**해설.** 먼저 클라이언트와 서버의 역할, 관리할 자원, 요청/응답의 문법을 정해야 한다. 그다음 host/port, 연결 수립 방식, 메시지 경계, 종료 조건, 오류 응답 형식을 정해야 한다. 이 순서는 우연이 아니라 11장 전체의 압축판이다. 소켓 함수는 그 뒤에 오는 구현 수단이지, 설계의 출발점이 아니다.

**Q3. 11장을 마친 뒤 어떤 순서로 복습하고 확장하는 것이 가장 효과적일까?**  
**해설.** 먼저 Echo와 Tiny를 다시 손으로 추적하며 연결 생애주기를 고정하는 것이 좋다. 그다음 12장의 process, thread, I/O multiplexing을 이어 읽어 “한 연결”을 “여러 연결”로 확장해야 한다. 여유가 있다면 프록시, persistent connection, 더 엄격한 HTTP 처리, `select`/`poll`/`epoll` 계열을 보며 11장의 틀이 실제 시스템으로 어떻게 자라는지 확인하면 된다.

---

# 부록 A. 함수 포켓 레퍼런스

| 함수 | 위치 | 한 줄 역할 | 기억할 포인트 |
|---|---|---|---|
| `socket` | (11.4.2) | 소켓 디스크립터 생성 | 아직 partially opened |
| `connect` | (11.4.3) | 클라이언트가 서버에 연결 | 성공해야 clientfd가 usable |
| `bind` | (11.4.4) | local address:port를 디스크립터에 결합 | 서버에서 중요 |
| `listen` | (11.4.5) | socket을 리스닝 상태로 전환 | backlog는 pending queue 힌트 |
| `accept` | (11.4.6) | 새 connection 수락 후 connfd 반환 | listenfd와 connfd 구분 중요 |
| `getaddrinfo` | (11.4.7) | host/service -> addrinfo list | modern, reentrant, protocol-independent |
| `getnameinfo` | (11.4.7) | socket address -> host/service 문자열 | 로그와 디버깅에 유용 |
| `open_clientfd` | (11.4.8) | 클라이언트 연결 helper | candidate list를 순회 |
| `open_listenfd` | (11.4.8) | 서버 listenfd helper | `SO_REUSEADDR` 포함 |
| `Rio_readinitb` | (10.5, 11.4.9) | descriptor를 버퍼드 Rio와 연결 | line I/O의 시작 |
| `Rio_readlineb` | (10.5, 11.4.9) | 줄 단위 읽기 | HTTP/Echo에 핵심 |
| `Rio_writen` | (10.5, 11.4.9) | 안정적 쓰기 | short count 대응 |
| `dup2` | (10.9, 11.5.4, 11.6) | descriptor redirection | CGI stdout 연결 |
| `fork` | (8.4, 11.5.4, 11.6) | 자식 프로세스 생성 | 동적 콘텐츠 생성 경로 |
| `execve` | (8.4, 11.5.4, 11.6) | 다른 프로그램 실행 | CGI 실행 핵심 |
| `mmap` | (9.8, 11.6) | 파일을 메모리에 매핑 | 정적 파일 전송 경로 |

---

# 부록 B. 11장을 위한 최소 코드 독해 체크리스트

다음 문장을 이해할 수 있으면 11장의 핵심 코드를 읽을 준비가 되어 있다.

- `connfd = Accept(listenfd, ...)`는 “다음 요청을 기다리는 소켓”과 “이번 클라이언트와 대화할 소켓”을 분리한다.
- `Rio_readlineb(&rio, buf, MAXLINE)`는 네트워크 바이트 스트림을 줄 단위 텍스트로 다루게 해 준다.
- `parse_uri`는 URL 세계를 파일/프로세스 세계로 번역한다.
- `Dup2(fd, STDOUT_FILENO)`는 CGI 프로그램의 표준 출력이 곧 client response가 되게 만든다.
- `mmap`은 정적 파일을 메모리처럼 다루게 해 네트워크로 보내기 쉽게 만든다.

---

# 부록 C. 한 장으로 접는 최종 도식

```text
(11.1) 서비스 모델
client -> request -> server resource -> response

(11.2) 네트워크 모델
host -> adapter -> LAN -> router -> internet -> packet -> encapsulation

(11.3) 식별 모델
name -> DNS -> IP -> port -> socket address -> socket pair

(11.4) 연결 모델
client: getaddrinfo -> socket -> connect
server: getaddrinfo -> socket -> bind -> listen -> accept

(11.4.9) 최소 동작 예제
Echo client/server

(11.5) 웹 모델
HTTP request/response
static file / dynamic CGI

(11.6) 종합 프로그램
Tiny main -> doit -> serve_static / serve_dynamic

(12장으로 연결)
iterative server의 한계 -> concurrent server
```

---

# 마지막 문장

11장을 제대로 이해했다는 것은 `socket`, `connect`, `accept`, `Rio`, `HTTP`, `CGI`, `Tiny`를 각각 외웠다는 뜻이 아니다. 그것은 **한 번의 연결이 태어나고, 요청이 해석되고, 운영체제 자원이 동원되고, 응답이 만들어지고, 연결이 닫히는 전체 흐름을 자기 말로 설명할 수 있다**는 뜻이다.
