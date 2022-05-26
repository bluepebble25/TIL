# HTTPS와 SSL
> 1. [HTTPS란](#https란)
> 2. [SSL](#ssl)
> 3. [SSL 인증서가 안전한 서비스 제공자를 보장하는 방법](#ssl-인증서가-안전한-서비스-제공자를-보장하는-방법)
> 4. [SSL 통신 과정 3단계](#ssl-통신-과정-3단계)
> 5. [인증서 등록하기](#인증서-등록하기)

## HTTPS란
HTTP 프로토콜은 TCP/IP 계층에서 최상위인 응용(Application) 계층에 해당하는 프로토콜이다. 그 중에서도 HTTPS는 HTTP 아래에 SSL(Secure Socket Layer) 이라는 보안을 위한 계층이 하나 더 있는 것을 말한다. TLS라는 것도 있는데, SSL의 3.0 ver을 참고해 표준화 한 것이 TLS(Transport Layer Security)이다. SSL과 TLS는 보통 같은 의미로 사용된다.

TCP/IP 계층 구조와 프로토콜
- HTTP
  - HTTP (응용 계층)
  - TCP (전송 계층, 식별 주소는 포트번호)
  - IP (인터넷 계층)
  - MAC 주소 (네트워크 인터페이스 계층)

- HTTPS
  - HTTP (응용 계층)
  - SSL (Secure Socket layer / 보안 계층)
  - TCP (전송 계층, 식별 주소는 포트번호)
  - IP (인터넷 계층)
  - MAC 주소 (네트워크 인터페이스 계층)

HTTP의 기본 포트 번호는 80번, HTTPS는 443번이다. 둘 다 TCP 계층을 갖고 있기 때문에 연결을 시작할 때는 3way-handshake, 종료할 때는 4way-handshake를 거치게 된다. 이것은 프로그래머가 신경쓰지 않도록 OS가 알아서 처리해주고 인터페이스로 소켓을 제공한다.

HTTP로 전송되는 정보는 암호화되지 않기 때문에 보안에 취약하다. HTTPS는 HTTP 아래에 있는 SSL이라는 보안 계층이 통신 내용을 암호화해주고 보안을 위한 기능을 제공한다. HTTPS 프로토콜을 이용하려면 인증 기관(CA)로부터 발급받은 SSL 인증서를 서버에 등록해야 한다.

## SSL
SSL에서 사용하는 암호화 방식은 대칭키 암호화와 공개키 암호화 방식을 혼합한 형태이다.
1. 대칭키 암호화 방식 : 암호화와 복호화를 하는데 한 가지의 동일한 키를 사용한다. 보내는 쪽에서 1234라는 키로 암호화를 한 후 그 것을 보낸다면, 받는 쪽에서도 1234라는 키로 복호화를 하는 것이다. 하지만 대칭키가 유출된다면 암호화가 무용지물이 되기 때문에 대칭키를 전달하는데 어려움이 있다는 단점이 있다.
2. 공개키 암호화 방식 - 공개키(public key)와 개인키(private key)라는 쌍을 이루는 키를 만든다. 발신자는 수신자가 공개해놓은 공개키를 이용해 정보를 암호화하고 수신자에게 보낸다. 수신자는 자신의 개인키로 복호화한다.
  - 어떤 사이트에 접속할 때 그 사이트의 디지털 인증서 안에 첨부되어 있는 공개키로 메시지를 암호화해 전송하면 사이트는 자신의 개인키로 메시지를 복호화한다.
  - 안전한 데이터 전달 외에도 데이터 제공자의 신원을 보장하는 데 사용된다. 보내는 쪽이 자신의 비공개 키로 정보를 암호화하고, 비공개키와 짝을 이루는 공개키와 함께 암호화된 정보를 전송한다. 수신자가 공개키로 암호화된 정보를 복호화할 수 있다면 그 데이터는 공개키와 쌍을 이루는 비공개키에 의해 암호화 되었다는 것을 의미한다. 따라서 그게 사람이 서명을 했다는 증거가 된다. 이게 전자 서명이다.
  - 공개키 방식의 단점은 알고리즘의 계산이 좀 느린 경향이 있다는 것이다.

공개키 방식은 대칭키에 비해 안전하지만 알고리즘 계산에 시간이 조금 소요되므로 SSL 통신에서는 둘이 혼합된 방식이 사용된다.
> - 실제 암호화에는 대칭키를 이용한다.
> - 하지만 대칭키가 노출되면 위험하므로 안전한 공개키 방식의 소통 채널로 대칭키를 교환한다.

## SSL 인증서가 안전한 서비스 제공자를 보장하는 방법
CA(Certificate Authority)는 디지털 인증서를 제공할 수 있는 공인된 기관이나 기업이다. 브라우저에는 기본적으로 CA 리스트가 탑재되어 있다. 브라우저의 소스코드에 CA가 포함되어 있다는 뜻이다. 브라우저는 CA 리스트와 각 CA의 공개키를 알고 있기 때문에, 어떤 서버에 접속할 때 다운받은 인증서가 자신의 CA 리스트에 있는지 확인하고 그에 맞는 공개키로 복호화를 할 수 있다. 복호화를 할 수 있다는 것은 서버가 CA의 비공개키로 암호화를 했다는 의미이고, CA의 비공개키를 갖고 있다는 것은 CA로부터 인증을 받았다는 뜻이니까 신뢰할 수 있는 사이트라는 의미가 된다.

## SSL 통신 과정 3단계
`1. 핸드셰이크 -> 2. 세션 -> 3. 세션 종료`

1. 핸드셰이크 (SSL 핸드셰이크)  
HTTPS도 TCP 계층을 갖고 있기 때문에 SSL 핸드셰이크를 하기 전에 3-way-handshake를 거친다. 그림에서 파란 부분은 TCP의 handshake이고, 노란 부분이 SSL 핸드셰이크이다. 앞으로는 SSL 핸드셰이크를 핸드셰이크라고 하겠다.
![](https://cf-assets.www.cloudflare.com/slt3lc6tev37/5aYOr5erfyNBq20X5djTco/3c859532c91f25d961b2884bf521c1eb/tls-ssl-handshake.png)
이미지 출처: [Cloudfare](https://www.cloudflare.com/ko-kr/learning/ssl/what-happens-in-a-tls-handshake/)

- Client hello : 클라이언트가 서버에 접속 요청을 보낸다. 그러면서 자신의 브라우저가 지원할 수 있는 암호화 방식(Cipher Suite)과 랜덤 데이터를 함께 보낸다.
- Server hello : 서버가 클라이언트에게 응답한다. 서버는 클라이언트가 제시한 암호화 방식 중 하나를 선택해 알려주고, 서버의 공개키가 첨부되어 있는 인증서와 서버 측에서 생성한 랜덤 데이터를 전송한다.
- Client key exchange : 클라이언트는 미리 주고받은 자신과 서버의 랜덤 데이터를 참고하여 서버와 암호화 통신을 할 때 사용할 키인 pre master secret key(대칭키)를 생성한 후 서버에게 전달한다. 이때 대칭키는 서버로부터 받은 공개키로 암호화되어 보내진다.
- Finished : 서버는 전달받은 암호화된 대칭키를 자신의 개인키로 복호화해 session key (대칭키)를 생성한다.

2. 세션  
클라이언트와 서버가 데이터를 주고받는 과정이다. 암호화는 각자 갖고 있는 session key(대칭키)를 이용한다. 둘 다 같은 대칭키를 갖고 있으므로 상대방이 암호화한 내용을 복호화할 수 있다.

3. 세션 종료  
통신이 끝났음을 상대방에게 알린다. 이때 통신에서 사용한 대칭키를 폐기한다.

## 인증서 등록하기
1. 먼저 CA에서 인증서를 발급받는다. 무료도 있고 유료도 있다. 각자 제공하는 서비스의 종류나 기간이 다르므로 잘 비교해보자. 국내 호스팅 업체에서 대행해주기도 한다. CA에 정보를 사실대로 입력하고 승인받으면 비공개키와 인증서를 받을 수 있다. 비공개키는 유출되면 안되므로 별도의 파일로 안전한 곳에 저장해야 한다.
2. 서버에 인증서를 설치한다. 서버의 종류(Apache, Nginx, ...)에 따라 설치하는 방법이 다르므로 그건 따로 검색해서 찾아보자.
3. 서버에 http로 접속하는 유저를 https로 리다이렉트하는 설정도 해주자.

## Reference
- https://wayhome25.github.io/cs/2018/03/11/ssl-https/
- https://opentutorials.org/course/228/4894
- https://blog.naver.com/skinfosec2000/222135874222
- https://nuritech.tistory.com/25