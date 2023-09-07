# HTTP, HTTPS, HTTP Status

## HTTP란?

---

- HTTP(HyperText Transfer Protocol)
- 인터넷 상에서 클라이언트와 서버가 자원을 주고 받을 때 쓰는 통신 규약

### 무상태성(stateless)

서버가 클라이언트의 상태를 보존하지 않는다. 매번의 요청마다 클라이언트가 필요한 상태에 대한 정보를 가지고 온다. 서버의 확장성이 높아진다는 장점이 있지만 주고받는 데이터의 양이 많아진다는 단점이 있다.

### 비연결성(connectionless)

클라이언트가 서버에 요청을 하고 응답을 받으면 바로 TCP/IP 연결을 끊는다. 그 덕분에 서버의 자원을 효율적으로 관리할 수 있다.

### 지속 연결(persistent connection)

HTTP/1.1부터는 매번의 요청마다 TCP/IP 연결을 끊는 것이 아니라 일정 시간동안 요청이 없을 경우 요청을 끊는 persistent connection이 기본으로 지원됨. 모든 리소스마다 매번 새로운 연결을 할 경우 서버의 부하가 너무 높기 때문.

## HTTP Methods

---

### GET

특정한 리소스를 가져오도록 요청함. request body를 담는 것이 금지돼 있지는 않지만 일반적으로 담지 않으며, 쿼리스트링을 통해서 필요한 정보를 요청하는 것이 일반적이다. 기존에는 body를 담는 것이 금지돼 있던 것이 표준이었기 때문에 body를 담을 경우 기존의 구현체에서는 요청이 거부당할 위험이 있다.

### POST

요청 데이터를 처리. 주로 데이터 등록에 사용함.

### PUT/PATCH

PUT은 대상 리소스가 없다면 생성하고, 있다면 교체.  PATCH는 리소스의 일부를 수정.

### DELETE

리소스를 삭제

### 멱등성

동일한 요청을 여러 번 보내더라도 그 결과가 같은 것을 ‘멱등하다’고 한다.

- **GET, PUT, DELETE** 요청은 멱등성을 보장한다.
- **POST** 요청은 리소스를 생성할 때 사용하므로 멱등성을 가지지 않는다.
- **PATCH**는 요청의 내용에 따라 멱등하게도, 멱등하지 않게도 설계할 수 있다. 예컨대 ‘age’ 필드의 값을 ‘20’으로 수정하는 PATCH 요청은 멱등하지만, 같은 필드의 값을 10만큼 더하는 PATCH 요청은 멱등하지 않다.

## HTTP 상태코드

---

- **1xx(정보) :** 요청을 받았으며 프로세스를 계속 진행합니다.
- **2xx(성공) :** 요청을 성공적으로 받았으며 인식했고 수용하였습니다.
- **3xx(리다이렉션) :** 요청 완료를 위해 추가 작업 조치가 필요합니다.
- **4xx(클라이언트 오류) :** 요청의 문법이 잘못되었거나 요청을 처리할 수 없습니다.
- **5xx(서버 오류) :** 서버가 명백히 유효한 요청에 대한 충족을 실패했습니다.

### 200(ok) vs 201(created)

200은 요청 처리가 성공적으로 완료되었다는 뜻. 201은 요청이 성공적으로 처리돼 새로운 리소스가 만들어졌다는 뜻으로, 일반적으로 POST 요청 또는 일부 PUT 요청 이후에 따라온다.

### 401(Unauthorized) vs 403(forbidden)

401은 클라이언트가 인증되지 않아(로그인하지 않아) 요청이 거부되었다는 뜻. 403은 인증된 클라이언트이지만 접근 권한이 없는 요청을 해서 요청이 거부되었다는 뜻.

## HTTP/1.1, HTTP/2, HTTP/3

---

### HTTP/1.1

