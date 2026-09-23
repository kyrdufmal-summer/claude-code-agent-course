# AX 채용정보 Agent Pipeline — 프로젝트 전체 가이드

> 프로젝트 경로: `chapter11/ax-job-agent/`  
> 기준: Chapter 11 실습안  
> 대상: Python 데이터 분석 초보자  
> 운영 방식: GPT Web을 Orchestrator로 사용하고, 로컬 Coding Agent는 Claude Code / Codex / Copilot을 작업 성격과 토큰 사용량에 따라 번갈아 사용

---

## 0. 이 문서를 가장 먼저 보는 방법

작업을 시작할 때는 항상 아래 순서로 확인합니다.

1. `docs/PROGRESS.md`에서 **현재 완료 단계**를 확인합니다.
2. 이 문서에서 **다음 STEP의 목적 / 작업 / 완료 조건**을 확인합니다.
3. GPT Web(Orchestrator)에게 **현재 STEP 하나만** 계획하게 합니다.
4. 로컬 Agent에게 **현재 STEP 하나만** 구현하게 합니다.
5. Jupyter Notebook 또는 Terminal에서 직접 실행합니다.
6. 사람이 결과를 확인하고 Markdown으로 해석합니다.
7. 이상이 없을 때만 `docs/PROGRESS.md`를 갱신하고 다음 STEP으로 넘어갑니다.

핵심 원칙은 다음입니다.

```text
계획
↓
구현
↓
실행
↓
관찰
↓
검증
↓
해석
↓
다음 STEP 결정
```

---

# 1. 프로젝트 목표

잡코리아에서 AX / AI / 데이터 분석 관련 채용공고를 주기적으로 수집하고,

1. 필요한 컬럼만 정리하고
2. 중복 공고와 기존 공고를 구분하고
3. 신규 공고의 기본 통계를 계산하고
4. 관련성이 높은 공고를 추리고
5. Gemini API로 주요 내용을 요약하고
6. 주간 Markdown 보고서를 만들고
7. Slack과 Gmail로 전송하고
8. GitHub Actions가 주 1회 자동 실행하도록 구성합니다.

이 프로젝트의 핵심은 특정 크롤링 기술이 아니라 다음 전체 흐름을 이해하는 것입니다.

```text
수집 → 정제 → 분석 → AI 해석 → 검증 → 보고 → 자동화
```

---

# 2. 역할 분리

## 2.1 GPT Web — Orchestrator

GPT Web은 직접 로컬 파일을 수정하는 역할이 아니라 다음을 담당합니다.

- 전체 목표 유지
- 작업을 작은 STEP으로 분해
- 각 STEP의 완료 조건 정의
- 로컬 Agent에게 전달할 작업 프롬프트 작성
- 실행 결과를 보고 다음 작업 결정

## 2.2 Local Coding Agent — Claude Code / Codex / Copilot

로컬 Agent는 실제 프로젝트 파일을 다룹니다.

- 파일 읽기
- 코드 작성 / 수정
- 명령 실행
- 오류 수정

> 강의안의 기본 Coding Agent는 Claude Code이지만, 이 프로젝트에서는 사용자의 운영 방식에 따라 Claude Code / Codex / Copilot을 번갈아 사용할 수 있습니다.  
> 단, **동시에 여러 Agent에게 같은 작업을 맡기지 않고 현재 STEP 하나만 명확히 전달**합니다.

## 2.3 Human — 최종 승인자

사람은 다음을 직접 확인합니다.

- 수집된 데이터가 맞는가?
- 컬럼과 값이 정상인가?
- pandas 계산이 맞는가?
- Gemini 요약이 원문과 크게 다르지 않은가?
- 다음 단계로 넘어가도 되는가?

---

# 3. 초보자용 데이터 분석 관점

이 프로젝트에서 반드시 이해해야 하는 데이터 흐름입니다.

```text
RAW DATA
채용공고 원본
↓
CLEAN
결측 / 날짜 / 중복 정리
↓
FILTER
AX / AI / 데이터 관련 공고 추리기
↓
COMPARE
기존 데이터와 비교
↓
NEW
신규 공고 판별
↓
AGGREGATE
회사별 / 지역별 / 경력별 통계
↓
INTERPRET
Gemini 요약 및 설명
↓
REPORT
Markdown 보고서
```

중요한 역할 구분:

- **Python / pandas**: 계산 가능한 사실
- **Gemini**: 요약, 설명, 분류, 해석

예:

