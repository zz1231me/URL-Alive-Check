# URL_Alive_Check

## 🎯 개요

URL의 접속 상태를 자동으로 확인하는 도구입니다.
- **Playwright** 브라우저로 실제 접속 테스트
- **프록시 사용/미사용** 각각 테스트
- **http/https × www/non-www** 변형 자동 생성 및 테스트
- 모든 HTTP 상태 코드 기록

## ✨ 주요 기능

### 테스트 시나리오
각 URL에 대해 다음 방식으로 테스트합니다:

1. **Direct + Playwright**: 직접 접속 (브라우저)
2. **Proxy + Playwright**: 프록시 접속 (브라우저, 프록시 활성화 시)

각 URL은 `http/https × www/non-www` 최대 4가지 변형으로 자동 테스트됩니다.

### 상세 결과 기록
- HTTP 상태 코드 (200, 404, 500 등)
- 응답 시간
- 오류 메시지
- JavaScript 오류
- 네트워크 오류
- 리다이렉트 정보

## 📦 설치

### 1. Python 패키지 설치
```bash
pip install -r requirements.txt
```

### 2. Playwright 브라우저 설치
```bash
playwright install chromium
```

## 🚀 사용법

### 기본 사용법
```bash
python url_alive_check.py --input sites.txt --output results.xlsx
```

### 설정 파일 사용
```bash
python url_alive_check.py --input sites.txt --config config.json --output results.xlsx
```

### 단일 URL 테스트
```bash
python url_alive_check.py --url https://example.com
```

### 순차 실행 (명시적 지정)
```bash
python url_alive_check.py --input sites.txt --sequential
```

## ⚙️ 설정 파일 (config.json)

```json
{
  "proxy": {
    "enabled": false,
    "server": "http://proxy-server.com:8080",
    "use_auth": false,
    "username": "",
    "password": ""
  },
  "timeout": 30,
  "headless": true,
  "user_agent": "Mozilla/5.0 ...",
  "retry_count": 0,
  "retry_delay": 5
}
```

### 설정 항목 설명

| 항목 | 설명 | 기본값 |
|------|------|--------|
| `proxy.enabled` | 프록시 사용 여부 | `false` |
| `proxy.server` | 프록시 서버 주소 | - |
| `proxy.use_auth` | 프록시 인증 사용 여부 | `false` |
| `proxy.username` | 프록시 인증 ID | - |
| `proxy.password` | 프록시 인증 PW | - |
| `timeout` | 타임아웃 (초) | `30` |
| `headless` | 헤드리스 모드 | `true` |
| `user_agent` | User-Agent 문자열 | Mozilla/5.0... |
| `retry_count` | 실패 시 재시도 횟수 | `0` |
| `retry_delay` | 재시도 대기 시간 (초) | `5` |

## 📄 URL 목록 파일 (sites.txt)

```
# 주석은 '#'으로 시작합니다
https://example1.com
https://example2.com
https://example3.com
```

## 📊 결과 파일 (results_YYMMDD.xlsx)

결과는 실행 날짜가 포함된 XLSX 파일로 저장됩니다. (예: `results_260308.xlsx`)

### XLSX 컬럼 구조

| 컬럼 | 설명 |
|------|------|
| `No` | 순번 |
| `Original_URL` | 입력한 원본 URL |
| `Accessible_URL` | 실제 접속 성공한 URL |
| `접속가능` | 접속 가능 여부 (O / X) |
| `Status` | 최종 상태 |
| `Response_Time(s)` | 응답 시간 (초) |
| `Source` | 성공한 접속 방법 (direct / proxy) |
| `Timestamp` | 테스트 시간 |
| `Issues` | 발견된 문제점 |

### 상태 코드

