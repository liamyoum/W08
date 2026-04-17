# C 언어로 구현하는 Web Server & Proxy Lab

이 저장소는 C 언어로 웹 서버와 프록시 서버를 직접 구현해 보며 네트워크 프로그래밍의 핵심 개념을 익히는 과제용 레포지토리입니다. 중심 주제는 소켓 프로그래밍이며, BSD 소켓 API부터 HTTP 요청/응답 처리, 정적/동적 컨텐츠 제공, 그리고 프록시의 동시성 및 캐시까지 단계적으로 다룹니다.

핵심 실습은 `webproxy-lab/tiny`의 Tiny 웹 서버를 완성하고, 이후 `webproxy-lab/proxy.c`에서 프록시 서버를 구현하는 흐름으로 진행합니다.

## 학습 키워드

- BSD Socket
- IP
- TCP
- HTTP
- File Descriptor
- DNS

## GOAL

소켓 이해 → 에코 서버/클라이언트 실습 → Tiny 웹 서버 구현 → CS:APP 11장 연습문제 풀이 → Proxy 과제 도전

최종 목표는 아래를 직접 구현하고 설명할 수 있는 상태에 도달하는 것입니다.

- 소켓을 사용해 요청과 응답이 오가는 간단한 클라이언트/서버 구조를 이해한다.
- HTTP 프로토콜을 이해하고 클라이언트의 request를 받아 response를 보내는 웹 서버를 구현한다.
- Tiny 웹 서버의 전체 흐름을 직접 완성한다.
- 브라우저와 웹 서버 사이에서 동작하는 프록시 서버를 구현한다.

## 권장 학습 순서

1. 소켓이 무엇인지 공부합니다.
2. 소켓을 이용해 간단한 에코 클라이언트/서버를 만들어 봅니다.
3. HTTP 프로토콜의 request/response 구조를 이해합니다.
4. `webproxy-lab/tiny/tiny.c`를 바탕으로 Tiny 웹 서버를 완성합니다.
5. `webproxy-lab/tiny/cgi-bin/adder.c`를 포함한 CGI 동작을 이해합니다.
6. CS:APP 11장 연습문제 `11.6c`, `11.7`, `11.9`, `11.10`, `11.11` 중 3문제 이상 풉니다.
7. `proxy.c`를 완성하여 순차 처리, 병렬 처리, 캐시 기능을 구현합니다.

