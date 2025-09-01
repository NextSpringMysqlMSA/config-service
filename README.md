
# Spring Cloud Config 서버 동작 흐름

Spring Cloud Config Server는 마이크로서비스 아키텍처(MSA) 환경에서 각 서비스의 설정을 **Git 저장소에서 중앙 집중식으로 관리**할 수 있도록 도와주는 핵심 구성 요소입니다.  
다음 흐름은 Config 서버가 시작되고 클라이언트로부터 설정 요청을 받아 응답하기까지의 전체 과정을 나타냅니다.

---

## 주요 역할

| 단계 | 설명 |
|------|------|
| 서버 시작 | Spring Boot 애플리케이션이 부팅되며 Config Server 역할을 수행하도록 초기화 |
| Git 연결 | application.yml의 설정을 통해 원격 Git 저장소에 연결 |
| 설정 제공 | 클라이언트로부터 요청이 들어오면 해당 앱/프로파일에 맞는 설정을 Git에서 읽어와 JSON 형태로 응답 |

---

## 설정 요청 예시

```

GET [http://localhost:8888/{application}/{profile}](http://localhost:8888/{application}/{profile})

````

예: `http://localhost:8888/auth-service/dev`

---

## 작동 흐름도

```mermaid
flowchart TD
    %% 노드 정의
    start((Start))
    configInit[Spring Boot 시작]
    enableConfig[Config 서버 활성화]
    loadProps[application.yml 로드]
    connectGit[Git 저장소 연결]
    listenPort[8888 포트 대기]
    configReq[설정 요청: GET /app/profile]
    fetchConfig[Git 저장소에서 설정 조회]
    returnJson[설정 JSON 반환]
    endNode((End))

    %% 흐름 정의
    start --> configInit
    configInit --> enableConfig
    enableConfig --> loadProps
    loadProps --> connectGit
    connectGit --> listenPort
    listenPort --> configReq
    configReq --> fetchConfig
    fetchConfig --> returnJson
    returnJson --> endNode

    %% 색상 스타일 정의
    classDef forest fill:#e6f4ea,stroke:#2e7d32,stroke-width:1.5px,color:#2e7d32;
    classDef terminal fill:#d0f0c0,stroke:#1b5e20,color:#1b5e20;

    %% 클래스 적용
    class start,endNode terminal;
    class configInit,enableConfig,loadProps,connectGit,listenPort,configReq,fetchConfig,returnJson forest;
````

---

## 관련 설정 예시 (`application.yml`)

```properties
server.port=8888

spring.cloud.config.server.git.uri=https://github.com/NextSpringMysqlMSA/config-repo.git
spring.cloud.config.server.git.default-label=main

```

---

## 개발 포인트

* `application.yml`에 Git 연동 정보와 서버 포트를 명확히 설정
* Config 서버는 서비스 디스커버리(Eureka) 또는 Gateway와 통합되어 사용 가능
* 클라이언트는 `spring.config.import=optional:configserver:`을 통해 설정을 불러옴