- `SUCCESS`: 정상 접속 (2xx)
- `REDIRECT`: 리다이렉트 발생
- `WARNING`: 접속은 됐으나 JS/네트워크 오류 있음
- `ERROR`: 오류 (4xx, 5xx)
- `TIMEOUT`: 타임아웃
- `SKIPPED`: 테스트 생략 (프록시 비활성화 등)

## 📈 실행 결과 예시

```
╔══════════════════════════════════════════════════════════════════╗
║  URL_Alive_Check  v2.1
║  URL 접속 상태 확인
║
║  URL → http/https × www/non-www × Direct/Proxy
║  2026-03-08 10:00:00
╚══════════════════════════════════════════════════════════════════╝

┌──────────────────────────────────────────────────────────────────┐
│  🔍 https://naver.com  [1/3]
│    2개 변형 × 1개 시나리오 = 2개 테스트
├──────────────────────────────────────────────────────────────────┤
│    ROUTE    STATUS        TIME    URL VARIANT
├──────────────────────────────────────────────────────────────────┤
  │  [1/2] https://naver.com
  │    DIRECT   [● SUCCESS ]    0.82s
  │             [● SUCCESS ]  판정
  │·····················································
  │  [2/2] https://www.naver.com
  │    DIRECT   [↪ REDIRECT]    0.65s
  │             [↪ REDIRECT]  판정
├──────────────────────────────────────────────────────────────────┤
│  종합 판정  [● SUCCESS ]
└──────────────────────────────────────────────────────────────────┘

  ●  XLSX 저장 완료 : results_260308.xlsx  (3행)

┌──────────────────────────────────────────────────────────────────┐
│  완료
├──────────────────────────────────────────────────────────────────┤
│  소요 시간  : 12.45초
│  저장 파일  : results_260308.xlsx
│  ●  모든 작업이 완료되었습니다.
└──────────────────────────────────────────────────────────────────┘
```

## 🔧 고급 사용법

### 프록시 설정 예시

#### 인증 없는 프록시
```json
{
  "proxy": {
    "enabled": true,
    "server": "http://proxy.example.com:8080",
    "use_auth": false
  }
}
```

#### 인증 있는 프록시
```json
{
  "proxy": {
    "enabled": true,
    "server": "http://proxy.example.com:8080",
    "use_auth": true,
    "username": "myuser",
    "password": "mypass"
  }
}
```

### 헤드풀 모드 (브라우저 화면 표시)
```json
{
  "headless": false
}
```

### 재시도 설정
```json
{
  "retry_count": 2,
  "retry_delay": 3
}
```

## 📋 판정 시나리오

### 시나리오 1: 완벽 정상
```
Direct Playwright: ✅ SUCCESS (200)

→ 판정: SUCCESS
```

### 시나리오 2: JavaScript 오류
```
Direct Playwright: ⚠ WARNING (200 + JS error)

→ 판정: WARNING
→ 원인: JavaScript 실행 오류
```

### 시나리오 3: 프록시 문제 (프록시 활성화 시)
```
Direct Playwright: ✅ SUCCESS (200)
Proxy  Playwright: ❌ TIMEOUT

→ 판정: SUCCESS  (Direct 성공 기준)
→ Issues: Proxy connection issue
```

### 시나리오 4: 사이트 오류
```
Direct Playwright: ❌ ERROR (404)

→ 판정: ERROR
→ 원인: 페이지 없음
```

## 🐛 문제 해결

### Playwright 설치 오류
```bash
# Windows
playwright install chromium --with-deps

# Linux
sudo playwright install chromium --with-deps
```

### 프록시 인증 오류
- `config.json`의 `use_auth`를 `true`로 설정했는지 확인
- `username`과 `password` 값 확인
- 프록시 서버 주소 형식 확인 (`http://host:port`)

### 타임아웃 증가
```json
{
  "timeout": 60
}
```

## 📝 라이선스

MIT License

## 🤝 기여

버그 리포트와 기능 제안을 환영합니다!

## 📮 문의

이슈 트래커를 통해 문의해주세요.
