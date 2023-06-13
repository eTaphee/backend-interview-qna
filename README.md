# backend-interview-qna

## CS(Computer Science)
### 네트워크(Network)
<details>
<summary>웹 통신의 큰 흐름 ex) wwww.domain.com 접속 흐름</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
웹 브라우저가 www.domain.com에 대해 HTTP Request 메시지에 대한 패킷을 만들어 OS에 전달합니다.

패킷 생성 시 헤더에 목적지 IP주소가 필요한데, DNS를 통해서 입력한 도메인에 대응하는 IP주소를 가지고 와서 사용합니다.

패킷이 생성되면 랜카드를 통해서 전기적 신호로 변환됩니다.

패킷은 공유기, 인터넷 모뎀, ISP를 지나서 인터넷에 있는 여러 라우터들에 의해 목적지(웹 서버) IP 주소를 찾아갑니다.

목적지에 도착한 패킷은 방화벽에서 검사 후 웹 서버에 전달됩니다.

웹 서버에서는 패킷이 자신에 대한 요청이 맞는지 확인하고 웹 서버 앱으로 전달합니다.

웹 서버앱에서는 패킷으로 부터 HTTP Request 메시지를 만들어 요청을 확인한 다음 처리하고 HTTP Response 메시지를 만들어 클라이언트에 전달합니다.

- HTTP 프로토콜은 TCP 기반의 프로토콜이기 때문에, 최초 통신 시 3 way handshake를 통해 연결을 생성하고 HTTP 요청/응답을 주고 받습니다.
- 데이터가 전송될 때 네트워크의 여러 라우터를 거치게 됩니다.    
이 때 사전에 라우팅 프로토콜에 의해 작성된 라우팅 테이블에 있는 정보로 최적화된 경로로 데이터를 전송합니다.(라우터에 의해)
- IP 계층에서 데이터는 패킷(Packet)으로 부릅니다.
- Data Link 계층에서 데이터는 프레임(Frame)으로 부릅니다.
- 패킷이 전송 될 때, 출발지/도착지의 주소정보를 가르키는 IP가 패킷 헤더에 포함이 되는데, 이는 도착 시 까지 변하지 않습니다.
- 반면 MAC 주소는 네트워크 인터페이스를 거쳐 갈 때마다 변경됩니다.
- 네트워크 인터페이스를 거쳐갈 때마다 다음 MAC주소를 결정하기 위해서 LAN 환경에서는 ARP, WAN 환경에서는 Proxy ARP를 사용합니다.
- 패킷이 최종 목적지에 도달하게 되면, 공유기(?) 등의 장비에 설정된 포트포워딩을 통해 로컬 장비(프로세스/어플리케이션)에 패킷을 전달합니다.
</details>

<details>
<summary>TCP/UDP 차이</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
TCP는 연결지향 프로토콜입니다. 가상회선을 만들어 신뢰성/도착 순서를 보장하는 전송을 합니다. 
UDP는 비연결형 프로토콜입니다. TCP와 달리 신뢰성을 보장하는 전송을 하지 않는 대신 속도가 빠릅니다.

TCP는 오류검사 기능을 통해 패킷이 문제가 있는 경우 재전송 요청할 수 있습니다.
UDP도 오류검사를 하지만 무결성을 위한 오류검사만 할 뿐 재전송 요청을 하지 않습니다.

- TCP의 데이터 단위는 세그먼트(segment)으로 부릅니다.
- UDP의 데이터 단위는 데이터그램(datagram)으로 부릅니다.
</details>

<details>
<summary>TCP 3/4 Way Handshake</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
3 Way Handshake는 TCP 통신 시작 시 가상회선(세션)을 연결하는 과정이고,
4 Way Handshake는 TCP 통신 종료 시 가상회선(세션)을 해제하는 과정입니다.

회선을 수립하기 위해 먼저 서버는 특정 포트에 대해 listening 상태여야 합니다.

----

1. 먼저 클라이언트가 임의의 수(M)를 담고 있는 SYN(M) 패킷을 서버로 송신합니다.
2. 서버는 SYN(M)패킷을 수신하고 SYN(N) + ACK(M+1) 패킷을 클라이언트로 송신합니다.
3. 클라이언트는 SYNC(N) 패킷을 수신하고 ACK(N+1) 패킷을 서버로 송신합니다.

----

