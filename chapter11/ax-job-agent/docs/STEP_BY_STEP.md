# AX 채용정보 Agent Pipeline — STEP BY STEP

> 이 문서는 프로젝트를 **한 단계씩 실행하기 위한 작업용 가이드**입니다.  
> 전체 목적과 구조는 `PROJECT_GUIDE.md`, 진행 상태는 `PROGRESS.md`, 공통 규칙은 `DEVELOPMENT_RULES.md`를 기준으로 합니다.

---

# 사용 방법

각 STEP은 아래 순서로 진행합니다.

1. 목적 확인
2. 왜 하는지 이해
3. 실제 작업 수행
4. 실행 결과 직접 확인
5. 완료 조건 판정
6. `PROGRESS.md` 갱신
7. 다음 STEP 진행

Notebook을 사용하는 STEP은 반드시 다음 3단위 규칙을 따릅니다.

```text
1. 작업계획 — Markdown Cell
2. 실제 코드 — Code Cell
3. 실행 결과 해석 / 분석 요약 — Markdown Cell
```

---

# STEP 00. Git / 프로젝트 준비

## 목적
원본 저장소를 직접 수정하지 않고 자신의 Fork와 별도 브랜치에서 안전하게 실습합니다.

## 왜 하는가
실습 중 오류가 발생하더라도 원본 코드와 작업 이력을 분리할 수 있습니다.

## 실제 작업
- 원본 저장소 Fork
- 자신의 Fork Clone
- `ax-job-agent` 브랜치 생성
- `chapter11/ax-job-agent` 폴더 생성

## 실행 명령 또는 코드

```powershell
git switch -c ax-job-agent
```

## 초보자가 봐야 할 포인트
- Fork = GitHub에 내 저장소 복사본 만들기
- Clone = GitHub 저장소를 내 PC로 가져오기
- Branch = 같은 저장소 안에서 작업 흐름 분리

## 예상 결과
현재 브랜치가 `ax-job-agent`이고 프로젝트 폴더가 존재합니다.

## 완료 조건
- [x] Fork 완료
- [x] Clone 완료
- [x] `ax-job-agent` 브랜치 생성
- [x] 프로젝트 폴더 생성

## 다음 단계로 넘어가기 전 체크
`git branch`, `git status`를 확인합니다.

---

# STEP 01. 개발환경 확인

## 목적
Python, 가상환경, Jupyter, 필수 패키지가 정상적으로 연결되어 있는지 확인합니다.

## 왜 하는가
환경이 잘못된 상태에서 이후 코드를 작성하면 코드 오류와 환경 오류를 구분하기 어렵습니다.

## 실제 작업
- `.venv` 생성 및 활성화
- 패키지 설치
- Jupyter kernel 등록
- Notebook 생성
- Notebook에서 Python 실행 경로와 패키지 import 확인

## 실행 명령 또는 코드

PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install pandas requests beautifulsoup4 jupyter python-dotenv
python -m ipykernel install --user --name ax-job-agent --display-name "Python (ax-job-agent)"
```

Notebook 작업계획 Markdown Cell:

```markdown
# STEP 01. 개발환경 확인

## 작업계획

- 현재 Notebook이 프로젝트 가상환경을 사용하는지 확인한다.
- Python 버전과 실행 경로를 확인한다.
- pandas, requests, BeautifulSoup import가 정상인지 확인한다.
- 모든 결과가 정상일 때만 STEP 01을 완료한다.
```

Notebook Code Cell:

```python
import sys
import platform

print("Python:", sys.version)
print("실행 경로:", sys.executable)
print("Platform:", platform.platform())
```

Notebook Code Cell:

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup

print("pandas:", pd.__version__)
print("requests:", requests.__version__)
print("BeautifulSoup import: OK")
```

결과 해석 Markdown Cell:

```markdown
## 실행 결과 해석 / 분석 요약

- Python 버전:
- 실행 경로:
- .venv 사용 여부:
- pandas import:
- requests import:
- BeautifulSoup import:
- 예상과 다른 부분:
- STEP 02 진행 가능 여부:
```

## 초보자가 봐야 할 포인트
가장 중요한 값은 `sys.executable`입니다.

