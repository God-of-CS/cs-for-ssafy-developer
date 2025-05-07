# 🌐 DNS와 HTTP, 그리고 URI/URL/URN 정리

## 1. DNS (Domain Name System)
> 도메인 이름을 IP 주소로 변환하는 분산 데이터베이스 시스템.

### ❓ `www.google.com` 입력 시 동작 과정
1.  **로컬 캐시 확인:** PC (브라우저 캐시, OS 캐시), 내부 네트워크 `hosts` 파일 조회.
2.  **Local DNS 서버 (리졸버)에 질의:** ISP 제공 DNS 서버. 자체 캐시 확인.
3.  **Root → TLD → Authoritative 서버 순차 질의:**
    * 리졸버 → Root 네임 서버: ".com 관리자 문의"
    * Root 서버 → TLD (최상위 도메인) 네임 서버 주소 응답
    * 리졸버 → TLD 네임 서버: "google.com 관리자 문의"
    * TLD 서버 → 권한 있는(Authoritative) 네임 서버 주소 응답
    * 리졸버 → 권한 있는 네임 서버: "www.google.com IP 주소 문의" → 최종 IP 주소 수신
4.  **결과 전달 및 캐싱:** 리졸버는 PC에 IP 주소 전달 및 정보 캐싱.
    * 📌 **트러블슈팅**: `nslookup`, `dig` 명령어로 DNS 조회 확인.

### 📋 DNS 레코드
* `A`: 도메인 이름 → IPv4 주소
* `AAAA`: 도메인 이름 → IPv6 주소
* `CNAME`: 도메인을 다른 도메인의 별칭으로 사용 (예: `blog.example.com` → `gh-pages.github.io`)
* `MX`: 해당 도메인의 메일 서버 지정
* `TXT`: 도메인 소유 확인, 이메일 보안(SPF, DKIM) 등에 임의 텍스트 값 설정

---

### 🔖 URI, URL, URN

* **URI (Uniform Resource Identifier: 통합 자원 식별자)**
    * 인터넷 상 자원을 **고유하게 식별**하는 문자열 (가장 포괄적).
    * 위치(URL)나 이름(URN) 정보를 포함할 수도, 안 할 수도 있음.
    * 📌 **URL과 URN은 모두 URI의 하위 집합.**
    * 예: `https://example.com/path`, `mailto:user@example.com`, `urn:isbn:9780134685991`

* **URL (Uniform Resource Locator: 통합 자원 위치 지정자)**
    * 자원의 **위치(어디에 있는지)** 와 접근 방법(프로토콜)을 명시하는 URI.
    * 일반적인 '웹 주소'.
    * 예: `https://www.google.com`, `ftp://example.com/download/file.zip`

* **URN (Uniform Resource Name: 통합 자원 이름)**
    * 자원의 **이름**을 부여하여 고유하게 식별하는 URI (위치 정보 X).
    * 자원 위치 변경돼도 이름은 불변 (영구 식별자 목표).
    * `urn:` 스키마 사용.
    * 예: `urn:isbn:9780134685991`, `urn:uuid:6e8bc430-9c3a-11d9-9669-0800200c9a66`

> **결론:** URI는 식별자(Identifier), URL은 위치(Locator), URN은 이름(Name). 웹 개발에서는 주로 URL을 다룸.

---

### 🔍 URL 구조
`scheme://host[:port]/path[?query][#fragment]`

* **scheme:** 사용할 프로토콜 (`http`, `https`, `ftp` 등)
* **host:** 자원이 위치한 서버의 도메인 이름 또는 IP 주소
* **port:** 서버 접속 포트 번호 (http: 80, https: 443 기본, 생략 가능)
* **path:** 서버 내 자원의 경로 (예: `/users/1`)
* **query:** 서버에 전달하는 추가 파라미터 (`key=value` 형태, `&`로 구분. 예: `?q=network&page=1`)
* **fragment:** 문서 내 특정 위치를 가리킴 (브라우저에서만 사용, 서버로 전달 X. 예: `#section-2`)

---

## 2. HTTP (HyperText Transfer Protocol)
> DNS로 IP 주소 확인 후, 클라이언트와 서버가 데이터를 주고받기 위한 통신 규약. **요청(Request)**과 **응답(Response)** 쌍으로 구성.

### 📤 HTTP 요청 (클라이언트 → 서버)
* **시작 줄 (Start Line):** `[메서드] [요청 경로] [HTTP 버전]` (예: `GET /users/123 HTTP/1.1`)
* **헤더 (Headers):** 요청 메타데이터 (`Host`, `User-Agent`, `Accept`, `Content-Type`, `Authorization` 등)
* **본문 (Body):** 실제 전송할 데이터 (`POST`, `PUT`, `PATCH` 요청 시 사용. 예: JSON 페이로드)