1. 연결 종료를 위해 클라이언트가 FIN 패킷을 서버로 송신합니다.
2. 서버는 FIN 패킷을 수신하고 ACK 패킷을 클라이언트로 송신합니다. => 클라이언트는 ACK 패킷을 수신하고, 서버의 FIN 패킷 수신을 대기합니다.
3. 서버는 FIN 패킷을 클라이언트로 송신합니다.
4. 클라이언트는 FIN 패킷을 수신하고 ACK 패킷을 서버로 송신합니다.

----

- 3 Way 연결 상태는 다음과 같이 변한다. LISTEN(S) -> SYNC_SENT(C) -> SYN_RECEIVED(S) -> ESTABLISHED(C) -> ESTABLISHED(S)
- 4 Way 연결 상태는 다음과 같이 변한다. FIN_WAIT1(C) -> CLOSE_WAIT(S) -> FIN_WAIT2(C) -> LAST_ACK(S) -> TIME_WAIT(C) -> CLOSED(S) -> CLOSED(C)
- 4 Way의 TIME_WAIT 상태는 서버의 FIN 패킷을 수신한 클라이언트는 잉여 패킷을 위해 일정 시간을 대기하는 상태를 말합니다.
</details>

<details>
<summary>HTTP/HTTPS 차이</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
HTTP는 보안 연결을 통해 암호화 과정이 없이 패킷이 송/수신 되므로 패킷 캡처를 통해 데이터를 감청 할 수 있습니다.

반면 HTTPS는 SSL/TLS 보안 연결 프로토콜을 이용해 교환된 대칭키로 패킷을 암호화하여 송/수신 하므로 데이터를 감청 할 수 없습니다.

- HTTP는 기본 포트 80, HTTPS는 기본 포트 443을 사용합니다.
- 패킷 캡처 프로그램에 와이어 샤크가 있습니다.
- SSL/TLS는 데이터의 기밀성, 무결성을 보장하는 보안 프로토콜
- SSL의 취약점을 보완한 것이 TLS
- SSL은 TLS의 호환성을 위해 사용
- TLS 사용 권장
- HTTPS 강제 사용을 위해 HSTS(HTTP Strict Transper Security) 와 HttpsRedirect 기능을 함께 사용
</details>

<details>
<summary>SSL/TLS Handshake</summary>

클라이언트와 서버가 통신 시 암호화 된 데이터를 송/수신 하기 위한 협상하는 과정입니다.

인증서 전달, 암호화에 사용할 대칭키 전달, 암호화 알고리즘을 결정합니다.

TCP 3 Way Handshake 과정이 완료 된 후 진행합니다.

1. 보안 연결을 위해 클라이언트가 Client Hello 패킷을 서버에 송신합니다. 송신 시 브라우저가 지원하는 암호화 알고리즘 목록을 포함하여 송신합니다.
2. 서버는 사용할 암호화 알고리즘을 선택해서 Server Hello 패킷을 클라이언트에 송신합니다.
3. 서버는 공개키가 포함된 인증서를 클라이언트에 송신합니다. 인증서 내부에 공개키가 없는 경우엔 서버가 Server Key Exchange 패킷을 통해 직접 전달합니다. 마지막으로 Server Hello Done 패킷을 송신합니다.
4. 클라이언트는 서버로부터 인증서를 수신 후 공개된 CA(인증 기관)의 공개키로 복호화하여 인증서가 유효한지 검증합니다.
5. 검증 완료 후 클라이언트는 데이터 암호화에 사용할 대칭키(비밀키)를 생성 후 인증서의 공개키(또는 서버의 공개키)를 이용해 암호화하여 서버에 전달합니다. Client Key Exchange 패킷입니다.
6. Change Cipher Spec, Finished 패킷을 송신하여 Handshake를 종료합니다.

- 대칭키(비밀키)로 같은 키를 가지고 암호화/복호화 하므로 비대칭키에 비해 빠르게 동작합니다.
- 비대칭(공개키, 비밀키 쌍) 암호화를 이용하여 비밀키를 교환합니다.
- 비대칭/대칭을 혼합해서 사용합니다.
</details>

<details>
<summary>RESTful</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
먼저 REST는 Representational State Transfer의 약자로, 웹에서 데이터를 전송하고 처리하는 방법을 정의한 인터페이스를 말합니다.

모든 데이터 구조와 처리 방식은 URL을 통해 정의됩니다.

