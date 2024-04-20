## 20.1 WebClient란?
Spring 5부터 지원하는 Non-Blocking HTTP request를 위한 리액티브 웹 클라이언트로서 함수형 기반의 향상된 API를 제공합니다.

WebClient는 내부적으로 HTTP 클라이언트 라이브러리에게 HTTP request를 위임하며, 기본 HTTP 클라이언트 라이브러리는 Reactor Netty입니다.

## 20.2 WebClient로 도서 정보 요청하기
- retrieve(): response를 어떤 형태로 얻을지에 대한 프로세스의 시작을 선언하는 역할
- toEntity(Void.class): 파라미터로 주어진 클래스의 형태로 변환한 response body가 포함된 ResponseEntity 객체를 리턴합니다.

## 20.3 WebClient Connection Timeout 설정
특정 서버 엔진의 HTTP Client Connector 설정을 통해 HTTP Connection에 대한 TImeout을 설정할 수 있습니다.

## 20.4 exchangeToMono()를 사용한 응답 디코딩
retrieve() 대신에 exchangeToMono()나 exchangeToFlux() 메서드를 이용하면 response를 사용자의 요구 조건에 맞게 제어할 수 있습니다.