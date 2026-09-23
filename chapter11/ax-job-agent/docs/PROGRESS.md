# AX 채용정보 Agent Pipeline — 진행 현황

> 이 파일은 **작업 시작 시 가장 먼저 확인하고, 작업 종료 시 반드시 갱신**합니다.

## 1. 현재 상태 요약

- 현재 브랜치: `ax-job-agent`
- 프로젝트 경로: `chapter11/ax-job-agent/`
- 현재 단계: **STEP 01 개발환경 확인 진행 중**
- 마지막 확인 시점 기준 완료:
  - [x] 원본 PUBLIC 저장소 Fork
  - [x] 자신의 Fork 로컬 Clone
  - [x] `ax-job-agent` 로컬 브랜치 생성
  - [x] `chapter11/ax-job-agent` 폴더 생성
  - [x] Python 가상환경 `.venv` 생성
  - [x] 가상환경 활성화
  - [x] pip 업그레이드
  - [x] `pandas requests beautifulsoup4 jupyter python-dotenv` 설치
  - [x] Python 버전 확인: **3.13.7**
  - [ ] `where.exe python`으로 현재 Python 경로 확인
  - [ ] VS Code Python Interpreter가 `.venv`인지 확인
  - [ ] `notebooks/ax_job_pipeline.ipynb` 생성
  - [ ] Notebook 환경 확인 Cell 실행
  - [ ] STEP 01 완료 판정

## 2. 다음 작업

다음 작업은 **STEP 01 개발환경 확인 마무리**입니다.

### 바로 실행할 것

PowerShell:

```powershell
where.exe python
```

맨 위 결과가 아래 형태인지 확인합니다.

```text
...\chapter11\ax-job-agent\.venv\Scripts\python.exe
```

그다음:

1. VS Code에서 Python Interpreter가 같은 `.venv`인지 확인
2. `notebooks/ax_job_pipeline.ipynb` 생성
3. Python / Platform 확인 Cell 실행
4. pandas / requests / BeautifulSoup import 확인 Cell 실행
5. 실제 출력 확인
6. 이상이 없으면 STEP 01 완료 처리

## 3. 전체 STEP 체크리스트

- [x] STEP 00. Git / 프로젝트 준비
- [ ] STEP 01. 개발환경 확인  ← **현재**
- [ ] STEP 02. 수집 데이터 명세
- [ ] STEP 03. 채용공고 페이지 접근 테스트
- [ ] STEP 04. 소량 데이터 수집
- [ ] STEP 05. DataFrame 생성
- [ ] STEP 06. 전처리 / 중복 제거
- [ ] STEP 07. 신규 공고 판별
- [ ] STEP 08. 기본 분석 / 관련 공고 필터링
- [ ] STEP 09. Gemini API 연동
- [ ] STEP 10. Gemini 결과 검증
- [ ] STEP 11. Markdown 보고서 생성
- [ ] STEP 12. Slack 발송
- [ ] STEP 13. Gmail 발송
- [ ] STEP 14. 함수화
- [ ] STEP 15. main.py 통합
- [ ] STEP 16. 로컬 전체 실행 검증
- [ ] STEP 17. GitHub Actions 수동 실행
- [ ] STEP 18. GitHub Actions 주간 실행

## 4. 작업 시작 체크

매번 아래를 확인합니다.

- [ ] 현재 브랜치 확인
- [ ] `git status` 확인
- [ ] `.venv` 활성화 여부 확인
- [ ] 이 파일에서 현재 STEP 확인
- [ ] `PROJECT_GUIDE.md`에서 해당 STEP 목적 확인
- [ ] GPT Web에게 현재 STEP 하나만 계획 요청

권장 명령:

```powershell
git branch
git status
```

가상환경이 꺼져 있으면:

```powershell
.\.venv\Scripts\Activate.ps1
```

> 현재 터미널 위치가 `chapter11/ax-job-agent`일 때의 명령입니다.

## 5. 작업 종료 체크

현재 STEP 작업이 끝나면:

- [ ] 실행 결과 직접 확인
- [ ] 오류 / 예상과 다른 점 기록
- [ ] 완료 조건 충족 여부 판정
- [ ] 이 파일의 체크박스 갱신
- [ ] 다음 작업을 한 줄로 명시
- [ ] 민감정보 포함 여부 확인
- [ ] `git status` / `git diff` 확인

## 6. 진행 기록

### 2026-09-23

완료:
- Fork
- Clone
- 실습 브랜치 생성
- 프로젝트 폴더 생성
- `.venv` 생성 및 활성화
- 기본 패키지 설치
- Python 3.13.7 확인
- 프로젝트 운영 가이드 문서 추가

다음:
- `where.exe python` 확인
- VS Code Interpreter 확인
- Notebook 생성 및 환경 검증