URI를 통해 자원을 명시하고, Method (GET, POST, DELETE, PATCH, PUT)을 통해 자원에 대한 행위를 정의합니다.
URI를 통해 자원을 접근하고, Method를 사용하여 자원의 상태를 조회하거나 상태를 변경합니다.

장/단점 필요할거 같다.
</details>

<details>
<summary>HTTP Method</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
- OPTIONS: 해당 URI에 대한 허용하는 메서드를 확인할 때 사용
- GET: 자원 정보 조회 (R)
- POST: 자원 생성 (C)
- PUT: 자원을 생성하거나 수정 (C, U)
- PATCH: 자원 정보를 수정 (U)
- DELETE: 자원 삭제 (D)

</details>

<details>
<summary>HTTP 상태코드</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
- 1XX (정보): 요청을 받았으며, 요청이 진행중임을 나타냅니다.
- 2XX (성공): 요청에 대한 성공을 나타냅니다.
- 3XX (리다이렉션): 요청 완료를 위해 추가 조치가 필요합니다.
- 4XX (클라이언트 오류): 클라이언트 요청의 문제로 요청을 처리할 수 없습니다.
- 5XX (서버 오류): 요청은 유효하나, 서버의 문제로 요청을 처리할 수 없습니다.

개발 시 2XX, 4XX, 5XX를 제외하고는 많이 접하고 있는거 같지 않습니다.
- 200 (OK): 일반적인 성공 응답(데이터 조회, 생성, 수정, 삭제 등..)
- 201 (Created): 자원이 생성되었을 때 성공 응답(POST, PUT), 추가된 자원 정보를 응답하기도 합니다.
- 204 (NotContent): 요청이 성공 했으나, 반환해줄 값이 없는 경우(DELETE?)
- 400 (BadRequest): 요청값이 잘못된 경우(일반 적인 경우 입력 매개변수에 대한 오입력, 유효성 검사)
- 401 (Unauthoried): 요청에 필요한 인증을 받지 못한 경우(authenciated)
- 403 (Forbidden): 요청 리소스에 대한 접근 권한이 없는 경우(authorized, scope)
- 404 (NotFound): 요청한 자원이 존재하지 않는 경우
- 500 (InternalServer): 요청 처리 중 서버에서 예외가 발생한 경우
</details>

<details>
<summary>CORS</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
교차 출처 리소스 공유, Cross Origin Resource Sharing

웹 개발 시, 현재의 오리진(도메인)과 다른 출처에서 API를 조회해 올 때 발생하는 문제로, 출처가 다른 응답에 대해 브라우저에서 차단합니다.
일반적으로 웹 브라우저는 보안상 이유로 동일한 오리진(프로토콜, 도메인, 포트)에 대해서만 리소스를 공유할 수 있도록 제한되어 있습니다.

예를 들어 localhost 웹 앱에서 localhost가 아닌 서버에서 api를 호출할 때 확인할 수 있습니다.

서버측에서 allow orgin 옵션을 통해 Access-Control-Allow-Origin 응답 헤더를 통해 cors 이슈를 해결할 수 있습니다.

추가적으로 외부 API 서비스와 연동 할 때 콘솔에서 Allow Origin 설정하는 부분이 있는것을 확인할 수 있습니다.

</details>

<details>
<summary>OSI 7 Layer & TCP/IP 4 Layer</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
통신이 일어나는 과정을 단계별로 파악이 가능합니다.
사실상 OSI 7 Layer는 표준일 뿐이고 실제는 TCP/IP 4 Layer가 통신이 일어나는 과정을 잘 표현하는 모델입니다.

각 레이어 설명은 검색하면 많이 나오니 패스..

</details>

### 운영체제(Operating System)

<details>
<summary>프로세스와 스레드</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
프로세스와 스레드는 컴퓨터에서 동작하는 실행 단위를 나타내는 용어입니다.

프로세스는 운영체제에서 실행하고 있는 프로그램을 말하며, 각각의 프로세스는 독립적으로 메모리를 할당 받고 운영체제로부터 자원을 할당 받아 실행됩니다.
프로세스는 최소 하나의 메인 스레드를 가지고 있습니다. 각 프로세스는 독립적으로 실행되기 때문에 서로의 자원에 직접적인 접근이 불가능합니다.
프로세스 간 통신(IPC)를 통해 프로세스 간 데이터 교환이 가능합니다.