정상 예:

```text
...\chapter11\ax-job-agent\.venv\Scripts\python.exe
```

## 예상 결과
Notebook이 프로젝트의 `.venv` Python으로 실행되고 모든 import가 성공합니다.

## 완료 조건
- [ ] `sys.executable`이 프로젝트 `.venv`를 가리킴
- [ ] pandas import 성공
- [ ] requests import 성공
- [ ] BeautifulSoup import 성공
- [ ] 사람이 실제 출력 확인

## 다음 단계로 넘어가기 전 체크
실행 결과를 확인하지 않고 완료 처리하지 않습니다.

---

# STEP 02. 수집 데이터 명세 정의

## 목적
크롤링 전에 앞으로 만들 DataFrame의 구조를 먼저 정의합니다.

## 왜 하는가
수집부터 시작하면 필요한 값과 불필요한 값이 섞여 이후 전처리가 복잡해집니다.

## 실제 작업
한 행과 컬럼의 의미를 Notebook Markdown으로 정의합니다.

## 실행 명령 또는 코드
이 STEP은 우선 Markdown 중심으로 진행합니다.

Notebook 작업계획 Markdown Cell:

```markdown
# STEP 02. 수집 데이터 명세

## 작업계획

DataFrame 한 행을 채용공고 한 건으로 정의한다.

초기 컬럼:
- company_name
- job_title
- career
- location
- posted_date
- closing_date
- job_url
- search_keyword
- collected_at
```

결과 해석 Markdown Cell:

```markdown
## 실행 결과 해석 / 분석 요약

- 한 행의 의미:
- 각 컬럼의 의미:
- 반드시 필요한 컬럼:
- 아직 추가하지 않을 컬럼:
- STEP 03 진행 가능 여부:
```

## 초보자가 봐야 할 포인트
DataFrame은 표이고, 한 행은 한 사건 또는 한 관측값입니다. 여기서는 **채용공고 한 건**입니다.

## 예상 결과
컬럼 구조를 자신의 말로 설명할 수 있습니다.

## 완료 조건
- [ ] 한 행의 의미 설명 가능
- [ ] 컬럼 목록 확정
- [ ] 각 컬럼 의미 설명 가능

## 다음 단계로 넘어가기 전 체크
실제 수집 전에 컬럼이 과도하지 않은지 확인합니다.

---

# STEP 03. 채용공고 페이지 접근 테스트

## 목적
본격적인 수집 전에 대상 페이지에 HTTP 요청이 가능한지 최소 단위로 확인합니다.

## 왜 하는가
처음부터 여러 페이지를 크롤링하면 접근 차단, HTML 구조 문제, 잘못된 URL 문제를 찾기 어렵습니다.

## 실제 작업
`1개 검색어 → 1개 페이지 → 1회 요청`만 테스트합니다.

## 실행 명령 또는 코드

Notebook 작업계획 Markdown Cell:

```markdown
# STEP 03. 채용공고 페이지 접근 테스트

## 작업계획

이번 단계에서는 실제 채용공고를 여러 건 수집하지 않는다.

확인 항목:
- HTTP 상태 코드
- Content-Type
- 응답 길이
- HTML 일부 내용
- 예상 페이지인지 여부

하지 않는 것:
- 여러 페이지 반복 요청
- DataFrame 생성
- 상세 공고 반복 수집
```

Notebook Code Cell 기본 형태:

```python
import requests

TARGET_URL = "확정된 테스트 URL"

response = requests.get(
    TARGET_URL,
    timeout=10,
    headers={"User-Agent": "Mozilla/5.0"}
)

print("Status Code:", response.status_code)
print("Content-Type:", response.headers.get("Content-Type"))
print("Response Length:", len(response.text))
print(response.text[:500])
```

결과 해석 Markdown Cell:

```markdown
## 실행 결과 해석 / 분석 요약

- 요청 성공 여부:
- HTTP 상태 코드:
- Content-Type:
- 응답 길이:
- HTML에서 확인한 내용:
- 차단 / 로그인 / 오류 페이지 여부:
- 자동 수집 진행 가능 여부:
- 추가 확인 사항:
```

