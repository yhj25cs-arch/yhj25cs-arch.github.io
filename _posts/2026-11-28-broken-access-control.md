---
title: "[OWASP Top 10 2025] Broken Access Control"
date: 2026-11-28 20:40:00 +0900
categories: ["블로그/기술문서"]
tags: [OWASP, Access Control, Authorization, IDOR, Privilege Escalation, Web Security]
---

## 1. OWASP Top 10이란?

OWASP(Open Worldwide Application Security Project)는 소프트웨어 및 애플리케이션의 보안 향상을 목적으로 활동하는 비영리 단체이며 OWASP Top 10은 웹 애플리케이션에서 중요하게 다루어야 하는 보안 위험을 10개의 항목으로 분류한 자료를 말한다. 개발자와 보안 담당자가 주요 보안 위험을 파악하고 이를 예방할 수 있도록 도움을 주는 것을 목표로 한다.

OWASP Top 10 중 하나인 Broken Access Control에 대해 공부해보겠다.

## 2. Broken Access Control 보안 동향

Broken Access Control은 2021년과 2025년 모두 OWASP Top 10에서 1위를 차지하였다.

OWASP Top 10 2025에서 발표한 관련 통계는 다음과 같다.

| 항목 | 수치 |
| --- | --- |
| 관련 CWE 개수 | 40개 |
| 평균 취약점 발생률 | 3.74% |
| 총 취약점 발생 건수 | 1,839,701건 |
| 관련 CVE 건수 | 32,654건 |

CWE(Common Weakness Enumeration)란? 소프트웨어와 하드웨어에서 발생할 수 있는 보안 약점의 유형을 분류한 것

CVE(Common Vulnerabilities and Exposures)란 공개적으로 알려진 개별 보안 취약점에 식별 번호를 부여하는 체계

-> 위 수치는 OWASP가 분류하고 집계한 자료를 기반으로 하며 실제 해킹 사고의 건수를 의미하는 것은 아님

2025년에도 접근 통제 실패가 1위를 유지하고 있다는 점에서 여전히 중요하게 다루어야 하는 보안 문제임을 알 수 있다.

## 3. Broken Access Control이란?

Broken Access Control은 접근 통제 실패를 의미한다.

접근 통제란 사용자가 허용된 범위 내에서만 정보에 접근하거나 특정 기능을 사용할 수 있도록 제한하는 것이다.

각 사용자는 자신의 권한에 해당하는 기능만 사용할 수 있어야 한다.

하지만 서버에서 사용자의 권한을 제대로 검증하지 않는다면 사용자가 다른 사용자의 개인정보를 확인하거나 관리자만 사용할 수 있는 기능에 접근하는 문제가 발생할 수 있다.

이처럼 사용자가 자신의 권한을 벗어나 정보에 접근하거나 기능을 실행할 수 있는 취약점을 Broken Access Control이라고 한다.

### 3.1. 인증과 인가의 차이

접근 통제를 이해하기 위해서는 인증과 인가의 차이를 먼저 알아야 한다.

| 구분 | 인증 | 인가 |
| --- | --- | --- |
| 영어 | Authentication | Authorization |
| 목적 | 사용자 신원 확인 | 사용자 권한 확인 |
| 확인 내용 | 누구인가? | 무엇을 할 수 있는가? |
| 예시 | 로그인 | 관리자 기능 접근 허용 |
| OWASP 2025 관련 항목 | A07: Authentication Failures | A01: Broken Access Control |

로그인에 성공하였더라도 모든 정보에 접근할 수 있는 것은 아니므로 별도의 권한 확인이 필요하다.

### 3.2. 수평적 권한 상승과 수직적 권한 상승

**수평적 권한 상승(Horizontal Privilege Escalation)**

동일한 권한 수준을 가진 다른 사용자의 정보나 기능에 접근하는 경우이다.

```text
user A ── 접근 허용 ── user A의 개인정보
user B ── 접근 허용 ── user B의 개인정보

user A ── 비인가 접근 ── user B의 개인정보
```

사용자의 권한 등급은 동일하지만 접근할 수 있는 정보의 범위를 벗어났다는 점이 특징이다.

**수직적 권한 상승(Vertical Privilege Escalation)**

자신보다 높은 권한을 가진 사용자의 기능에 접근하는 경우이다.

예를 들어 일반 사용자가 관리자만 사용할 수 있는 계정 관리 기능이나 시스템 설정 기능을 실행하는 경우이다.

```text
관리자
  ↑
일반 사용자가 관리자 기능에 접근
  ↑
일반 사용자
```

## 4. IDOR 취약점 분석

### 4.1. IDOR의 개념 및 발생 원리

IDOR(Insecure Direct Object Reference)는 안전하지 않은 직접 객체 참조를 의미한다.

