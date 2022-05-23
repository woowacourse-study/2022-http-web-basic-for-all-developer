# 4주차

## HTTP 헤더

- header-field = field-name “:” OWS field-value OWS (OWS: 띄어쓰기 허용)
- HTTP 전송에 필요한 모든 부가정보
    - 메시지 바디 내용, 바디 크기, 압축, 인증, 서버 정보 등등
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능

### RFC 변화

- 2014년 HTTP 표준 RFC7230이 등장하면서 RFC2616 폐기 됨.
    - 엔티티 바디라는 용어가 사라짐
    - 엔티티(Entity) → 표현(Representation)
    - Representation(표현) = Representation Metadata + Representation Data

**HTTP BODY**

- 메시지 본문(message body)을 통해 표현 데이터 전달 (메시지 본문 = 페이로드)
- 표현(메타데이터 + 데이터)은 요청이나 응답에서 전달할 실제 데이터
- 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
    - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등

## 표현

- 서로 간에 이해할 수 있는 무언가로 변화가 필요
    - DB에 있는 바이너리 데이터를 전송하면 무슨 소리인지 모름
- 리소스를 (json, xml, html…) 라는 표현으로 전달한다.
    - 표현 - 전달하는 데이터를 의미
- 표현 헤더는 전송과 응답 모두 사용

### Content-Type

- 표현 데이터의 형식 설명
- 미디어 타입, 문자 인코딩
- text/html; charset=utf8
- application/json

### Content-Encoding

- 표현 데이터 인코딩
- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
- gzip, deflate, identity(압축 안함)

### Content-Language

- 표현 데이터의 자연 언어를 표현
- ko, en

### Content-Length

- 표현 데이터의 길이
- 바이트 단위
- Transfer-Encoding(전송 코딩)을 사용할 경우에는 사용하면 안됨.
    - 전송 코딩 안에 관련 정보가 다 들어있음

## 협상(콘텐츠 네고시에이션)

- 클라이언트가 선호하는 표현 요청
- 클라이언트가 원하는 우선순위에 맞춰서 전달해주는 것.
- 요청 시에만 사용

- Accept : 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset : 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding : 클라이언트가 원하는 압축 인코딩
- Accept-Language : 클라이언트가 선호하는 자연 언어

### 협상 예시 (Accept-Language)

- 적용 전
    - 클라이언트에 대한 아무런 정보가 없음 → 기본값인 영어로 응답

- 적용 후
    - 요청 시, 선호하는 언어를 설정 → 지원하는 언어에 있으면 해당 언어로 응답

- 해당 언어를 지원하지 않으면, 문제 발생할 수 있음 → 우선 순위가 생김

### 협상과 우선 순위

- Quality Values (q) 값 사용
- 0~1, 클수록 높은 우선순위
- 생략하면 1
- Accept-Lanugage: ko-KR, ko;q=0.9, en-Us;q=0.8,en;q=0.7