## 초보자가 봐야 할 포인트
`200`만 나왔다고 정상 페이지라고 단정하지 않습니다. 실제 HTML 일부를 직접 확인합니다.

## 예상 결과
대상 페이지 접근 가능 여부를 설명할 수 있습니다.

## 완료 조건
- [ ] HTTP 응답 확인
- [ ] Content-Type 확인
- [ ] 본문이 비어 있지 않은지 확인
- [ ] 사람이 HTML 일부 직접 확인
- [ ] 자동 수집 가능 여부 판단

## 다음 단계로 넘어가기 전 체크
robots.txt / 이용 정책 / 과도한 요청 여부를 확인합니다.

---

# STEP 04. 소량 데이터 수집

## 목적
전체 크롤러를 만들기 전에 5~10건 정도의 최소 데이터만 수집해 구조를 검증합니다.

## 왜 하는가
소량 데이터에서 필드 추출이 틀리면 전체 수집 전에 빠르게 수정할 수 있습니다.

## 실제 작업
- 1개 검색어
- 1개 페이지
- 5~10개 공고
- 회사명 / 제목 / URL 등 핵심 필드 확인

## 실행 명령 또는 코드
현재 페이지 구조를 확인한 뒤 실제 HTML 선택자에 맞춰 작성합니다.

## 초보자가 봐야 할 포인트
"수집 성공"보다 "실제 화면의 값과 코드가 뽑은 값이 같은지"가 중요합니다.

## 예상 결과
5~10개의 공고가 확인 가능한 형태로 출력됩니다.

## 완료 조건
- [ ] 5~10건 수집
- [ ] 실제 페이지와 값 비교
- [ ] 필수 값 누락 여부 확인

## 다음 단계로 넘어가기 전 체크
아직 DataFrame 변환 전에 raw 결과를 눈으로 확인합니다.

---

# STEP 05. DataFrame 생성

## 목적
수집한 데이터를 pandas 표 구조로 변환합니다.

## 왜 하는가
이후 전처리, 필터링, 집계는 DataFrame을 기준으로 진행합니다.

## 실제 작업
- 리스트 / 딕셔너리 구조 확인
- DataFrame 생성
- shape / head / columns 확인

## 실행 명령 또는 코드

```python
df = pd.DataFrame(jobs)

print(df.shape)
print(df.columns.tolist())
display(df.head())
```

## 초보자가 봐야 할 포인트
- 행 개수
- 열 개수
- 한 행의 의미
- 컬럼명
- 실제 값

## 예상 결과
정의한 컬럼 구조의 DataFrame이 생성됩니다.

## 완료 조건
- [ ] DataFrame 생성
- [ ] shape 해석 가능
- [ ] head 결과 확인
- [ ] 컬럼명 확인

## 다음 단계로 넘어가기 전 체크
예상하지 않은 컬럼이나 빈 값이 많은지 확인합니다.

---

# STEP 06. 전처리 / 중복 제거

## 목적
분석 가능한 상태로 데이터를 정리합니다.

## 왜 하는가
결측, 중복, 날짜 형식 오류가 있으면 이후 통계가 왜곡됩니다.

## 실제 작업
- 결측 확인
- 문자열 공백 정리
- 날짜 변환
- URL 중복 확인 및 제거

## 실행 명령 또는 코드

```python
display(df.isna().sum())
print("URL 중복:", df.duplicated(subset=["job_url"]).sum())
```

## 초보자가 봐야 할 포인트
전처리는 데이터를 "예쁘게" 만드는 것이 아니라 분석 오류를 줄이는 과정입니다.

## 예상 결과
결측과 중복 현황을 설명할 수 있습니다.

## 완료 조건
- [ ] 결측 현황 확인
- [ ] 중복 건수 확인
- [ ] 날짜 변환 오류 확인

## 다음 단계로 넘어가기 전 체크
제거한 데이터가 왜 제거됐는지 설명할 수 있어야 합니다.

---

# STEP 07. 신규 공고 판별

## 목적
이전 실행에 이미 존재했던 공고와 새 공고를 구분합니다.