웹 애플리케이션에서는 사용자 정보, 문서, 예약 내역 등의 데이터를 구분하기 위해 고유한 식별자를 사용한다.

서버가 해당 식별자로 데이터를 조회하면서 요청한 사용자에게 접근 권한이 있는지 확인하지 않으면 IDOR 취약점이 발생할 수 있다.

가상의 도서관 시스템에서 사용자가 자신의 도서 대출 내역을 조회한다고 가정해 보자.

| 대출 번호 | 소유자 |
| --- | --- |
| 101 | 사용자 A |
| 102 | 사용자 B |
| 103 | 사용자 C |

사용자 A는 자신의 대출 내역인 101번에만 접근할 수 있어야 한다.

하지만 서버가 요청받은 대출 번호만 확인하고 실제 소유자가 누구인지 검증하지 않는다면 다른 사용자의 대출 내역에도 접근할 수 있게 된다.

### 4.2. HTTP Request 분석

사용자 A가 자신의 대출 내역을 조회하면 브라우저에서 다음과 같은 HTTP Request가 전송된다고 가정하자.

```http
GET /api/loans/101 HTTP/1.1
Host: library.example
Cookie: session=<user_A_session>
```

- 101: 조회하려는 대출 내역의 식별자
- Cookie: 현재 로그인한 사용자의 세션 정보

서버는 세션 정보를 통해 사용자를 식별하고 대출 번호를 이용하여 데이터베이스에서 대출 내역을 조회한다.

정상적으로 요청이 처리되면 다음과 같은 응답을 반환한다.

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "loan_id": 101,
    "user_id": "A",
    "book": "Computer Security"
}
```

이때 서버에서 대출 번호와 세션 정보를 각각 확인하더라도 해당 사용자가 대출 내역의 실제 소유자인지 확인하지 않는다면 접근 통제 실패가 발생한다.

즉, 사용자 A의 세션으로 사용자 B의 대출 내역이 반환될 수 있는 것이다.

IDOR의 근본적인 원인은 식별자를 외부에서 확인하거나 변경할 수 있다는 사실 자체가 아니라 해당 데이터에 대한 접근 권한을 검증하지 않는다는 점이다.

### 4.3. 취약한 코드 분석

Python의 Flask를 이용하여 대출 내역을 조회하는 기능을 구현한다고 가정해 보자.

```python
@app.route("/api/loans/<int:loan_id>")
@login_required
def get_loan(loan_id):
    loan = Loan.query.get_or_404(loan_id)

    return jsonify({
        "loan_id": loan.id,
        "user_id": loan.user_id,
        "book": loan.book
    })
```

`@login_required`는 사용자가 로그인한 상태인지 확인한다.

그러나 로그인 여부만 확인할 뿐 해당 대출 내역에 접근할 권한까지 검증하지는 않는다.

취약점이 발생하는 핵심 부분은 다음과 같다.

```python
loan = Loan.query.get_or_404(loan_id)
```

사용자가 요청한 대출 번호만을 기준으로 데이터베이스에서 정보를 조회한다.

이를 SQL문의 형태로 표현하면 다음과 같다.

```sql
SELECT *
FROM loans
WHERE id = :loan_id;
```

대출 번호에 대한 조건만 존재하며 현재 로그인한 사용자의 ID에 대한 조건은 존재하지 않는다.

따라서 요청받은 대출 번호가 데이터베이스에 존재한다면 소유자와 관계없이 정보가 반환될 수 있다.

이러한 권한 검증 누락은 CWE-862(Missing Authorization), 사용자 제어 식별자를 통한 권한 우회는 CWE-639(Authorization Bypass Through User-Controlled Key)와 관련된다.

### 4.4. 개선된 코드 분석

```python
@app.route("/api/loans/<int:loan_id>")
@login_required
def get_loan(loan_id):
    loan = Loan.query.filter_by(
        id=loan_id,
        user_id=current_user.id
    ).first_or_404()

    return jsonify({
        "loan_id": loan.id,
        "user_id": loan.user_id,
        "book": loan.book
    })
```

개선된 코드에서는 대출 내역을 조회할 때 현재 로그인한 사용자의 ID를 추가로 확인한다.

```python
loan = Loan.query.filter_by(
    id=loan_id,
    user_id=current_user.id
).first_or_404()
```

`current_user.id`는 서버에서 확인한 현재 로그인 사용자의 ID이다.

이를 SQL문의 형태로 표현하면 다음과 같다.

```sql
SELECT *
FROM loans
WHERE id = :loan_id
  AND user_id = :current_user_id;