- 지속 연결(Persistent connection): 지정한 timeout 동안 연속적인 요청 사이에 커넥션을 닫지 않음. 기존 연결에 대해서 handshake 생략 가능
- 파이프라이닝(pipelining): 이전 요청에 대한 응답이 완전히 전송되기 전에 다음 전송을 가능하게 하여, 여러 요청을 연속적으로 보내 그 순서에 맞춰 응답을 받는 방식으로 지연 시간을 줄이는 방식 → **HOLB**를 낳음
- HOLB(Head Of Line Blocking): 파이프라이닝 방식으로 요청을 처리할 때 반드시 요청받은 순서대로 처리하기 때문에 앞선 요청이 완료되지 않으면 뒤의 요청도 응답이 늦어지게 된다. 이를 HOLB 문제라 하며, 이 때문에 모던 브라우저에서는 대부분 파이프라이닝을 금지하고 있다.

### HTTP/2

- 멀티플렉싱: 한 개의 TCP 연결에서 동시다발적으로 여러 개의 요청과 응답을 처리하며, 응답 순서에 무관하게 데이터를 처리 가능하게 됨.


### HTTP/3

- 기존 TCP 기반의 프로토콜이었던 HTTP1, HTTP2와 달리 **UDP 기반**의 프로토콜인 QUIC을 사용하여 통신하는 프로토콜
- UDP 프로토콜 자체는 TCP보다 신뢰성이 낮기도 하고 흐름 제어도 안되지만, 커스터마이징이 가능하기 때문에 구현을 어떻게 하냐에 따라서 TCP와 비슷한 수준의 기능을 가질 수도 있다.

## HTTPS

---

- HTTPS: HTTP에 데이터 암호화가 추가된 프로토콜
- HTTPS의 필요성: HTTP는 단순 텍스트를 주고 받기 때문에 누군가 네트워크에서 신호를 가로채어 본다면 내용이 노출된다.
- 대칭키 암호화와 비대칭키 암호화를 모두 사용해 빠른 속도와 안정성을 보장

### 대칭키, 비대칭키

- 대칭키 암호화
    - 클라이언트와 서버가 동일한 키를 사용해 암호화/복호화를 진행함
    - 키가 노출되면 매우 위험하지만 연산 속도가 빠름
- 비대칭키 암호화
    - 1개의 쌍으로 구성된 공개키와 개인키를 암호화/복호화 하는데 사용함
    - 키가 노출되어도 비교적 안전하지만 연산 속도가 느림

### https 연결 성립 과정

1. 클라이언트(브라우저)가 서버로 최초 연결 시도를 함
2. 서버는 인증서를 브라우저에게 넘겨줌
3. 브라우저는 CA(Certificate Authority)의 공개키를 통해 인증서를 복호화해 유효성을 검사하고 세션키를 발급함
4. 브라우저는 세션키를 보관하며 추가로 서버의 공개키로 세션키를 암호화하여 서버로 전송함
5. 서버는 개인키로 암호화된 세션키를 복호화하여 세션키를 얻음
6. 클라이언트와 서버는 동일한 세션키를 공유하므로 데이터를 전달할 때 세션키로 암호화/복호화를 진행함

### SSL, TLS

- SSL(Secure Sockets Layer)과 TLS (Transport Layer Security) 모두 네트워크 통신에서 보안을 제공하는 프로토콜
- TLS는 SSL보다 보안이 강화된 현대적 버전의 프로토콜이다.

### 참고 자료

---

- [https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer Science/Network/HTTP %26 HTTPS.md](https://github.com/gyoogle/tech-interview-for-developer/blob/master/Computer%20Science/Network/HTTP%20%26%20HTTPS.md)
- [https://inpa.tistory.com/entry/HTTP-🌐-백엔드-로드맵-HTTP는-무엇일까요](https://inpa.tistory.com/entry/HTTP-%F0%9F%8C%90-%EB%B0%B1%EC%97%94%EB%93%9C-%EB%A1%9C%EB%93%9C%EB%A7%B5-HTTP%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C%EC%9A%94)
- https://cl8d.tistory.com/63
- https://oen-blog.tistory.com/211
- https://mangkyu.tistory.com/146
- https://withbundo.blogspot.com/2021/08/httphttp2-http2.html
- https://evan-moon.github.io/2019/10/08/what-is-http3/
- https://www.youtube.com/watch?v=H6lpFRpyl14
- https://aws.amazon.com/ko/what-is/ssl-certificate/
