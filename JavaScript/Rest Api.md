## 요약

1. REST란, 웹 사이트에서 사용하는 모든 자원들(리소스)에 고유한 URI를 부여하고, 그 URI를 통해 자원에 접근하며 HTTP Method를 이용하여 자원의 상태를 관리하는 아키텍쳐 스타일이다.
2. RESTful API란, REST 아키텍쳐의 스타일을 따르는 API이다.

# API

---

API(Application Programming Interface)는 소프트웨어 간 상호 작용을 하기 위해 만들어진 통신 규칙이다.

## API의 종류

---

### 접근 방식에 따른 API 유형

1. Private API
   - 내부 API로, 기업이나 연구 단체 등 특정 단체의 내부에서만 사용하며 외부에 노출되지 않는다.
2. Public API
   - 개방형 API로, 모두에게 공개하는 API이다.
   - 접속하는 대상에 제약이 없는 경우, Open API라고 한다.
3. Partner API
   - 비즈니스 파트너 간의 데이터 공유 등 특정인들만 사용할 수 있다.

### 아키텍쳐 스타일에 따른 API 유형

1. SOAP(Simple Object Access Protocol) API
   - XML을 사용해 메시지를 교환하는 프로토콜
   - SOAP은 보안 및 표준화에 대해 높은 수준의 규칙을 제공한다.
   - SSL, WS-Security 보안을 지원하며 특히 WS-Security는 전송 레벨에서 매우 뛰어난 보안성을 가진다.
   - ACID를 준수하여 데이터의 변형을 줄이고, 사전에 DB와 상호작용을 정확하게 정의하기 때문에 데이터의 무결성을 지킨다.
2. RPC(Remote Procedure Call) API
   - 원격 프로시저 호출이 가능한 네트워크 프로토콜
3. REST API
   - HTTP 프로토콜을 활용하는 REST 아키텍쳐 스타일 기반 API
4. GraphQL

## REST

- REST(Representational State Transfer)는 자원을 이름으로 구분해 해당 자원의 상태를 주고 받는 모든 것을 의미한다.
  - 즉, 자원의 표현에 의한 상태 전달이다.
- HTTP 프로토콜을 그대로 활용하기 때문에, 웹이 장점을 최대한 활용할 수 있는 아키텍쳐 스타일

### REST의 개념

- HTTP URI(Uniform Resource Identifier)를 통해 자원을 명시하고, HTTP Method를 통해 해당 자원에 대한 CRUD를 적용하는 것
- 즉, 웹 사이트에서 사용하는 모든 자원들(리소스)에 고유한 URI를 부여하고, 그 URI를 통해 자원에 접근하며 HTTP Method를 이용하여 자원의 상태를 관리하는 아키텍쳐 스타일이다.

### REST의 구성 요소

1. **자원(Resource)**
   - 모든 자원은 서버에 존재하며, 고유의 ID(URI)가 있다.
   - URI의 형태는 `/shop/{shop_id}` 의 형태를 가지고 있다.
   - 클라이언트에서 URI를 통해 자원을 선택하고, Method를 통해 자원의 상태 조작을 서버에 요청한다.
2. **Method**
   - HTTP Method(GET, POST, DELETE, PUT, PATCH)를 사용한다.
3. **표현(Representation of Resource)**
   - 클라이언트와 서버가 주고받는 데이터의 형태
   - JSON, XML, TEXT, RSS 등이 있으나 일반적으로는 JSON, XML을 통해 데이터를 주고 받는다.

### REST의 제한 조건

1. **Server-Client 구조**
   - 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client
   - 서버는 API를 제공하여 자원의 처리 및 저장을 책임지고, 클라이언트는 사용자 인증이나 세션, 로그인 정보 등을 직접 관리하고 책임진다.
   - 역할을 확실히 구분함으로써 의존성이 감소된다.
2. **무상태성(Stateless)**
   - HTTP 프로토콜이 무상태성을 갖기 때문에 REST 또한 무상태성을 갖는다.
   - 클라이언트의 context(클라이언트 상태 정보)를 서버에 저장하지 않는다.
   - 서버는 각각의 요청을 완전히 별개의 것으로 인식하고 처리한다.
     - 단순히 클라이언트의 요청만을 처리하며, 이전 요청이 다음 요청의 처리에 연관되어서는 안된다.
     - 그러나 DB가 변경되며 다음 요청의 처리에 연관되는 경우는 허용한다.
3. **캐시 처리 기능(Cacheable)**
   - HTTP 프로토콜을 사용하므로 웹에서 사용하는 기존의 인프라를 그대로 활용할 수 있다.
   - HTTP Last-Modified Tag 또는 E-Tag를 이용하여 캐싱 구현 가능
   - 대량의 요청을 효율적으로 처리할 수 있다.