```

대출 번호와 사용자 ID가 모두 일치해야 데이터가 조회된다.

따라서 다른 사용자의 대출 번호를 요청하더라도 현재 로그인한 사용자의 정보와 일치하지 않기 때문에 데이터가 반환되지 않는다.

| 구분 | 취약한 코드 | 개선된 코드 |
| --- | --- | --- |
| 로그인 여부 확인 | O | O |
| 대출 번호 확인 | O | O |
| 소유자 확인 | X | O |
| 타인의 정보 접근 방지 | X | O |

이전에 공부했던 SQL Injection은 사용자 입력에 의해 SQL문의 구조가 변경되면서 발생하는 취약점이었다.

반면 위 사례에서는 SQL문이 정상적으로 실행되더라도 접근 권한을 확인하는 조건이 누락되어 있기 때문에 취약점이 발생한다.

위 코드는 대출 내역의 소유자만 해당 정보에 접근할 수 있는 가상의 서비스를 기준으로 작성하였다.

## 5. 실제 해킹 사례 - CVE-2023-22515

### 5.1. 사건 개요

| 항목 | 내용 |
| --- | --- |
| 취약점 번호 | CVE-2023-22515 |
| 공개일 | 2023년 10월 4일 |
| 대상 | Atlassian Confluence Data Center 및 Server |
| 취약점 유형 | Broken Access Control |
| CVSS 점수 | 9.8 (NVD 기준) |
| 위험도 | Critical |
| 주요 피해 | 비인가 관리자 계정 생성 |
| 실제 악용 여부 | 확인됨 |

Confluence는 기업이나 조직에서 문서를 작성하고 공유하며 협업할 수 있도록 지원하는 서비스이다.

기업 내부의 업무 자료, 기술 문서, 프로젝트 관련 정보 등을 관리하는 데 사용된다.

2023년 10월 Atlassian은 인증되지 않은 외부 공격자가 Confluence에서 관리자 계정을 생성할 수 있는 접근 통제 취약점을 공개하였다.

### 5.2. 취약점 발생 원인 및 공격 원리

Confluence를 처음 설치하면 시스템의 초기 설정을 진행하고 관리자 계정을 생성하는 과정이 필요하다.

초기 설정이 완료된 이후에는 외부 사용자가 해당 설정 기능을 임의로 실행할 수 없어야 한다.

그러나 취약한 버전에서는 외부 요청을 통해 시스템의 초기 설정 완료 상태에 영향을 줄 수 있었다.

초기 설정 완료 상태를 나타내는 값을 개념적으로 표현하면 다음과 같다.

```text
setupComplete = true
```

정상적인 경우 초기 설정이 완료되었으므로 관리자 계정 생성과 관련된 초기 설정 기능은 제한되어야 한다.

하지만 해당 상태가 비인가 요청에 의해 변경될 수 있었으며 그 결과 이미 설치가 완료된 시스템에서도 초기 설정 과정의 관리자 계정 생성 기능이 악용될 수 있었다.

```text
외부 사용자의 요청
        ↓
시스템 설정에 대한 접근 통제 실패
        ↓
초기 설정 완료 상태 변경
        ↓
비인가 관리자 계정 생성
        ↓
