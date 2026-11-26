---
title: "[SEGMENT CTF] 대회 참여 및 Write-up"
date: 2026-11-14 12:00:00 +0900
categories: ["CTF/Wargame"]
tags: [CTF, SEGMENT, Cryptography, Steganography, BGV, PNG, Python]
---

## 1. 대회 참여 개요

Root4 팀으로 SEGMENT CTF에 참여하여 총 2개의 문제를 해결했다.

| 문제명 | 분야 |
|---|---|
| BGV Secret Key Recovery | Cryptography |
| Frame 12 | Steganography |

---

## 2. BGV Secret Key Recovery Write-up

### 2.1. 문제 설명

다항식 기반 암호화 방식에서 알려진 평문 `CTF2026`과 암호문 `(c₀, c₁)`을 이용해 비밀키 `s`를 복구하고 지정된 비트 연산으로 FLAG를 생성하는 문제

암호화 과정:

$$
c_1=c_0s+te+m\pmod q
$$

비밀키 `s`의 모든 계수는 `{-1, 0, 1}` 중 하나

### 2.2. 취약점 분석

암호화에 사용된 두 파라미터 `q`, `t`의 최대공약수를 계산

```python
from math import gcd

p = gcd(q, t)
print(p)
```

실행 결과:

```
40961
```

`p = 40961`은 `q`와 `t`를 모두 나누므로, 암호화 식을 `p`로 줄이면 오류항 `te`가 사라짐

$$
c_1-m\equiv c_0s\pmod p
$$

따라서 알려진 평문과 암호문을 이용해 비밀키에 관한 관계식을 얻을 수 있었음

### 2.3. 비밀키 복구 과정

#### 2.3.1. 암호문 불러오기

`ctxt.txt`에는 총 8192개의 정수가 저장됨

- 앞 4096개: `c₀`의 계수
- 뒤 4096개: `c₁`의 계수

알려진 메시지 `"CTF2026"`을 ASCII 코드로 변환하고 나머지 계수는 0으로 설정

#### 2.3.2. NTT 적용

다항식 곱셈을 효율적으로 계산하기 위해 NTT(Number Theoretic Transform)를 사용함

NTT를 적용하면 다항식 곱셈이 각 위치의 곱셈으로 변환됨

$$
\hat c_{1,i}-\hat m_i
=\hat c_{0,i}\hat s_i\pmod p
$$

따라서 역원이 존재하는 위치에서는 다음과 같이 비밀키 성분을 계산할 수 있음

$$
\hat s_i=
(\hat c_{1,i}-\hat m_i)\hat c_{0,i}^{-1}\pmod p
$$

#### 2.3.3. 역원이 없는 위치 확인

py 파일을 실행함

### 2.4. 실행 결과

NTT를 적용한 결과 역원이 존재하지 않는 위치가 총 12개 발견됨

해당 위치를 미지수로 설정하고 Meet-in-the-Middle 알고리즘을 이용해 비밀키 후보를 탐색함

모든 계수가 {-1, 0, 1}에 속하는 비밀키를 복구하는 데 성공함

복구한 비밀키를 암호문 관계식에 대입하여 검증한 결과 모든 계수가 조건을 만족함

이후 문제에서 제시한 비트 변환, 비트 반전, 블록 XOR 및 ASCII 변환을 수행하여 최종 FLAG를 획득 성공

**FLAG: `SEG{F0UND_TH3_S}`**

### 2.5. 결론

이번 문제는 암호화 파라미터 q와 t의 최대공약수가 1이 아니라는 점을 이용하는 문제였다.

최대공약수 40961을 이용해 오류항을 제거하고 NTT와 Meet-in-the-Middle 알고리즘을 활용하여 비밀키를 복구하였다.

---

## 3. Frame 12-write up

### 3.1. 문제 설명

Frame12: 사진 동아리의 오래된 폴라로이드 사진에서 숨겨진 메모를 찾아 메모의 지시에 따라 실제 플래그를 추출하는 문제

문제 설명에서 가짜 단서가 존재한다고 언급하였기에 처음 발견한 플래그가 실제 정답인지 검증하는 과정을 진행할 것

### 3.2. PNG 파일 분석 및 복구

`file` 명령어로 이미지 형식을 확인

```
file polaroid.png
```

파일은 600×600 크기의 PNG 이미지

`pngcheck`로 PNG 내부 구조를 확인