스레드는 프로세스내에서 실행되는 작업 단위입니다.
하나의 프로세스는 여러 개의 스레드를 가지고 있으며, 프로세스 내의 메모리를 공유하며 실행됩니다.
스레드끼리는 프로세스내의 자원을 공유할 수 있습니다.
프로세스의 성능을 향상 시키기 위해 여러 스레드가 사용될 수 있으며, 사용시 동기화에 주의를 기울이며 사용해야 합니다.

- C#에서 Pipeline를 통해 구현이 가능합니다.
- Shared Memory를 통해서 프로세스간 데이터 공유 가능
</details>

<details>
<summary>컨텍스트 스위칭</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
실제로 멀티 스레드 환경에서 개별 스레드가 동시에 작업하는 것처럼 보이는 것은 컨텍스트 스위칭 떄문입니다.
컨텍스트 스위칭은 운영체제가 CPU의 실행 상태를 하나의 스레드에서 다른 스레드로 전환하는 과정입니다.
이를 통해 다수의 스레드가 동시에 실행되는 것처럼 보입니다.

CPU의 하나의 코어에서는 하나의 스레드만 실행할 수 있습니다. 컨텍스트 스위칭을 통해 스레드간 실행시간을 분할하여 동시에 실행되는 것처럼 보입니다.
현재 실행 중인 스레드의 상태를 저장하고, 다음 실행할 스레드의 상태를 복원하는 작업을 수행합니다.

</details>

<details>
<summary>동기, 비동기(블로킹, 논블로킹)</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->


</details>

<details>
<summary>멀티스레드</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
프로세스와 스레드 참고

</details>

<details>
<summary>프로세스 동기화</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
어렵네..

</details>

<details>
<summary>스레드 동기화(Thread-Safe)</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
공유된 자원에 대한 동시 접근을 막고, 스레드들이 순처적 또는 제한적으로 접근하는 것을 스레드 동기화라고 합니다.
스레드 동기화된 클래스나/매서드를 보고 Thread-Safe하다라고 표현합니다.

뮤택스(Mutex)
lock, monitor와 같이 임계 영역을 제어합니다.
차이점은 monitor는 하나의 프로세스 내에서 사용가능한 반면, 뮤택스는 해당 머신의 프로세스간 lock를  지정할 수 있습니다.
대신 monitor보다 느립니다.

세마포어(Semaphore)
세마포어는 공유된 자원에 접근 가능한 스레드의 수를 제어합니다.

- C#에 스레드 동기화를 위한 Concurrent 자료구조가 있습니다.
- 한번에 하나의 스레드만 접근 가능한 영역(임계 영역, Critical Section) lock 블록을 통해 구현할 수 있습니다. 가능한 범위는 작게 하는 것이 좋습니다.
- lock 블록과 같이 임계 영역을 제어하는 Monitor 클래스가 있습니다.
- 기타 동기화를 위해 AutoResetEvent와 ManualResetEvent 클래스가 있습니다.

</details>

### 객체지향

<details>
<summary>OOP</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
문제 해결 대상을 추상화시켜 상태, 행위를 가진 객체로 만들고,
객체간의 상호작용을 통해 로직을 구현하는 프로그래밍 방법론입니다.

</details>

<details>
<summary>객체지향의 특징</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
- 상속: 클래스간 계층구조를 형성하여 코드의 재사용성과 확장성을 높임
- 추상화: 불필요한 부분을 제거, 공통성질을 일반화 하는 것
- 다형성: 오버라이딩(부모 클래스의 기능을 새로 재정의), 오버로딩(같은 이름의 매서드를 매개변수를 다르게 해서 정의)
- 캡슐화: 접근제어자를 통한 내부 구현을 숨기고 공개된 방식으로 상호작용 하도록 함

</details>

<details>
<summary>SOLID</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
- SRP(단일 책임 원칙): 클래스는 단 하나의 책임을 져야 합니다. 여러 가지 기능을 수행하는 경우 수정이 어려워지기 때문입니다. 이를 통해 응집도를 높이고 결합도를 낮춥니다.
- OCP(개방-폐쇄 원칙): 확장에는 열려 있고, 변경에는 닫혀있어야 합니다. 기존의 코드를 변경하지 않고 기능을 추가할 수 있어야합니다. 인터페이스/추상클래스를 사용합니다.
- LSP(리코스프 치환 원칙): 서브 타입은 언제나 기반 타입으로 대체될 수 있어야합니다. 다형성을 지원하기 위한 원칙입니다.
- ISP(인터페이스 분리 원칙): 자신이 사용하지 않는 인터페이스에 의존하면 안됩니다. 최소한의 필요성에 초점을 맞춰 분리해야합니다.
- DIP(의존성 역전 원칙): 상속받은 구현에 의존하는 것이 아닌 추상화 그 잡채에 의존해야합니다. 결합도를 낮추고 유연성을 향상시킵니다. 테스트 용이.