4. **계층 구조(Layered System)**
   - 클라이언트는 서버의 API만 호출한다.
   - 서버는 다중 계층으로 구성될 수 있다.
     - 보안, 로드 밸런싱, 암호화 등을 위한 계층을 추가하는 등 계층 구조 변경이 가능하다.
     - Proxy, Gateway 등의 중간 매체를 사용할 수 있다.
     - 클라이언트는 서버와 직접적인 통신을 하는지, 중간 서버와 통신하는지 알 수 없다.
5. **인터페이스 일관성(Uniform Interface)**
   - 모든 자원이 URI로 표현되기 때문에 자원의 조작을 통일되고 한정적인 인터페이스로 수행한다.
   - HTTP 프로토콜을 따르는 모든 플랫폼에서 사용 가능하며 특정 언어나 기술에 종속되지 않는다.
6. **자체 표현(Self-Descriptiveness)**
   - 요청 메세지만으로도 어떤 의미인지 쉽게 이해할 수 있는 구조를 가진다.
7. **Code-On-Demand**
   - Server로부터 스크립트를 받아서 Client에서 실행한다.
   - 반드시 충족할 필요는 없다.

## RESTful API(REST API)

---

REST 아키텍쳐의 스타일을 따르는 API이다.

⇒ URI와 HTTP Method를 사용하여 URI로 어떤 자원에 접근할 것인지, Method로 어떤 처리를 할 것인지 표현하도록 설계된 API

### REST API의 특징

- 확장성
  - REST의 인터페이스 일관성 원칙에 의해 기존 API의 수정 없이 새로운 기능이나 서비스 추가를 할 수 있다.
  - 기능 고도화, 시스템 확장, 유지 보수에 유리하다.
- 플랫폼 독립적
  - HTTP 프로토콜을 기반으로 하여, 특정 언어나 기술에 종속되지 않는다.
  - 클라이언트와 서버의 구현이 완전히 독립적이라는 장점이 있다.
- 계층적 구조
  - 클라이언트와 서버가 서로 분리되어 계층적 구조를 가진다.
  - 분리된 구조로 인해 각각 독립적으로 유지 보수 및 개발이 편리하다.

### REST API 설계 규칙

1. 슬래시 구분자(/ )는 계층 관계를 나타내는데 사용한다.
   ex) **http://restapi.example.com/houses/apartments**
2. URI 마지막 문자로 슬래시(/ )를 포함하지 않는다.
   - URI에 포함되는 모든 글자는 리소스의 유일한 식별자로 사용되어야 하며 URI가 다르다는 것은 리소스가 다르다는 것이고, 역으로 리소스가 다르면 URI도 달라져야 한다.
   - REST API는 분명한 URI를 만들어 통신을 해야 하기 때문에 혼동을 주지 않도록 URI 경로의 마지막에는 슬래시(/)를 사용하지 않는다.
     ex) **http://restapi.example.com/houses/apartments/** (X)
3. 하이픈(- )은 URI 가독성을 높이는데 사용
   - 불가피하게 긴 URI경로를 사용하게 된다면 하이픈을 사용해 가독성을 높인다.
4. 밑줄(\_ )은 URI에 사용하지 않는다.
   - 밑줄은 보기 어렵거나 밑줄 때문에 문자가 가려지기도 하므로 가독성을 위해 밑줄은 사용하지 않는다.
5. URI 경로에는 소문자가 적합하다.
   - URI 경로에 대문자 사용은 피하도록 한다.
   - RFC 3986(URI 문법 형식)은 URI 스키마와 호스트를 제외하고는 대소문자를 구별하도록 규정하기 때문이다.
6. 파일확장자는 URI에 포함하지 않는다.
   - REST API에서는 메시지 바디 내용의 포맷을 나타내기 위한 파일 확장자를 URI 안에 포함시키지 않는다.
   - Accept header를 사용한다. ex) **http://restapi.example.com/members/soccer/345/photo.jpg** (X)ex) GET / members/soccer/345/photo HTTP/1.1 Host: restapi.example.com Accept: image/jpg (O)
7. 리소스 간에는 연관 관계가 있는 경우
   - /리소스명/리소스 ID/관계가 있는 다른 리소스명ex) GET : /users/{userid}/devices (일반적으로 소유 ‘has’의 관계를 표현할 때)

### **REST API에서의 에러 처리 방법**

- REST API에서는 에러도 사용자 친화적이고, 대부분의 경우는 기계 읽기 가능한 형식으로 반환되어야 한다.
- 일반적으로 HTTP 상태 코드와 함께 응답에 에러에 대한 추가 정보를 포함한다.
  - 예를 들면, 항목을 찾을 수 없는 경우 HTTP 상태 코드인 404(Not Found)를 반환하고, 에러 메시지와 함께 그 이유에 대한 자세한 정보를 JSON 형식으로 제공할 수 있다.

# 출처

- https://dev-coco.tistory.com/97#head1
- https://ko.wikipedia.org/wiki/REST
- https://thalals.tistory.com/284