```
pngcheck -v polaroid.png
```

출력 결과:

```
600 x 600 image, 24-bit RGB
CRC error in chunk IHDR
computed 31040f8b
expected c79d9e93
```

PNG의 `IHDR` 청크에서 CRC 오류가 발생한 것을 확인

PNG의 IHDR에는 이미지의 가로·세로 크기 등이 저장되므로 헤더 값이 변조되었을 가능성을 고려

#### 원래 이미지 높이 찾기

CRC 값이 일치하는 높이를 Python으로 탐색

실행 결과:

```
원래 높이: 736
```

원래 이미지 크기: **600×736**

이를 바탕으로 PNG 헤더를 복구하여 `restored.png`를 생성

아래에 작게 메모가 있는게 보였음 → 복구 작업 실행

### 3.3. 숨겨진 메모 확인

복원된 이미지에 기존에 보이지 않던 하단의 메모가 나타남

```
roll 07  frame 12
D-76 1:1 20C 9min
the blue proof lies -
work GREEN, then rewind
key: fixer + roll no.
```

메모에서 다음과 같은 단서를 확인

<!-- image.png 삽입 위치 -->

- `roll 07`: 롤 번호는 07
- `the blue proof lies`: BLUE 채널에 가짜 단서가 존재함
- `work GREEN`: GREEN 채널을 분석해야 함
- `then rewind`: 추출한 데이터의 순서를 뒤집어야 함
- `key: fixer + roll no.`: `fixer07`을 복호화 키 후보로 사용

### 3.4. Steganography 분석

이미지에 숨겨진 데이터를 확인하기 위해 `zsteg`를 사용

```
zsteg restored.png
```

분석 결과 BLUE 채널에서 다음 문자열이 발견

```
b1,b,lsb,xy .. text: "SEG{c0nt4ct_sh33t_r0ll_07}="
```

그러나 메모에 `the blue proof lies`라고 적혀 있으므로 해당 문자열은 가짜 플래그로 판단함

메모의 지시에 따라 GREEN 채널을 분석

초기에는 `zsteg`의 추출 옵션과 여러 XOR 변환을 시도했으나 플래그를 찾지 못함

Python을 이용하여 GREEN 채널의 비트 추출 방식, 데이터 순서, 복호화 키를 함께 검사하는 방식으로 변경

### 3.5. GREEN 채널 복호화

자동 분석 스크립트 `frame12_analyze.py`를 실행

```
python3 frame12_analyze.py
```

실행 결과:

```
[FLAG] xy/bit0/lsb/byte_reverse=True/xor/fixer07/shift0
    SEG{n3v3r_tru5t_th3_blu3_pr00f}
```

분석 결과 다음 조합에서 플래그가 발견함

| 항목 | 설정 |
|---|---|
| 채널 | GREEN |
| 비트 위치 | bit 0 (LSB) |
| 픽셀 탐색 | xy |
| 비트 결합 | LSB |
| 데이터 변환 | 바이트 순서 역순 |
| 복호화 | XOR |
| 키 | `fixer07` |
| 키 오프셋 | 0 |

메모의 `rewind` rewind바이트 순서를 뒤집는 작업이었고 `fixer + roll no.`는 `fixer07`이라는 XOR 키로 해석해야 했음

동일한 플래그가 여러 변환 조합에서 발견되어 결과를 확인할 수 있었음

### 3.6. 최종 플래그

```
SEG{n3v3r_tru5t_th3_blu3_pr00f}
```

### 3.7. 결론

PNG 헤더 변조와 이미지 스테가노그래피, XOR 복호화가 결합된 문제로 먼저 IHDR의 CRC 오류를 이용해 원래 이미지 높이를 복구하고 숨겨진 메모에서 분석 대상 채널과 복호화 단서를 확인함. 이후 BLUE 채널의 가짜 플래그를 제외하고 GREEN 채널의 LSB 데이터를 분석하여 플래그 후보를 추출 성공

핵심은 PNG 복구를 통해 숨겨진 메모를 발견하고 메모의 지시에 따라 올바른 채널과 데이터 변환 방식을 선택하는 것

---

## 4. Root4 팀 Write-up

[SEGMENT CTF - Root4 Write-up](https://nettle-pruner-0b5.notion.site/Root4-write-up-418307a11c8a807d8743e0ab5f1c7f30?source=copy_link)