```text
"신규 공고가 18건이다"
→ pandas

"이 공고는 LLM / RAG 업무 비중이 높다"
→ Gemini
```

---

# 4. 목표 프로젝트 구조

최종적으로 다음과 같은 구조를 목표로 합니다.

```text
chapter11/
└── ax-job-agent/
    ├── docs/
    │   ├── PROJECT_GUIDE.md
    │   └── PROGRESS.md
    ├── notebooks/
    │   └── ax_job_pipeline.ipynb
    ├── src/
    │   ├── crawler.py
    │   ├── preprocess.py
    │   ├── analyzer.py
    │   ├── gemini_client.py
    │   ├── reporter.py
    │   └── notifier.py
    ├── data/
    │   ├── raw/
    │   └── processed/
    ├── reports/
    ├── .env.example
    ├── .gitignore
    ├── main.py
    ├── requirements.txt
    └── README.md
```

처음부터 모든 파일을 만들지 않습니다.  
**필요해지는 단계에서 하나씩 추가합니다.**

---

# 5. 전체 진행 순서

## STEP 00. Git / 프로젝트 준비

### 목적
원본 저장소를 직접 수정하지 않고 자신의 Fork에서 안전하게 실습합니다.

### 작업
- PUBLIC 저장소 Fork
- 자신의 Fork Clone
- 실습 브랜치 `ax-job-agent` 생성
- `chapter11/ax-job-agent` 프로젝트 폴더 생성

### 완료 조건
- 저장소 Owner가 본인 GitHub 계정
- 로컬 Clone 완료
- 현재 브랜치가 `ax-job-agent`
- 프로젝트 폴더 존재

---

## STEP 01. 개발환경 확인

### 목적
앞으로 사용할 Python / Jupyter / 필수 패키지가 정상인지 먼저 검증합니다.

### 작업
- 가상환경 생성
- 가상환경 활성화
- pip 업그레이드
- 기본 패키지 설치
- Python 위치와 버전 확인
- VS Code Python Interpreter가 `.venv`인지 확인
- Notebook 뼈대 생성
- 환경 확인 Code Cell 실행

### 기본 명령

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install pandas requests beautifulsoup4 jupyter python-dotenv
python --version
where.exe python
```

### Notebook에서 확인할 항목

```python
import sys
import platform

print("Python:", sys.version)
print("Platform:", platform.platform())
```

```python
import pandas as pd
import requests
from bs4 import BeautifulSoup