## 왜 하는가
매주 같은 공고를 반복 추천하지 않기 위해서입니다.

## 실제 작업
기본 식별자로 `job_url`을 사용합니다.

## 실행 명령 또는 코드
이전 수집 데이터와 현재 데이터를 비교합니다.

## 초보자가 봐야 할 포인트
"신규"는 절대적인 개념이 아니라 **이전 실행 데이터와 비교한 결과**입니다.

## 예상 결과
기존 공고와 신규 공고가 분리됩니다.

## 완료 조건
- [ ] 판별 기준 설명 가능
- [ ] 신규 공고만 별도 확인 가능

## 다음 단계로 넘어가기 전 체크
URL이 바뀌는 사이트라면 보조 키가 필요한지 확인합니다.

---

# STEP 08. 기본 분석 / 관련 공고 필터링

## 목적
Gemini를 사용하기 전에 Python으로 계산할 수 있는 사실을 먼저 계산합니다.

## 왜 하는가
개수와 통계는 LLM이 아니라 pandas가 계산해야 재현성이 높습니다.

## 실제 작업
- 신규 공고 수
- 회사별 공고 수
- 지역별 공고 수
- 경력 조건 분포
- 검색어별 건수
- 관련 키워드 기반 필터

## 초보자가 봐야 할 포인트
숫자로 계산할 수 있는 사실과 AI 해석을 분리합니다.

## 완료 조건
- [ ] 기본 통계 확인
- [ ] 관련 공고 필터 기준 확인
- [ ] 결과를 사람이 직접 검증

## 다음 단계로 넘어가기 전 체크
Gemini에 넘길 데이터가 너무 많지 않은지 확인합니다.

---

# STEP 09. Gemini API 연동

## 목적
검증된 일부 신규 공고를 Gemini에 전달해 텍스트 해석을 수행합니다.

## 왜 하는가
공고 원문에서 핵심 업무, 기술, 직무 특성을 구조화하기 위해서입니다.

## 실제 작업
- API Key를 환경변수로 관리
- 소량 공고로 API 호출
- 응답 원문 확인

## 초보자가 봐야 할 포인트
API Key는 코드에 직접 쓰지 않습니다.

## 완료 조건
- [ ] API 호출 성공
- [ ] 키 Git 비노출
- [ ] 응답 직접 확인

## 다음 단계로 넘어가기 전 체크
응답이 사실을 과장하거나 누락하지 않았는지 확인합니다.

---

# STEP 10. Gemini 결과 검증

## 목적
API 호출 성공과 분석 품질을 분리해서 확인합니다.

## 왜 하는가
LLM의 결과는 자동으로 정답이 아닙니다.

## 실제 작업
몇 개 공고를 원문과 Gemini 결과로 직접 비교합니다.

## 완료 조건
- [ ] 누락 확인
- [ ] 과도한 해석 확인
- [ ] 실제 보고서 사용 가능 여부 판단

## 다음 단계로 넘어가기 전 체크
검증 기준을 문서에 남깁니다.

---

# STEP 11. Markdown 보고서 생성

## 목적
분석 결과를 사람이 읽을 수 있는 주간 보고서로 만듭니다.

## 왜 하는가
수집과 분석 결과를 실제 의사결정에 사용할 수 있는 형태로 바꾸기 위해서입니다.

## 실제 작업
- pandas 통계
- Gemini 요약
- 추천 공고
- 데이터 기준
- 주의사항

## 완료 조건
- [ ] Markdown 파일 생성
- [ ] 계산 사실과 AI 설명 구분
- [ ] 사람이 직접 읽고 검토

## 다음 단계로 넘어가기 전 체크
링크와 한글이 정상인지 확인합니다.

---

# STEP 12. Slack 발송

## 목적
보고서를 Slack으로 전달합니다.

## 실제 작업
Webhook 또는 프로젝트에서 정한 방식으로 테스트 메시지를 전송합니다.

## 완료 조건
- [ ] 실제 Slack 수신
- [ ] 한글 정상
- [ ] 링크 정상

## 다음 단계로 넘어가기 전 체크
실제 Webhook 값이 Git에 포함되지 않았는지 확인합니다.

---

