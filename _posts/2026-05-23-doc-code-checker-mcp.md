---
title: "DocCodeCheckerMCP 개발 프로젝트"
date: 2026-05-23 12:00:00 +0900
categories: ["개발"]
tags: [Python, MCP, Swagger, API, HTTP, Render, Automation]
---

## 1. 프로젝트 개요

DocCodeCheckerMCP는 API 명세와 실제 코드 구현 간의 불일치를 자동으로 검증하기 위해 개발한 MCP 프로젝트이다.

Swagger 파일과 실제 서버 코드를 자동으로 비교하고 서로 일치하지 않는 항목을 정리하여 반환하도록 설계했다.

## 2. 개발 배경 및 목적

API 개발 과정에서 Swagger 문서는 최신 상태이지만 실제 코드에는 변경 사항이 반영되지 않는 경우가 발생할 수 있다.

이러한 명세와 구현 간의 불일치는 API 사용 과정에서 오류를 발생시킬 수 있다.

이를 해결하기 위해 Swagger 문서와 서버 코드를 직접 하나씩 비교하는 대신 두 파일을 자동으로 분석하고 불일치 항목을 확인할 수 있는 MCP를 개발하고자 했다.

## 3. 사용 기술

| 기술 | 역할 |
|---|---|
| Python | 서버 및 핵심 기능 구현 |
| MCP | 외부 도구 제공 및 호출 인터페이스 |
| Swagger | API 명세 문서 |
| JSON | API 명세 및 도구 스키마 정의 |
| stdio | 표준 입출력을 이용한 MCP 통신 |
| HTTP | 외부에서 서버에 접근하기 위한 통신 방식 |
| Unit Test | 개별 기능의 정상 동작 검증 |
| Render | 서버 배포 |
| Git, GitHub | 소스 코드 및 버전 관리 |

## 4. 주요 개념

### 4.1. MCP

MCP(Model Context Protocol)는 AI 애플리케이션이 외부 데이터나 도구에 접근하고 이를 활용할 수 있도록 연결하는 표준 프로토콜이다.

이번 프로젝트에서는 API 명세 분석, 코드 분석, 비교 기능을 각각 MCP Tool로 구성하여 AI가 필요한 기능을 호출할 수 있도록 했다.

### 4.2. Swagger

Swagger는 API의 경로, 요청 방식, 파라미터, 응답 형식 등을 정의하고 문서화하는 데 사용되는 도구이다.

API 명세를 통해 개발자는 해당 API가 어떤 방식으로 동작해야 하는지 확인할 수 있다.

이번 프로젝트에서는 `swagger.json` 파일을 분석 대상으로 사용했다.

## 5. 주요 기능

### 5.1. read_swagger_spec

`swagger.json` 파일을 읽고 API 명세에 정의된 관련 키워드를 추출한다.

이를 통해 문서에 명시된 API 관련 정보를 확인할 수 있다.

### 5.2. extract_code_tokens

Python 소스 코드 파일을 읽고 코드 내부에서 사용되는 함수명, 변수명 등의 토큰을 추출한다.

추출된 토큰은 API 명세와 실제 코드의 불일치를 확인하기 위한 비교 대상으로 사용된다.

### 5.3. compare_spec_and_code

Swagger 문서에서 추출한 키워드와 Python 코드에서 추출한 토큰을 비교한다.

서로 일치하지 않는 항목을 반환하여 API 명세와 실제 코드 구현 사이의 차이를 확인할 수 있도록 한다.

## 6. 프로젝트 구조

```text
DocCodeCheckerMCP/
├── server/
│   ├── main.py
│   └── handlers.py
├── core/
│   ├── swagger_parser.py
│   ├── code_tokenizer.py
│   └── comparer.py
├── adapters/
│   └── file_loader.py
├── tests/
│   └── test_swagger_parser.py
└── schemas/
    ├── read_swagger_spec.json
    ├── extract_code_tokens.json
    └── compare_spec_and_code.json
```

| 디렉터리 | 역할 |
|---|---|
| `server/` | MCP 서버 실행 및 도구 요청 처리 |
| `core/` | 문서 분석, 코드 분석 및 비교 기능 |
| `adapters/` | 파일 읽기 기능 |
| `tests/` | 개별 기능 테스트 |
| `schemas/` | MCP 도구별 스키마 정의 |

서버 요청 처리, 핵심 분석 기능, 파일 입출력 기능을 분리하여 구성했다.

## 7. 동작 과정

1. Swagger 문서와 Python 코드 파일을 읽는다.
2. Swagger 문서에서 API 관련 키워드를 추출한다.
3. Python 코드에서 사용되는 토큰을 추출한다.
4. 추출된 키워드와 토큰을 비교한다.
5. 서로 일치하지 않는 항목을 반환한다.

세 가지 MCP Tool을 활용하여 API 명세와 실제 코드 구현 사이의 불일치 항목을 확인할 수 있도록 구성했다.

## 8. 서버 구축 및 배포

### 8.1. stdio 통신 구현

표준 입출력을 이용하여 MCP 서버와 클라이언트가 통신할 수 있도록 구현했다.

이를 통해 로컬 환경에서 MCP Tool을 호출하고 실행할 수 있도록 했다.

### 8.2. HTTP 서버 구축

로컬 환경에서만 사용하는 것에 그치지 않고 외부에서도 서버에 접근할 수 있도록 HTTP 서버 엔트리를 추가로 구축했다.

### 8.3. Unit Test

개별 기능의 정상 동작을 확인하기 위해 Unit Test를 작성했으며 테스트를 통과했다.

### 8.4. Render 배포

Render를 이용하여 서버를 배포했다.

배포에 필요한 환경을 설정하고 외부에서 접근할 수 있는 서버 URL을 생성했다.

## 9. 프로젝트 결과

- MCP stdio 통신 방식 구현 완료
- HTTP 서버 엔트리 추가 구축
- Unit Test 작성 및 통과
- Render 배포 성공
- 외부에서 접근 가능한 서버 URL 생성 완료

API 명세와 실제 코드의 불일치를 확인하는 MCP Tool을 구현하고 서버 구축과 테스트 및 배포까지 진행했다.

## 10. 한계 및 향후 계획

현재 프로젝트는 단순 키워드 기반으로 문서와 코드를 비교하기 때문에 실제 API의 동작까지 검증하는 것은 불가능하다는 한계가 있다.

문서와 코드에 동일한 키워드가 존재하더라도 실제로 명세에 맞게 동작하는지는 확인할 수 없다.

향후에는 이러한 한계를 보완하여 좀 더 복잡한 구조의 API에도 활용할 수 있는 MCP를 개발하고 싶다.

## 11. 배운 점 및 느낀 점

이번 프로젝트를 통해 MCP 서버의 구조와 기능에 대한 이해도를 높일 수 있었다.

또한 MCP Tool을 구성하고 서버를 직접 구축하여 배포하는 과정을 경험했다.

특히 서버 구축 및 배포 과정에서 환경 설정의 중요성을 체감했다.

단순 키워드 기반 비교 방식의 한계도 확인할 수 있었으며 앞으로는 더욱 복잡한 구조의 API에도 활용할 수 있도록 기능을 발전시키고 싶다.

## 12. GitHub Repository

[DocCodeCheckerMCP 프로젝트 소스 코드](https://github.com/yhj25cs-arch/DocCodeCheckerMCP)