print("pandas:", pd.__version__)
print("requests:", requests.__version__)
print("BeautifulSoup import: OK")
```

### 완료 조건
- `.venv` 활성화
- Python 버전 출력
- Python 경로가 프로젝트의 `.venv\Scripts\python.exe`
- pandas / requests / BeautifulSoup import 성공
- Notebook 실행 성공

---

## STEP 02. 수집 데이터 명세 정의

### 목적
크롤링 전에 **DataFrame 한 행과 각 컬럼의 의미**를 먼저 정의합니다.

### 한 행의 의미

```text
DataFrame의 한 행 = 채용공고 한 건
```

### 초기 컬럼

- `company_name`: 회사명
- `job_title`: 공고 제목
- `career`: 경력 조건
- `location`: 근무 지역
- `posted_date`: 등록일
- `closing_date`: 마감일
- `job_url`: 공고 URL
- `search_keyword`: 어떤 검색어로 발견했는지
- `collected_at`: 수집 시각

### 완료 조건
- 컬럼 목록 확정
- 각 컬럼 의미 설명 가능
- 한 행이 무엇을 의미하는지 설명 가능

---

## STEP 03. 채용공고 페이지 접근 테스트

### 목적
대량 크롤링 전에 HTTP 요청이 가능한지 확인합니다.

### 원칙
처음부터 여러 페이지를 수집하지 않습니다.

```text
1개 검색어
→ 1개 페이지
→ 응답 확인
```

### 확인 항목
- HTTP 상태 코드
- Content-Type
- 응답 길이
- HTML에서 예상 채용정보가 보이는지
- robots.txt / 이용 정책
- 과도한 요청이 아닌지

### 완료 조건
- 요청 성공 여부를 설명 가능
- 페이지 구조를 사람이 직접 확인
- 자동 수집이 적절하지 않다면 샘플 HTML / CSV로 대체할지 결정

---

## STEP 04. 소량 데이터 수집

### 목적
전체 수집 로직 전에 최소 데이터로 구조를 검증합니다.

### 확장 순서

```text
1개 검색어
↓
1개 페이지
↓
1개 공고
↓
5~10개 공고
```

### 완료 조건
- 5~10건 정도의 수집 결과를 직접 확인
- 회사명 / 제목 / URL 등이 실제 페이지와 맞는지 검증

---

## STEP 05. DataFrame 생성

### 목적
수집 결과를 pandas가 다룰 수 있는 표 구조로 변환합니다.

### 확인 예

```python
print(df.shape)
display(df.head())
```

### 초보자 확인 질문
- 행은 몇 개인가?
- 열은 몇 개인가?
- 한 행은 무엇인가?
- 컬럼 이름은 무엇인가?
- 예상 값이 들어 있는가?

### 완료 조건
- DataFrame 생성 성공
- `shape`, `head()` 결과 해석 가능

---

## STEP 06. 전처리 / 중복 제거

### 목적
분석 가능한 상태로 데이터를 정리합니다.

### 주요 작업
- 결측 확인
- 날짜 형식 변환
- URL 중복 확인
- 필요 시 검색어 통합
- 불필요한 공백 / 문자열 정리

### 확인 예

```python
display(df.isna().sum())
print("URL 중복:", df.duplicated(subset=["job_url"]).sum())
```

### 완료 조건
- 결측값 현황 설명 가능
- 중복 건수 설명 가능
- 날짜 변환 실패 여부 확인

---

## STEP 07. 신규 공고 판별

### 목적
매주 실행할 때 같은 공고를 반복 추천하지 않도록 합니다.

### 1차 기준
`job_url`

```text
지난 실행 URL
+
이번 실행 URL
↓
새로 나타난 URL만 신규 공고
```

### 보조 키 후보
- 회사명 + 공고 제목 + 마감일

### 초기 저장 방식
별도 DB를 추가하지 않고 CSV 또는 JSON으로 시작합니다.

예:

```text
data/processed/jobs_history.csv
```

### 완료 조건
- 기존 공고 / 신규 공고를 분리
- 신규 판별 기준 설명 가능

---

## STEP 08. 기본 분석 / 관련 공고 필터링

### 목적
Gemini 호출 전에 Python으로 계산 가능한 사실을 먼저 만듭니다.

### 기본 분석 후보
- 이번 주 신규 공고 수
- 회사별 공고 수
- 지역별 공고 수
- 경력 조건 분포
- 검색어별 발견 건수
- 주요 직무 키워드

### 완료 조건
- 통계가 코드 계산 결과임을 확인
- 표를 직접 보고 이상 여부 검증

---

## STEP 09. Gemini API 연동

### 목적
검증된 일부 신규 공고를 Gemini에 전달해 텍스트 해석을 수행합니다.

### Gemini 역할
- 공고 핵심 내용 요약
- 요구 기술 추출
- 직무 유형 분류
- AX 관련성 설명
- 추천 이유 작성

### Gemini에게 맡기지 않을 것
- 신규 공고 개수
- 회사별 공고 수
- 지역별 공고 수

이 값들은 pandas가 계산합니다.

### 보안
API Key는 코드에 직접 작성하지 않습니다.

`.env`

```text
GEMINI_API_KEY=***
```

`.gitignore`

```text
.env
.venv/
__pycache__/
```

### 완료 조건
- API 호출 성공
- 키가 Git에 포함되지 않음
- 응답을 Notebook에서 직접 확인

---

## STEP 10. Gemini 결과 검증

### 목적
API 호출 성공과 분석 성공을 구분합니다.

### 최소 검증
몇 개의 공고를 원문과 Gemini 결과로 직접 비교합니다.

### 기록 예

```markdown
## Gemini 결과 검증

### 공고 1
- 원문에서 확인한 기술:
- Gemini가 추출한 기술:
- 누락:
- 과도한 해석:
- 사용 가능 여부:
```

### 완료 조건
- 누락 / 과도한 해석 여부 기록
- 실제 보고서에 사용 가능한지 사람이 판단

---

## STEP 11. Markdown 보고서 생성

### 목적
분석 결과를 사람이 읽을 수 있는 주간 보고서로 만듭니다.

### 기본 구조

```markdown
# 주간 AX 채용 동향

실행일: YYYY-MM-DD

## 1. 이번 주 요약
- 신규 공고:
- AX 관련성이 높은 공고:
- 주요 키워드:

## 2. 주요 동향

## 3. 추천 공고

## 4. 데이터 기준
- 검색어:
- 수집 시각:
- 분석 대상 건수:

