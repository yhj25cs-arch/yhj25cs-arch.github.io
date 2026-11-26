---
title: "DocCodeCheckerMCP 개발 프로젝트"
date: 2026-05-23 12:00:00 +0900
categories: ["개발"]
tags: [Python, MCP, Swagger, API, Automation]
---
---
title: "DocCodeCheckerMCP 개발 프로젝트"
date: 2026-11-27 12:00:00 +0900
categories: ["개발"]
tags: [Python, MCP, Swagger, API, Automation]
---

## 1. 프로젝트 개요

DocCodeCheckerMCP는 API 명세 문서와 실제 구현된 코드 사이의 불일치를 확인하기 위해 개발한 프로젝트이다.

Swagger 문서에 정의된 API 관련 요소와 Python 코드 내부에서 사용되는 함수명, 변수명 등의 토큰을 추출하고 비교하여 서로 일치하지 않는 항목을 출력하도록 구성했다.

## 2. 개발 목적

프로그램을 개발하는 과정에서 코드가 수정되더라도 기존 API 명세 문서에는 변경 사항이 반영되지 않을 수 있다.

이러한 경우 문서에 명시된 내용과 실제 구현된 코드 사이에 차이가 발생한다.

이를 직접 하나씩 비교하는 대신 MCP를 활용하여 문서와 코드의 불일치 항목을 빠르게 확인할 수 있도록 하고자 했다.

## 3. 사용 기술

| 기술 | 역할 |
|---|---|
| Python | 서버 및 핵심 기능 구현 |
| MCP | 도구 제공 및 호출 인터페이스 |
| Swagger | API 명세 문서 |
| JSON | API 명세 및 도구 스키마 정의 |
| stdio | MCP 서버 통신 방식 |

## 4. MCP란?

MCP(Model Context Protocol)는 AI 애플리케이션이 외부 데이터나 도구에 접근하고 이를 활용할 수 있도록 연결하는 표준 프로토콜이다.

이번 프로젝트에서는 API 명세 분석, 코드 분석, 비교 기능을 각각 MCP Tool로 구성했다.

## 5. 주요 기능

### 5.1. read_swagger_spec

로컬 환경에 저장된 `swagger.json` 파일을 읽고 API 명세에 정의된 관련 키워드를 추출한다.

### 5.2. extract_code_tokens

Python 소스 코드 파일을 읽고 코드 내부에서 사용되는 함수명, 변수명 등의 토큰을 추출한다.

### 5.3. compare_spec_and_code

Swagger 문서에서 추출한 키워드와 Python 코드에서 추출한 토큰을 비교한다.

서로 일치하지 않는 항목을 반환하여 문서와 코드 사이의 차이를 확인할 수 있도록 한다.

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
| server | MCP 서버 실행 및 도구 요청 처리 |
| core | 문서 분석, 코드 분석, 비교 기능 |
| adapters | 파일 읽기 기능 |
| tests | 기능 테스트 |
| schemas | MCP 도구별 스키마 정의 |

서버 요청 처리와 핵심 분석 기능, 파일 입출력 기능을 분리하여 구성했다.

## 7. 동작 과정

1. Swagger 문서와 Python 코드 파일을 읽는다.
2. Swagger 문서에서 API 관련 키워드를 추출한다.
3. Python 코드에서 사용되는 토큰을 추출한다.
4. 두 결과를 비교한다.
5. 서로 일치하지 않는 항목을 반환한다.

## 8. 한계 및 개선 방향

현재 프로젝트는 문서에서 추출한 키워드와 코드의 토큰을 비교하는 방식이다.

따라서 동일한 키워드가 존재하더라도 실제 API의 동작까지 일치한다고 판단할 수는 없다. 반대로 서로 다른 이름을 사용하더라도 동일한 역할을 수행하는 경우가 존재할 수 있다.

향후에는 다음과 같은 방향으로 기능을 개선할 수 있다.

- HTTP 메서드 및 API 경로 비교
- 요청 및 응답 파라미터 검증
- AST(Abstract Syntax Tree)를 활용한 코드 구조 분석
- 불일치 항목에 대한 상세한 설명 제공
- 다양한 프로그래밍 언어 지원

## 9. 배운 점

이번 프로젝트를 통해 MCP를 활용하여 특정 기능을 도구 형태로 제공하는 구조를 학습할 수 있었다.

또한 파일을 읽고 필요한 정보를 추출하는 기능과 이를 비교하는 기능을 분리하여 프로그램을 구성하는 방법을 경험했다.

단순한 키워드 비교 방식에는 한계가 존재하기 때문에 정확한 분석을 위해서는 코드의 구조와 의미까지 고려할 필요가 있다고 생각한다.

## 10. GitHub Repository

[DocCodeCheckerMCP 프로젝트 소스 코드](https://github.com/yhj25cs-arch/DocCodeCheckerMCP)