어려우면 CS:APP(Computer Systems: A Programmer's Perspective) 11장을 보면서 천천히 따라가는 방식으로 진행하면 됩니다. 이 과제에서는 교재 11장에 등장하는 Tiny 웹 서버 기능을 모두 구현하는 것이 중요합니다.

## 웹 서버는 어떤 기능들의 모음인가?

Tiny를 구현할 때는 웹 서버를 하나의 큰 프로그램이 아니라 여러 기능의 조합으로 보는 것이 좋습니다.

- TCP 연결을 수립하기 위한 listening socket 생성
- 클라이언트 연결 수락과 연결별 file descriptor 관리
- HTTP request line 및 header 파싱
- URI 분석 후 정적 컨텐츠와 동적 컨텐츠 구분
- 파일 읽기와 응답 본문 전송
- CGI 프로그램 실행을 통한 동적 응답 생성
- 오류 상황에 대한 적절한 HTTP 에러 응답 전송

이 과정에서 BSD 소켓, 파일 입출력, 프로세스/디스크립터, 문자열 처리, DNS/호스트 이름 처리 개념이 자연스럽게 연결됩니다.

## 과제 범위

### 1. Tiny 웹 서버 완성

아래 파일을 중심으로 Tiny 웹 서버를 완성합니다.

- `webproxy-lab/tiny/tiny.c`
- `webproxy-lab/tiny/cgi-bin/adder.c`

Tiny는 GET 요청을 처리하며, 정적 파일과 CGI 기반 동적 컨텐츠를 모두 다룹니다. 레포에는 테스트용 정적 파일(`home.html`, `godzilla.jpg`, `godzilla.gif`)과 CGI 예제(`cgi-bin/adder.c`)가 포함되어 있습니다.

### 2. CS:APP 11장 연습문제

다음 문제 중 3문제 이상 풉니다.

- `11.6c`
- `11.7`
- `11.9`
- `11.10`
- `11.11`

### 3. Proxy 서버 구현

Tiny를 완성한 뒤 아래 파일에서 프록시 서버 과제에 도전합니다.

- `webproxy-lab/proxy.c`

프록시는 브라우저와 웹 서버 사이에 위치합니다.

- 브라우저는 웹 서버 대신 프록시에 접속합니다.
- 프록시는 브라우저의 요청을 웹 서버로 포워딩합니다.
- 웹 서버의 응답을 받은 뒤, 프록시는 그 응답을 다시 브라우저에 전달합니다.

구현 목표는 다음 3가지입니다.

1. 순차적 요청 처리
2. 병렬적 요청 처리
3. 요청 캐시

## 레포지토리 구조

```text
webproxy_lab_docker/
├── README.md
└── webproxy-lab
    ├── Makefile
    ├── README.md
    ├── csapp.c
    ├── csapp.h
    ├── driver.sh
    ├── free-port.sh
    ├── nop-server.py
    ├── port-for-user.pl
    ├── proxy.c
    └── tiny
        ├── Makefile
        ├── README
        ├── csapp.c
        ├── csapp.h
        ├── godzilla.gif
        ├── godzilla.jpg
        ├── home.html
        ├── tiny.c
        └── cgi-bin
            ├── Makefile
            └── adder.c
```

## 빌드 및 실행

### Tiny 웹 서버 빌드

```bash
cd webproxy-lab/tiny
make
```

### Tiny 웹 서버 실행

```bash
./tiny 8000
```

테스트 예시:

```bash
curl http://localhost:8000/
curl http://localhost:8000/home.html
curl "http://localhost:8000/cgi-bin/adder?a=1&b=2"
```

### Proxy 서버 빌드

```bash
cd webproxy-lab
make
```

### Proxy 서버 실행

```bash
./proxy 4500
```

프록시 테스트 예시:

```bash
curl -x http://localhost:4500 http://localhost:8000/home.html
```

### 사용 가능한 포트 찾기

포트 충돌이 나면 아래 스크립트를 활용할 수 있습니다.

```bash
cd webproxy-lab
./free-port.sh
./port-for-user.pl <userID>
```

`free-port.sh`는 현재 비어 있는 포트를 찾고, `port-for-user.pl`은 사용자별로 충돌 가능성이 낮은 포트를 계산해 줍니다.

## 검증

프록시 구현 검증은 `driver.sh`를 기준으로 진행할 수 있습니다.

```bash
cd webproxy-lab
./driver.sh
```

자동 채점 스크립트는 아래 항목을 확인합니다.

- Basic: 프록시를 통해 가져온 결과가 원본 Tiny 서버 응답과 같은지 확인
- Concurrency: 응답이 지연되는 서버가 있어도 다른 요청을 처리할 수 있는지 확인
- Cache: 원 서버가 내려간 뒤에도 캐시된 객체를 제공할 수 있는지 확인

현재 스크립트 기준 배점은 총 70점입니다.

- Basic 40점
- Concurrency 15점
- Cache 15점

## 참고 자료

- CS:APP Chapter 11
- [Proxy Lab PDF](http://csapp.cs.cmu.edu/3e/proxylab.pdf)
- `webproxy-lab/README.md`
- `webproxy-lab/tiny/README`

## 정리

이 과제의 핵심은 단순히 서버를 띄우는 것이 아니라, 네트워크 프로그램이 실제로 어떻게 요청을 받고 해석하고 전달하는지 직접 구현해 보는 데 있습니다. Tiny 웹 서버를 완성하고, 연습문제를 풀며 개념을 다진 뒤, 순차 처리, 병렬 처리, 캐시를 갖춘 프록시 서버까지 확장해 보세요.