</details>

<details>
<summary>인터페이스와 추상클래스</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
다형성과 추성화를 구현하는데 사용되는 개념입니다.

추상 클래스는 상속을 통해 확장되고, 인터페이스는 구현을 통해 사용할 수 있습니다.
추상 클래스는 상속을 통해 코드의 재사용성을 높이고, 클래스간 공통 기능을 제공합니다.
인터페이스는 클래스의 동작/규약을 정의하고, 클래스간 결합도를 낮춥니다.

- 클래스는 단일 상속, 인터페이스는 다중 상속이 가능합니다.
- 기본적인 개념은 생략합니다.

</details>

<details>
<summary>DI, IoC</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
- DI: 객체가 다른 객체를 사용할 때, 결합도를 낮추기 위해 의존성을 외부에서 주입받는 것을 말합니다.
- IoC: 프로그램 제어 흐름에 대한 권한을 프레임워크나 컨테이너에 넘기는 개념입니다. 객체 생성, 의존성 해결등 개발자가 직접 처리하는게 아닌 IoC가 담당합니다.

</details>

### 데이터베이스(Database)

<details>
<summary>ORM</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
?? 카테고리가 이곳이 맞을까?

객체와 RDB 사이의 상호작용을 단순화하는 기술(?) 입니다.

1. 생상성 향상: SQL 쿼리를 직접 작성하는 노력을 줄일 수 있습니다. 객체지향 방식으로 데이터를 조작할 수 있습니다. 개발자들이 더 빠르게 개발 가능합니다.
2. 데이터베이스 독립성: 벤더에 종속적이지 않습니다. 데이터베이스를 변경하더라도 최소한의 변경으로 수정 가능합니다.
3. 객체지향적 접근: RDB의 테이블을 객체로 추상화하여, 객체지향 프로그래밍 원칙에 맞는 코드 작성을 용이하게 합니다.(1번과 중복)
4. 유지보수 용이성: 스키마 변경 시, 매핑 코드를 업데이트할 수 있어 수정작업이 편리합니다.

</details>

<details>
<summary>ACID</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
트랜잭션의 안정성을 보장하는 성질입니다.

1. Atomic(원자성): 트랜잭션의 모든 작업은 모두 실행/모두 실행되지 않습니다. 오류 발생 시 롤백.
2. Consistency(일관성): 트랜잭션 전/후, 데이터베이스의 상태는 변하지 않습니다. 제약이나 규칙 만족해야합니다.
3. Isolation(독립성): 동시에 실행되는 트랜잭션은 서로 영향을 주지않고 독립적으로 실행됩니다. 하나의 트랜잭션이 실행될 때, 다른 트랜잭션의 변경 사항이 보이지 않습니다.
4. Durability(지속성): 트랜잭션 커밋 이 후, 시스템 장애가 있더라도 커밋된 상태가 유지되는 것을 보장합니다.

</details>

<details>
<summary>Transaction</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
여러개의 작업을 하나로 묶은 실행 단위입니다.
데이터베이스의 상태를 변환하는 기능을 수행하는 작업을 모아놓은 하나의 작업 단위.

</details>


<details>
<summary>Index</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
데이터 검색 및 쿼리 성능을 향상시키기 위해 사용합니다.

검색 성능을 향상 시키나, 
추가/수정/삭제 시 인덱스를 업데이트 하기 위한 비용 발생 및 성능 저하 될 수 있습니다.(+ 저장공간 차지)

</details>

<details>
<summary>RDBMS, NoSQL</summary>

<!--summary 아래 빈칸 공백 두고 내용을 적는공간-->
RDBMS는 객체들의 관계를 구조화하여 데이터를 저장하는 데이터베이스입니다. SQL을 사용하여 데이터를 조회/추가/수정/삭제 할 수 있습니다.
명확한 데이터 구조 보장 및 중복을 피할 수 있습니다.

NOSQL은 RDBMS에 비해 자유로운 형태로 데이터를 저장할 수 있습니다.

</details>

### 자료구조/알고리즘(Data Structure & Algorithm)

### .NET

### 가비지 컬렉터(GC, Garbage Collector)
APM, 장애대응