#### ⚙️ HTTP 메서드 (요청의 의도)
| 메서드  | 설명                     | 멱등성 |
|:-------|:-------------------------|:------:|
| `GET`    | 리소스 조회              | O      |
| `POST`   | 리소스 생성              | X      |
| `PUT`    | 리소스 전체 교체 또는 생성 | O      |
| `PATCH`  | 리소스 부분 수정         | 논란   |
| `DELETE` | 리소스 삭제              | O      |
* 기타: `HEAD`, `OPTIONS` 등

### 📥 HTTP 응답 (서버 → 클라이언트)
* **상태 줄 (Status Line):** `[HTTP 버전] [상태 코드] [상태 메시지]` (예: `HTTP/1.1 200 OK`)
* **헤더 (Headers):** 응답 관련 메타데이터 (`Content-Type`, `Content-Length`, `Set-Cookie`, `Location` 등)
* **본문 (Body):** 요청한 데이터(HTML, JSON, 이미지 등) 또는 오류 메시지.

#### 🔢 HTTP 상태 코드 (요청 결과 요약)
* `2xx` (성공): `200 OK`, `201 Created`, `204 No Content`
* `3xx` (리다이렉션): `301 Moved Permanently`, `302 Found`, `304 Not Modified`
* `4xx` (클라이언트 오류): `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`
* `5xx` (서버 오류): `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`

### ✨ HTTP의 주요 특징
* **Stateless (무상태성):** 각 HTTP 요청은 이전 요청과 독립적. 서버는 클라이언트 상태 저장 X.
    * 📌 상태 유지를 위해 쿠키, 세션, 토큰(JWT 등) 사용.
* **Connectionless (비연결성):** 기본적으로 요청/응답 후 연결 해제.
    * 📌 HTTP/1.1의 Keep-Alive, HTTP/2에서 개선.

---

### 🚀 HTTP/1.1, HTTP/2, HTTP/3

**HTTP/1.1 (1997년)**
* 텍스트 기반 프로토콜.
* **연결 유지 (Keep-Alive):** TCP 연결 재사용.
* **파이프라이닝 (Pipelining):** 이론적, 실제 사용 미미 (HOL Blocking 문제).
* **단점:**
    * **HOL (Head-of-Line) Blocking:** 하나의 TCP 연결에서 앞선 요청 지연 시 후속 요청 대기.
    * **비효율적인 헤더:** 텍스트 형태, 중복 전송 오버헤드.

**HTTP/2 (2015년)**
* **바이너리 프로토콜:** 메시지를 바이너리 프레임(Frame)으로 처리.
* **멀티플렉싱 (Multiplexing):** 📌 핵심 개선. 단일 TCP 연결에서 여러 요청/응답 동시, 비순차적 처리 (HTTP 계층 HOL Blocking 해결).
* **스트림 우선순위 (Stream Prioritization):** 중요 리소스 우선 전송.
* **서버 푸시 (Server Push):** 클라이언트 요청 전 리소스 미리 전송 (현재 사용 감소 추세).
* **헤더 압축 (HPACK):** 헤더 전송 오버헤드 감소.
* 📌 **참고:** TCP 계층의 HOL Blocking은 여전히 존재.

**HTTP/3 (2022년)**
* **QUIC 프로토콜 사용:** UDP 기반 전송 계층 프로토콜.
* **TCP HOL Blocking 해결:** QUIC 스트림은 독립적, 패킷 손실 시 다른 스트림 영향 X.
* **연결 설정 속도 향상:** TCP+TLS 핸드셰이크 통합 (0-RTT 또는 1-RTT).
* **연결 마이그레이션 (Connection Migration):** 클라이언트 IP/포트 변경 시 연결 유지.
* **강화된 보안:** TLS 1.3 기반 암호화 기본 제공.

#### 📊 HTTP 버전 요약 비교

| 특징             | HTTP/1.1                 | HTTP/2                       | HTTP/3                          |
|:-----------------|:--------------------------|:-----------------------------|:--------------------------------|
| **기반 프로토콜**| TCP                       | TCP                          | **QUIC (UDP 기반)** |
| **메시지 형식** | 텍스트                    | 바이너리                     | 바이너리                        |
| **HOL Blocking** | HTTP/TCP 모두 존재        | TCP 계층에 존재              | **해결** |
| **동시 전송** | 파이프라이닝 (제한적)     | **멀티플렉싱** | **멀티플렉싱 (QUIC)** |
| **헤더 처리** | 비압축, 중복              | HPACK 압축                   | QPACK 압축                      |
| **연결 설정** | TCP + TLS 핸드셰이크 (느림) | TCP + TLS 핸드셰이크 (느림)    | **QUIC 핸드셰이크 (빠름)** |
| **암호화** | 선택 (HTTPS)              | 필수 (사실상)                | **필수** |
| **연결 유지** | Keep-Alive                | 단일 연결                    | **연결 마이그레이션 지원** |
