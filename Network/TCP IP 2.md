## [www.google.com](http://www.google.com) 을 입력하면 일어나는 일

웹에서 사용되는 프로토콜을 사용하므로 각각 다음을 프로토콜을 사용하게 됩니다.

- 어플리케이션 계층 - HTTP / DNS
- 전송 계층 - TCP
- 인터넷 계층 - IP / ARP
- 네트워크 액세스 계층 - Ethernet

## 0. HTST 확인

HTTPS를 강제할 때 서버 측에서 `302` 로 응답하게 되면 취약점 포인트로 작용할 수 있습니다. 그래서 클라이언트에서 HTTP의 연결 가능여부를 확인할 수 있도록합니다. 그래서 HTTP로 접속이 가능한지 여부를 확인하여 불가능하다면 HTTPS로 연결을 시도하게 됩니다.

## 1. DNS 서버 조회

이 때 도메인 네임으로 작성된 `[www.google.com](http://www.google.com)` 의 실제 ip 주소를 알기 위해 캐시를 살펴보게 됩니다. 4가지의 캐시를 순차적으로 탐색합니다.

- 캐시 탐색 순서
    - 브라우저 캐시 → OS 캐시 → 라우터 캐시 → ISP 캐시(인터넷 공급자, ex) SK, KT, U+ ..)

캐시에 저장된 도메인 정보가 없다면 DNS서버에 요청 쿼리를 보내어 실제 주소를 확인합니다. DNS 서버에서 `.` 을 시작으로 재귀적으로 탐색하며 `[www.google.com](http://www.google.com)` 와 일치하는 주소를 탐색하여 ip를 반환합니다.

- 재귀적 탐색 순서
    - `.` → `.com.` → `[google.com.](http://google.com)` → `www.google.com.`

실제 요청을 보낼 ip 주소를 알게되었으니 요청에 대한 패킷을 생성합니다.

## 2. (http://)www.google.com(:80) 요청 패킷 생성

브라우저로 `[www.google.com](http://www.google.com)` 을 입력합니다. 일반적으로 `.com` 의 접미사가 포함되어 있으면 브라우저는 검색이 아닌 URL을 입력했다고 판단하며 URI 스킴이 존재하지 않는다고 하면 기본적으로 `http://` 의 접두사를 붙입니다. 메서드가 지정되지 않았으므로 `GET` 의 HTTP 요청을 보내게 됩니다.

이렇게 HTTP REQUEST를 어플리케이션 레이어 요청으로 패킷을 담게 되면 전송 계층으로 패킷을 보내게 됩니다.

## 3. TCP 헤더 생성

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7094c7a1-e2db-4d36-a40b-fe610e624cfe/Untitled.png)

### 헤더에 포트번호 추가

어플리케이션 계층에서 작성된 패킷에 TCP 헤더를 추가합니다. 송신측 포트와 수신측 포트의 정보를 기록합니다. TCP의 웰 노운 포트에서 HTTP는 80번 포트이므로 수신측 포트를 80번으로 설정하게 됩니다.

**송신측(클라이언트) 포트는 80포트가 아니며 운영체제에 따라 다른 포트를 선택하여 요청합니다.**

> [https://stackoverflow.com/questions/2957757/how-can-an-application-use-port-80-http-without-conflicting-with-browsers](https://stackoverflow.com/questions/2957757/how-can-an-application-use-port-80-http-without-conflicting-with-browsers)
> 

### TCP 커넥션 생성을 위한 컨트롤 비트 추가

TCP에서는 신뢰성 있는 데이터 전송을 위해서 논리적인 연결(세션)을 생성합니다. 이를 위해서 3-Way Handshake 및 4-Way Handshake를 진행합니다.

### 3-Way Handshake

장치 간 논리적인 연결을 위하여 세션을 수립합니다. 3-Way Handshake는 세션을 시작하는 과정입니다. 이 과정을 통해서 서버 및 클라이언트가 모두 데이터를 송수신할 준비가 되었음을 확인합니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0d393ed1-c950-40b1-b49f-0840f8d0590e/Untitled.png)

