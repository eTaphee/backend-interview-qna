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

### 데이터베이스(Database)

### 자료구조/알고리즘(Data Structure & Algorithm)

### .NET


### 가비지 컬렉터(GC, Garbage Collector)
APM, 장애대응