# STEP 13. Gmail 발송

## 목적
주간 보고서를 이메일로도 받을 수 있게 합니다.

## 완료 조건
- [ ] 실제 메일 수신
- [ ] 제목 / 본문 / 한글 정상
- [ ] 인증정보 Git 비노출

## 다음 단계로 넘어가기 전 체크
실제 비밀번호 또는 App Password가 코드에 없는지 확인합니다.

---

# STEP 14. 함수화

## 목적
Notebook에서 검증한 코드를 재사용 가능한 함수로 이동합니다.

## 왜 하는가
Notebook 검증 코드를 운영 Pipeline 코드와 분리하기 위해서입니다.

## 실제 작업
검증된 기능만 `src/`로 옮깁니다.

## 완료 조건
- [ ] 함수 단위 실행 성공
- [ ] Notebook 결과와 동일한 결과 확인

## 다음 단계로 넘어가기 전 체크
검증되지 않은 새 로직을 함수화 과정에서 추가하지 않습니다.

---

# STEP 15. main.py 통합

## 목적
전체 Pipeline 실행 순서를 한 곳에서 조정합니다.

## 실제 작업

```text
수집
→ 전처리
→ 신규 판별
→ 분석
→ Gemini
→ 보고서
→ Slack
→ Gmail
```

## 완료 조건
`python main.py`가 로컬에서 끝까지 실행됩니다.

## 다음 단계로 넘어가기 전 체크
중간 실패가 성공처럼 처리되지 않는지 확인합니다.

---

# STEP 16. 로컬 전체 실행 검증

## 목적
GitHub Actions로 옮기기 전에 로컬 Pipeline을 완전히 검증합니다.

## 실제 작업
- 수집 건수
- 필수 컬럼
- 중복
- 날짜
- 신규 공고
- Gemini
- 보고서
- Slack
- Gmail
- 오류 로그

## 완료 조건
모든 주요 기능이 로컬에서 성공합니다.

## 다음 단계로 넘어가기 전 체크
로컬 실패 상태에서 GitHub Actions를 만들지 않습니다.

---

# STEP 17. GitHub Actions 수동 실행

## 목적
로컬에서 성공한 Pipeline이 GitHub 환경에서도 동작하는지 확인합니다.

## 실제 작업
처음에는 `workflow_dispatch`만 사용해 수동 실행합니다.

## 완료 조건
GitHub Actions 수동 실행이 성공합니다.

## 다음 단계로 넘어가기 전 체크
Secrets, 경로, requirements, OS 차이를 확인합니다.

---

# STEP 18. GitHub Actions 주간 자동 실행

## 목적
검증된 Pipeline을 매주 자동 실행합니다.

## 실제 작업
수동 실행 성공 후 schedule을 추가합니다.

예:

```yaml
on:
  workflow_dispatch:
  schedule:
    - cron: "0 0 * * 1"
```

## 완료 조건
- [ ] 수동 실행 성공 이력 존재
- [ ] schedule 설정
- [ ] Secrets 연결
- [ ] 주간 실행 기준 설명 가능

## 다음 단계로 넘어가기 전 체크
자동화 후 첫 실제 실행 결과도 사람이 확인합니다.

---

# 공통 중단 조건

아래 중 하나라도 해당하면 다음 STEP으로 넘어가지 않습니다.

- 코드 실행 오류
- 예상 컬럼 누락
- 데이터가 비정상
- 결과를 사람이 확인하지 않음
- 실행 결과 해석이 없음
- Gemini 결과 검증 미완료
- 인증정보 노출
- 로컬 실행 실패
- GitHub Actions 수동 실행 실패

---

# 공통 완료 보고 형식

Local Coding Agent는 작업 후 아래 형식으로 보고합니다.

```text
1. 수정한 파일
2. 추가 / 변경한 내용
3. 사용자가 직접 실행해야 하는 것
4. 예상 결과
5. 실제 실행 여부
6. 완료 조건 충족 여부
7. 다음 STEP은 진행하지 않았는지
8. git status
9. git diff 요약
```

> Agent가 직접 확인하지 않은 실행 결과를 성공했다고 보고하지 않습니다.