## 5. 주의사항
- 실제 지원 전 원문 공고를 다시 확인할 것
```

### 완료 조건
- pandas가 만든 사실과 Gemini 설명을 구분
- Markdown 파일 생성 확인

---

## STEP 12. Slack 발송

### 목적
보고서를 실제 알림 채널로 전송합니다.

### 확인 항목
- HTTP 상태
- 메시지 도착
- 한글 깨짐
- 링크 표시
- 메시지 길이

### 완료 조건
- 실제 Slack에서 메시지 확인

---

## STEP 13. Gmail 발송

### 목적
최종 보고서를 이메일로도 받을 수 있도록 확장합니다.

### 주의
- 인증정보는 코드에 넣지 않음
- 로컬은 `.env`
- GitHub Actions에서는 Secrets

### 완료 조건
- 실제 메일 수신 확인

---

## STEP 14. 함수화

### 목적
Notebook에서 검증된 코드를 재사용 가능한 함수로 옮깁니다.

### 함수 예시

```python
def collect_jobs():
    ...

def clean_jobs(df):
    ...

def find_new_jobs(df, history_df):
    ...

def analyze_jobs(df):
    ...

def summarize_with_gemini(df):
    ...

def create_report(analysis, summaries):
    ...

def send_slack(report):
    ...

def send_email(report):
    ...
```

### 원칙
- Notebook = 검증 기록
- `src/` = 재사용 가능한 운영 코드

### 완료 조건
- Notebook에서 검증된 기능이 함수로 분리됨
- 함수 단위 실행 확인

---

## STEP 15. main.py 통합

### 목적
전체 실행 순서를 한 파일에서 조정합니다.

### 구조 예

```python
def main():
    jobs = collect_jobs()
    clean = clean_jobs(jobs)
    new_jobs = find_new_jobs(clean)
    analysis = analyze_jobs(new_jobs)
    summaries = summarize_with_gemini(new_jobs)
    report = create_report(analysis, summaries)
    send_slack(report)
    send_email(report)

if __name__ == "__main__":
    main()
```

### 완료 조건

```powershell
python main.py
```

가 로컬에서 끝까지 성공해야 합니다.

---

## STEP 16. 로컬 전체 실행 검증

### 체크리스트
- [ ] 수집 건수가 0이 아닌가?
- [ ] 필수 컬럼이 존재하는가?
- [ ] 중복 URL이 예상 범위인가?
- [ ] 날짜 변환 실패가 없는가?
- [ ] 신규 공고 판별이 정상인가?
- [ ] Gemini 응답이 원문과 크게 다르지 않은가?
- [ ] Markdown 보고서가 생성되는가?
- [ ] Slack에 도착하는가?
- [ ] Gmail에 도착하는가?
- [ ] 오류가 발생했는데 성공한 것처럼 보이지 않는가?

### 로그 후보
- 실행 시각
- 수집 건수
- 신규 공고 수
- Gemini 처리 건수
- Slack 성공 여부
- Email 성공 여부
- 오류 메시지

---

## STEP 17. GitHub Actions 수동 실행

### 목적
로컬에서 성공한 `python main.py`를 GitHub 환경에서도 실행합니다.

### 순서
1. Workflow 작성
2. 처음에는 schedule을 넣지 않음
3. `workflow_dispatch`로 수동 실행
4. GitHub Actions의 Run workflow 실행
5. 실패 시 환경 차이 확인

### 주요 확인
- requirements.txt
- 파일 경로
- 환경변수
- GitHub Secrets
- 운영체제 차이
- 네트워크 요청

### 완료 조건
- 수동 Workflow 성공

---

## STEP 18. GitHub Actions 주간 자동 실행

### 목적
검증된 Pipeline을 매주 자동 실행합니다.

### 예

```yaml
on:
  workflow_dispatch:
  schedule:
    - cron: "0 0 * * 1"
```

위 예시는 UTC 월요일 00:00, 한국시간 월요일 오전 9시입니다.

### 완료 조건
- 수동 실행 성공 후 schedule 추가
- Secrets 정상 연결
- 주간 자동 실행 설정 확인

---

# 6. 매 STEP 공통 Notebook 작성 패턴

모든 분석 단계는 아래 패턴을 사용합니다.

## Markdown Cell — 작업 계획

```markdown
# STEP XX. 작업명

## 작업 계획
이번 단계에서 무엇을 확인할지 작성
```

## Code Cell — 실행

현재 STEP에 필요한 최소 코드만 작성합니다.

## Code Cell — 확인

예:

```python
print(df.shape)
display(df.head())
```

## Markdown Cell — 결과 해석

```markdown
## 실행 결과 해석