TCP 헤더에 컨트롤 비트를 담아서 송수신하게 되며 그 과정은 다음과 같습니다.

1. 클라이언트에서 서버에 접속 요청 SYN비트를 전송한 후 SYN-SENT 상태가 됩니다.
2. 서버에서 SYN 요청을 받고 수락을 위해 SYN + ACK 비트를 보냅니다. 이후 SYN-RECVED 상태가 됩니다.
3. 클라이언트에서 ACK 비트를 보낸후 ESTABLESHED 상태가 됩니다. 서버도 ACK비트를 수신한 후 ESTABLESHED 상태가 됩니다.

SYN비트에서 각각 서버와 클라이언트의 순차 일련번호를 교환하여 통신할 수 있도록 합니다.

### 4-Way Handshake

세션을 종료하기 위해서는 4-Way Handshake를 사용합니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/766cc13a-486f-4a79-9783-bbf7740f41e3/Untitled.png)

1. 클라이언트가 연결 종료를 말하는 FIN 비트를 송신합니다. 이후 FIN-WAIT 상태가 됩니다.
2. FIN 비트를 받은 서버에서는 FIN 비트를 받았다는 ACK 비트를 전송합니다. 이후 CLOSE-WAIT 상태가 됩니다.
3. 연결을 종료한 후 서버는 FIN 비트를 클라이언트에게 전송합니다. 서버는 LAST-ACK 상태가 됩니다.
4. FIN 비트를 수신한 클라이언트는 확인을 알리는 ACK를 전송하고 일정시간동안 TIME-WAIT상태로 대기합니다.
5. ACK 비트를 수신한 서버는 세션을 종료하고 CLOSE 상태가 됩니다.

- 왜 세션 종료에는 4-Way Handshake 과정이 필요할까?
    - TCP는 신뢰성있는 통신 과정입니다. 클라이언트가 FIN 요청을 보내더라도 서버에서는 아직 송신할 데이터가 남아있을 수 있습니다. 그래서 FIN을 수신했다는 ACK 비트를 먼저 송신한 후 작업이 완전히 완료된 이후에 FIN 비트를 송신해야합니다.
- 클라이언트는 왜 FIN 이후 TIME-WAIT 상태로 대기할까?
    - FIN 비트가 도착했더라도, 서버에서 보낸 패킷이 아직 도착하지 않거나 유실되었을 수 있습니다. 잉여 데이터가 남아있을수도 있기 때문에 일정 시간동안 세션을 유지하는 것입니다.
    

## 서버 라우팅

인터넷 계층에서 요청을 보낸 IP와 DNS 서버를 통해 알아낸 목적지 IP를 IP 헤더에 담습니다. 요청 IP는 NAT을 통해 공인 IP로 변경되며 인터넷에 연결된 라우터들을 통해서 구글 서버로 전달되게 됩니다.

### ARP

IP 헤더에 담긴 주소로 도착했다면, ARP를 이용해 실제 서버 단말의 MAC 주소를 알아냅니다. 알아낸 MAC 주소를 통해 물리적으로 데이터를 전송합니다.

## 서버 응답 생성

구글 서버는 패킷에 기록된 80 포트와 기본 메서드 GET 요청을 받습니다. 구글 서버는 HTTP 요청에 맞는 응답을 하기 위해 요청 컨트롤러에서 리스폰스를 생성합니다. 리스폰스에는 HTTP에 필요한 인코딩 방식, 상태 코드 등이 담겨 HTML과 같은 형태로 다시 클라이언트에게 전송됩니다.

## 브라우저 렌더링

브라우저는 HTML 컨텐츠를 렌더링하여 사용자에게 보여줍니다. 이 때 추가적으로 필요한 데이터들을 다시 요청하여 렌더링을 끝냅니다.

> 참조 
[https://www.youtube.com/watch?v=BEK354TRgZ8&t=200s](https://www.youtube.com/watch?v=BEK354TRgZ8&t=200s) 
[https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a](https://medium.com/@maneesha.wijesinghe1/what-happens-when-you-type-an-url-in-the-browser-and-press-enter-bb0aa2449c1a)
>
