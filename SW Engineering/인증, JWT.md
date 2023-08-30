# 요약

- JWT는 **인증에 필요한 정보들을 암호화시킨 JSON 토큰**을 의미한다.
- JWT는 JSON 데이터를 **Base64 URL-safe Encode** 를 통해 인코딩하여 직렬화한 것이다.
- 토큰 내부에는 위변조 방지를 위해 개인키를 통한 **전자서명**도 들어있다.
- 사용자가 JWT 를 서버로 전송하면 서버는 서명을 검증하는 과정을 거치게 되며 검증이 완료되면 요청한 응답을 돌려준다.
- JWT는 .을 구분자로 Header, Payload, Signature로 이뤄져있다.
- 인증과정을 요약하는건 불가능하기에 밑의 본문글 참고할것.


# JWT 구조
![image](https://github.com/novice-hero/TikTechTalk/assets/88226519/b53dc212-4994-48d6-8281-d04f72ab1c41)
<br />
JWT는 . 을 구분자로 Header, Payload, Signature를 의미한다.

- Header : JWT에서 사용할 타입과 해시 알고리즘의 종류가 담김.
- Payload : 서버에서 첨부한 사용자 권한 정보와 데이터가 담김.
- Signature : Header, Payload를 Base64 Encode 한 이후 Header에 명시된 해시함수를 적용하고, 개인키로 서명한 전자서명이 담김.

Header와 Payload는 단순히 인코딩된 값이기 때문에 제 3자가 복호화 및 조작 가능
Signature는 서버 측에서 관리하는 비밀키가 유출되지 않는 이상 복호화할 수 없다.

→ Signature는 토큰의 위변조 여부를 확인하는데 사용된다.

<br />

# JWT를 이용한 인증 과정
![image](https://github.com/novice-hero/TikTechTalk/assets/88226519/8ea7e2a3-49e7-43aa-84b7-c5b8f827dcea)
<br />
1. 사용자가 서버에 로그인 인증을 요청
2. 서버에서 클라이언트 측의 인증 요청을 받으면 Header, Payload, Signature를 정의
    a. Header, Payload, Signature를 각각 Base 64로 한 번 더 암호화하여 JWT생성
    b. 쿠키에 담아 클라이언트에 발급
3. 클라이언트는 서버로부터 받은 JWT를 저장한다. (ex - 로컬 스토리지, 쿠키 등등)
4. API를 서버에 요청할때 Authorization Header에 Access Token을 담아 보낸다.
5. 서버는 클라이언트가 Header에 담아 보낸 JWT가 내 서버에서 발행한 토큰인지 일치 여부를 확인하여 일치한다면 인증을 통과
    a. 페이로드에 들어있는 유저의 정보들을 select해서 클라이언트에 돌려줌
6. 액세스 토큰의 시간이 만료되면 클라이언트는 리프래쉬 토큰을 이용
    a. 서버로부터 새로운 액세스 토큰을 발급받는다.

# Access Token과 Refresh Token
- **Access Token** : **클라이언트**가 갖고있는 실제로 유저의 정보가 담긴 토큰으로, 클라이언트에서 요청이 오면 서버에서 해당 토큰에 있는 정보를 활용하여 사용자 정보에 맞게 응답을 진행
- **Refresh Token**: 새로운 Access Token을 발급해주기 위해 사용하는 토큰으로 짧은 수명을 가지는 Access Token에게 새로운 토큰을 발급해주기 위해 사용. 해당 토큰은 보통 **데이터베이스**에 유저 정보와 같이 기록.

Access Token 만을 통한 인증 방식의 문제는 제 3자에게 탈취당할경우 보안에 취약

탈취 당하면 토큰이 만료되기 전까지 삭제가 불가능하며 누구나 권한 접근이 가능하다.

토큰 유효시간을 짧게하면 토큰 남용을 방지할수 있지만 불편해지는 단점이 생긴다.

Refresh 토큰은 긴 유효시간을 가지면서 Access Token이 만료됐을때 새로 재발급해주는 열쇠의 역할을 한다.

<br />

![image](https://github.com/novice-hero/TikTechTalk/assets/88226519/ecec7ee3-9ee6-4d3a-8dc1-d727279a070c)

<br />

1. 사용자가 ID, PW를 이용하여 로그인

2. 서버에서는 회원 DB에서 값을 비교

3~ 4. 로그인이 완료되면 Access Token, Refresh Token을 발급한다. 이때 회원DB에도 Refresh Token을 저장해둔다.

5. 사용자는 Refresh Token은 안전한 저장소에 저장 후, Access Token을 헤더에 실어 요청을 보낸다.

6~ 7. Access Token을 검증하여 이에 맞는 데이터를 보낸다.

8. 시간이 지나 Access Token이 만료됐다.
9. 사용자는 이전과 동일하게 Access Token을 헤더에 실어 요청을 보낸다.

10~ 11. 서버는 Access Token이 만료됨을 확인하고 권한없음을 신호로 보낸다.

12. 사용자는 Refresh Token과 Access Token을 함께 서버로 보낸다.
13. 서버는 받은 Access Token이 조작되지 않았는지 확인한후, Refresh Token과 사용자의 DB에 저장되어 있던 Refresh Token을 비교한다. Token이 동일하고 유효기간도 지나지 않았다면 새로운 Access Token을 발급해준다.
14. 서버는 새로운 Access Token을 헤더에 실어 다시 API 요청 응답을 진행한다.

<br />

<aside>
💡 위의 9번에서 11번을 굳이 거칠 필요는 없다. <br />
사용자에서 Acess Token의 Payload를 통해 유효기간을 알수있다. <br />
프론트엔드 단에서 API 요청 전에 토큰이 만료됐다면 곧바로 재발급 요청을 보낼수도 있다. <br />
<br />
작성자의 주관적인 의견 : 위의 9번에서 11번도 필요한 작업이라고 생각합니다. <br />
클라이언트에서 확인했을때 만료가 아니더라도 서버로 보내는 과정에서 만료되는 상황이 발생할수있기때문에 에러 핸들링을 대비해놓기 위해 2가지 방법을 구축하는게 안전할거같습니다. <br />

</aside>

<br />

# JWT의 장단점

장점

- 인증을 위한 별도의 저장소가 필요 없음
- 헤더와 페이로드를 가지고 시그니처를 생성하므로 데이터 위변조 차단
- 클라이언트 인증 정보를 저장하는 세션과 다르게 서버는 무상태(StateLess)가 되어 서버 확장성이 우수
- 토큰 기반으로 다른 로그인 시스템에 접근 및 권한 공유 가능 (쿠키와의 차이)
- OAuth의 경우 Facebook, Google 등 소셜 계정을 이용하여 다른 웹서비스에서도 로그인 가능
- 모바일 어플리케이션 환경에서도 동작 (모바일은 세션 사용 불가능)

단점

- 토큰 자체에 정보를 담고 있으므로 양날의 검이 될 수 있다.
- Payload 자체는 암호화 된것이 아니라 BASE 64로 인코딩 된것이기 때문에, 중간에 Payload를 탈취하여 디코딩하면 데이터를 볼수 있으므로 Payload에 중요 데이터를 넣을수 없다.
- 정보가 많아질수록 토큰의 길이가 늘어나 네트워크에 부하를 줄 수 있다.
- 토큰 자체를 탈취당하면 대처하기가 어렵게 된다. (유효시간을 그래서 짧게 설정)