- 실행 성공 여부:
- 확인한 데이터:
- 예상과 다른 부분:
- 다음 단계 진행 가능 여부:
- 추가 확인 사항:
```

---

# 7. Orchestrator에게 매번 전달할 기본 프롬프트 구조

```text
나는 Python 데이터 분석 초보자입니다.

현재 프로젝트:
chapter11/ax-job-agent

현재 완료 단계:
[PROGRESS.md 내용]

이번에는 STEP XX 하나만 진행합니다.

다음 원칙을 지켜 주세요.

1. 왜 이 작업을 하는지 먼저 설명합니다.
2. 현재 STEP을 더 작은 작업으로 나눕니다.
3. 아직 다음 STEP의 코드는 만들지 않습니다.
4. Jupyter에서 셀 단위로 검증할 수 있게 합니다.
5. 로컬 Coding Agent에게 전달할 프롬프트를 작성합니다.
6. 완료 조건을 명확히 작성합니다.
7. 실행 결과를 내가 확인한 뒤에만 다음 단계로 넘어갑니다.
```

---

# 8. Local Coding Agent에게 전달할 기본 원칙

Claude Code / Codex / Copilot 중 어떤 Agent를 사용해도 아래 규칙은 동일합니다.

```text
현재 STEP 하나만 작업한다.

작업 전:
- 현재 프로젝트 폴더 확인
- 관련 파일 확인
- 기존 코드를 먼저 읽기

작업 중:
- 현재 STEP에 필요한 최소 변경만 수행
- 임의로 다음 STEP 구현 금지
- 불필요한 dependency 추가 금지
- 민감정보 하드코딩 금지

작업 후:
- 변경 파일 목록 보고
- 실행 명령 보고
- 실제 실행 결과 확인
- 완료 조건 충족 여부 보고
```

---

# 9. Git / 보안 규칙

다음은 Git에 올라가면 안 됩니다.

- `.env`
- 실제 API Key
- 실제 Slack Webhook URL
- Gmail 비밀번호 / App Password
- Notebook 출력에 노출된 인증정보

예제만 저장합니다.

`.env.example`

```text
GEMINI_API_KEY=
SLACK_WEBHOOK_URL=
GMAIL_USER=
GMAIL_APP_PASSWORD=
```

---

# 10. 작업 중단 조건

아래 중 하나라도 발생하면 다음 STEP으로 넘어가지 않습니다.

- 코드 실행 오류
- 예상 컬럼 누락
- 수집 결과가 비정상
- 중복 처리 기준 불명확
- 날짜 변환 실패를 설명하지 못함
- Gemini 응답과 원문 차이가 큼
- 인증정보가 코드에 노출됨
- `python main.py`가 로컬에서 실패
- GitHub Actions 수동 실행 실패

---

# 11. 최종 운영 구조

```text
매주 1회
↓
GitHub Actions
↓
main.py
↓
Crawler
↓
pandas
├─ 정제
├─ 중복 제거
├─ 신규 공고 판별
├─ 관련 공고 필터
└─ 기본 통계
↓
Gemini API
├─ 요약
├─ 기술 추출
├─ 직무 분류
└─ 추천 이유
↓
Report
↓
Slack + Gmail
```

---

# 12. 최종 자기점검

프로젝트 종료 시 다음 질문에 자신의 말로 답할 수 있어야 합니다.

- [ ] Fork와 Clone의 차이는?
- [ ] origin과 upstream의 차이는?
- [ ] GPT Web Orchestrator와 Local Coding Agent의 차이는?
- [ ] 왜 Notebook에서 셀 단위 검증을 했는가?
- [ ] DataFrame 한 행은 무엇인가?
- [ ] 중복 공고와 신규 공고는 어떻게 판별하는가?
- [ ] pandas와 Gemini의 역할 차이는?
- [ ] Gemini 결과는 어떻게 검증하는가?
- [ ] `main.py`는 무엇을 담당하는가?
- [ ] GitHub Actions는 왜 마지막에 추가하는가?
- [ ] GitHub Secrets는 왜 필요한가?
- [ ] 전체 Agent Pipeline을 설명할 수 있는가?

---

# 13. 가장 중요한 운영 원칙

> 한 번에 전체 프로그램을 만들지 않습니다.

> 현재 STEP 하나만 구현하고, 직접 실행하고, 결과를 확인하고, 해석한 뒤 다음 STEP으로 넘어갑니다.

> Agent의 완료 보고보다 실제 데이터와 실행 결과를 우선합니다.