- 구체적인 것이 우선한다.
    1. text/plain;format=flowed
    2. text/plain
    3. text/*

## 전송 방식

### 단순전송

- Content-Length 의 바이트만큼 데이터를 받는다.
- 길이를 알아야 사용 가능

### 압축전송

- gzip 등으로 압축해서 제공한다.
- Content-Encoding 인코딩한 방법을 알려준다.

### 분할 전송

- Transfer-Encoding: chunked
- n 바이트씩 보내고, \r\n 이면 끝.
- Content-Length 값이 예상되지 않는다. chunk 마다 Length가 있기 때문에 Content-Length 값을 주면 안된다.

### 범위 전송

- Range로 범위를 지정해서 요청, Content-Range 로 처음과 끝 길이를 담아서 반환

## 일반 정보

### From

유저에이전트의 이메일 정보

- 일반적으로 사용되진 않음.
- 검색엔진에서 주로 사용

### Referer

이전 웹 페이지 주소

- 현재 요청된 페이지 이전 웹 페이지 주소
- A → B로 이동할 경우 B를 요청할 때 Referer:A를 포함함
- 요청에서 사용 → 유입 경로 분석

### User-Agent

클라이언트의 애플리케이션 정보 (웹 브라우저 정보 등등)

- user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/
537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
- 통계 정보
- 요청에서 사용

### Server

요청을 처리하는 ORIGIN 서버의 소프트웨어 정보

- 진짜 나의 요청이 있는 마지막 서버
    - 실제 애플리케이션에서 나의 표현 데이터를 만들어주는 서버
- 응답에서 사용

### Date

메시지가 발생한 날짜와 시간

- 응답에서 사용

## 특별한 정보

### Host

요청한 호스트 정보 (도메인)

- 요청 시 필수
- 하나의 서버, 하나의 IP 주소에 여러 도메인을 처리해야 할때

### Location

- 3XX 응답 결과에 Location 헤더가 있으면 자동 이동 (리다이렉트)
- 201 (Created) : Location 값으로 생성된 리소스 URI
- 3xx (Redirection) : Location 값으로 리다이렉션하기 위한 대상 리소스

### Allow

허용가능한 HTTP 메서드

- 405에서 응답에 포함해야 함 (Allow: GET, HEAD, PUT)
- 많이 구현하지는 않음

### Retry-After

유저에이전트가 다음 요청을 하기까지 기다려야 하는 시간

- 503 (Service Unavailable) 서비스가 언제까지 불능인지 알려줄 수 있음.
- 실제로 사용하긴 쉽지 않음.

## 인증

### Authorization

클라이언트 인증 정보를 서버에 전달

- 헤더를 제공, 인증과 관련된 값을 넣어줌
- Authoriztion: Basic xxxxxxx

### WWW-Authenticated

리소스 접근 시 필요한 인증 방법 정의

- 401 Unauthorized 응답과 함께 사용
    - WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"
    - 인증 하려면 위의 정보를 참고해서 인증 정보를 만들어라

## 쿠키

- Set-Cookie
    
    서버에서 클라이언트로 쿠키 전달 (응답)
    
- Cookie
    
    클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청 시 전달
    

### 쿠키를 사용하는 이유

- HTTP는 무상태(Stateless) 프로토콜이다
    - 요청과 응답을 주고 받으면 연결이 끊어진다.
    - 이전 요청을 기억하지 못한다.
    - 클라이언트와 서버는 서로 상태를 유지하지 않는다.
- 대안1 - 모든 요청에 사용자 정보 포함
    - 모든 요청과 링크에 사용자 정보가 포함되도록 개발해야 한다.
    - 보안 문제, 매 요청에 쿠키를 포함해야하는 번거로움

### 이러한 문제점을 해결하고자 `쿠키` 등장

- 로그인을 하면 서버는 유저 정보를 Set-Cookie에 넣어서 응답한다.
- 웹 브라우저 내부의 쿠키 저장소에 Set-Cookie 를 저장한다.

- 로그인 이후, 모든 요청 시마다 쿠키 저장소에서 쿠키를 조회해서 Cookie 헤더에 포함하여 전달한다.

### 쿠키 단점

- 쿠키에 대한 정보를 매 헤더(Http Header)에 추가하여 보내기 때문에 `상당한 트랙픽`을 발생
- 결제 정보 등을 쿠키에 저장하였을때 쿠키가 유출되면 `보안에 대한 문제점`도 발생할 수 있음

### 쿠키 제약 방법

- 예) set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; domain=.google.com; Secure
- 사용처
    - 로그인 세션 관리
        1. 서버에서는 요청 쿠키 정보에 Session id를 확인을 한 후에 없으면 Set-Cookie를 통해 새로 발행한 `Session-id` 반환
        2. 클라이언트는 HTTP Request 헤더에 Session id를 포함하여 요청
        3. 서버는 Session id를 통해 해당 세션을 찾아 응답
    - 광고 정보 트래킹
- 쿠키 정보는 항상 서버에 전송됨
    - 네트워크 트레픽 추가 유발
    - 최소한의 정보만 사용 (세션 id, OAuth 인증토큰)
    - 서버에 전송하지 않고, 웹 브라우저 내부에 저장하고 싶으면 웹 스토리지 (localStorage, sessionStorage)
- 보안에 민감한 데이터는 저장하면 안됨.

### 쿠키 - 생명주기

- Set-Cookie: expires = (만료일)
    - 만료일이 되면 쿠키 삭제
- Set-Cookie: max-age = n초
    - 0이나 음수면 삭제. 즉, n초만큼 유지
- 세션쿠키
    - 만료 날짜를 생략하면 브라우저 종료시까지 유지
- 영속 쿠키
    - 만료일 입력하면 해당 날짜까지

### 쿠키- 도메인

- 명시 : 명시한 문서 기준 도메인 + 서브 도메인
    - domain = example.org 를 지정해서 쿠키 생성
        - example.org 및 dev.example.org 모두 접근 가능
- 생략 : 현재 문서 기준 도메인만 적용
    - 쿠키 생성하고, domain 지정을 생략
        - example.org만 쿠키 접근 가능

### 쿠키 - 경로

- 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
- 일반적으로 Path=/ 루트로 지정
- 예시 - path=/home
    - /home, /home/level1, → 가능
    - /hello → 불가능

### 쿠키 - 보안

- Secure
    - 쿠키는 보통 http, https를 구분하지 않고 전송하지만, Secure를 적용하면 https인 경우에만 전송
- HttpOnly
    - XSS 공격 방지
    - 자바스크립트에서 접근 불가
    - HTTP 전송에만 사용
- SameSite
    - XSRF 공격 방지
    - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우에만 쿠키 전송

### 공유하고 싶은 내용

### 쿠키와 세션의 차이점

- 저장 위치
    - 쿠키 : 클라이언트
    - 세션 : 서버
- 보안
    - 쿠키 : 클라이언트에 저장되므로 보안에 취약하다.
    - 세션 : 쿠키를 이용해 Session ID만 저장하고 이 값으로 구분해서 서버에서 처리하므로 비교적 보안성이 좋다.
- 라이프사이클
    - 쿠키 : 만료시간에 따라 브라우저를 종료해도 계속해서 남아 있을 수 있다.
    - 세션 : 만료시간을 정할 수 있지만 브라우저가 종료되면 만료시간에 상관없이 삭제된다.
- 속도
    - 쿠키 : 클라이언트에 저장되어서 서버에 요청 시 빠르다.
    - 세션 : 실제 저장된 정보가 서버에 있으므로 서버의 처리가 필요해 쿠키보다 느리다.