관리자 권한 획득
```

핵심적인 문제는 시스템의 초기 설정 상태를 변경할 수 있는 권한과 관리자 계정을 생성할 수 있는 권한이 충분하게 보호되지 않았다는 점이다.

초기 설정 여부를 나타내는 상태값은 기능의 실행 조건으로 사용될 수 있지만 해당 상태값만으로 사용자의 접근 권한을 판단해서는 안 된다.

앞서 분석한 IDOR는 객체에 대한 접근 권한 검증이 누락된 사례인 반면, CVE-2023-22515는 시스템 설정 및 관리자 기능에 대한 접근 통제 실패로 높은 권한을 획득할 수 있었던 사례이다.

### 5.3. 실제 악용 및 피해

| 날짜 | 내용 |
| --- | --- |
| 2023.10.04 | Atlassian이 취약점을 공개하고 보안 패치 배포 |
| 2023.10.05 | CISA가 실제 악용된 취약점 목록(KEV Catalog)에 등록 |
| 2023.10.16 | CISA, FBI, MS-ISAC 공동 보안 권고문 발표 |

공동 보안 권고문에 따르면 해당 취약점은 보안 패치가 공개되기 이전부터 공격에 악용되었다.

관리자 권한을 획득한 공격자는 기업 내부 문서에 접근하거나 시스템 설정을 변경할 수 있으며 악성 플러그인 설치 등을 통해 추가적인 공격을 수행할 가능성도 존재한다.

공동 보안 권고문에서는 해당 취약점 악용 이후 데이터 외부 유출에 사용된 기술도 보고하였다.

### 5.4. 대응 방법

Atlassian은 취약점이 수정된 버전으로 업데이트하도록 권고하였다.

공개 당시 제시된 수정 버전은 다음과 같다.

- 8.3.3
- 8.4.3
- 8.5.2

즉시 업데이트하기 어려운 경우에는 외부 네트워크에서 취약한 서버에 접근하지 못하도록 제한하는 임시 대응 방안이 제시되었다.

하지만 이미 침해가 발생하였다면 업데이트만으로 공격자가 생성한 관리자 계정이나 악성 파일 등이 제거되는 것은 아니다.

따라서 비정상적으로 생성된 관리자 계정, 알 수 없는 플러그인, 시스템 설정 변경 기록 및 접근 로그 등을 추가적으로 확인해야 한다.

해당 사례를 통해 일반적인 사용자 기능뿐만 아니라 초기 설정, 관리자 계정 생성, 시스템 설정 변경과 같이 특정 상황에서만 사용되는 기능도 접근 통제의 대상이 되어야 한다는 것을 알 수 있다.

## 6. 예방 및 대응 방법

### 6.1. 서버 측 접근 권한 검증

일반 사용자의 화면에서 관리자 메뉴를 숨기는 것만으로는 접근 통제가 이루어졌다고 볼 수 없다.

클라이언트는 사용자가 직접 제어할 수 있는 영역이므로 서버에서도 실제 접근 권한을 검증해야 한다.

또한 로그인 여부뿐만 아니라 요청한 자원에 접근할 권한이 있는지도 함께 확인해야 한다.

### 6.2. 최소 권한 및 기본 거부 원칙

- Least Privilege: 사용자에게 필요한 최소한의 권한만 부여
- Deny by Default: 명시적으로 허용된 접근을 제외하고 기본적으로 모든 접근을 거부

사용자의 역할과 권한에 따라 접근 범위를 제한하고 권한이 확인되지 않은 경우에는 요청을 거부하도록 설계해야 한다.

### 6.3. 객체 단위 접근 권한 검증

문서, 계정, 예약 내역 등 개별 데이터에 접근할 때마다 해당 사용자에게 접근 권한이 있는지 확인해야 한다.

식별자를 예측하기 어려운 값으로 변경하는 것은 추가적인 보호 수단이 될 수 있지만 권한 검증 자체를 대신할 수는 없다.

## 7. 학습을 통해 알게 된 점

이전에 WebGoat의 Authentication Bypasses 02 문제를 풀면서 Burp Suite를 활용한 인증 우회 실습을 진행한 경험이 있다. 비밀번호 재설정 과정에서 보안 질문에 대한 답변을 입력하고 Proxy의 Intercept 기능을 통해 HTTP Request를 확인하였다. 이후 해당 요청을 Repeater로 전송하여 매개변수를 변경하였고 서버에서 매개변수의 이름과 구조를 제대로 검증하지 않아 올바른 답변 없이도 본인 확인 절차를 우회할 수 있었다.

당시 진행한 실습은 인증 우회에 해당하지만 사용자가 전달한 HTTP Request를 서버에서 제대로 검증하지 않아 보안 문제가 발생한다는 점에서 접근 통제 실패와도 공통점이 있다고 생각하였다. 이번 학습을 통해 인증과 인가가 서로 다른 역할을 수행한다는 것을 알게 되었다.

특히 로그인한 사용자라고 하더라도 요청하는 정보나 기능마다 접근 권한을 확인해야 한다는 점이 중요하다고 생각하였다.

또한 복잡한 공격 기법이 사용되지 않더라도 개발 과정에서 권한 검증이 누락되는 것만으로 심각한 보안 문제가 발생할 수 있다는 것을 알게 되었다.

앞으로 웹 해킹이나 시스템 해킹을 공부할 때 기능의 동작 방식뿐만 아니라 해당 기능이 어떤 권한으로 실행되는지에도 주의를 기울이고자 한다.

## 8. 참고자료

1. [OWASP Top 10 2025 - A01: Broken Access Control](https://top10.owasp.org/2025/A01_2025-Broken_Access_Control/)

2. [PortSwigger - Access Control Vulnerabilities and Privilege Escalation](https://portswigger.net/web-security/access-control)

3. [OWASP - Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)

4. [OWASP - Insecure Direct Object Reference Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)

5. [MITRE - CWE-862: Missing Authorization](https://cwe.mitre.org/data/definitions/862.html)

6. [MITRE - CWE-639: Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html)

7. [Atlassian - CVE-2023-22515 Security Advisory](https://confluence.atlassian.com/security/cve-2023-22515-improper-authorization-vulnerability-in-confluence-data-center-and-server-1295682276.html)

8. [NIST NVD - CVE-2023-22515](https://nvd.nist.gov/vuln/detail/cve-2023-22515)

9. [CISA, FBI, MS-ISAC - CVE-2023-22515 Cybersecurity Advisory](https://www.ic3.gov/CSA/2023/231016.pdf)
