# 주소록 웹앱 Claude 코딩 실습 가이드 (9주차 웹프로젝트 재도전)

> VS Code에서 `Ctrl + Shift + V`를 누르면 이 파일을 보기 좋게 읽을 수 있어요.
> 왼쪽에 이 가이드, 오른쪽에 VS Code 작업 화면을 띄워 두고 순서대로 따라 하세요.

## 이 가이드로 하는 일

| 과제 | 이 가이드에서 |
|---|---|
| 1) 9주차 문제점 파악 → Claude 코딩 성공 | A장(문제점 표) + B장(0~8단계 프롬프트) |
| 2) 반복 연습 + 중요한 VS Code 화면 캡처 → GitHub | C장(반복 방법) + D장(캡처할 화면 목록) + E장(README·업로드) |
| 3) GitHub URL 제출 | E장 마지막 |

- 걸리는 시간(대략): 1회차 60~90분, 2회차 40~60분 정도예요. 컴퓨터 상태에 따라 달라질 수 있어요.
- 같이 들어 있는 `참고용_완성본/` 폴더는 Claude가 만든 코드가 이상할 때 **비교용**으로만 쓰세요. 제출하는 코드는 여러분이 Claude Code로 만든 코드여야 해요.

---

## A. 9주차에 무엇이 문제였나 (과제 1의 "문제점 파악")

| # | 9주차에 생긴 문제 | 원인 | 이번에 바꾸는 방법 |
|---|---|---|---|
| 1 | 첫 커밋이 끝까지 실패 | Git 이름·이메일 미설정 | 0단계에서 `git config --global` 먼저 |
| 2 | `source` 명령 오류 | 교안이 Mac 명령 우선 | 모든 명령을 Windows PowerShell 기준으로 |
| 3 | 가상환경 활성화 빨간 오류 | PowerShell 실행 정책이 스크립트 차단 | 0단계에서 `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` |
| 4 | 교안에 있는 승인 화면이 안 나옴 | 학생마다 모드가 auto/accept edits로 제각각 | 0단계에서 manual 모드 맞추기 |
| 5 | 프롬프트를 넣어도 반응 없음 | `Login expired` | 0단계에서 `/login` 확인 |
| 6 | "포트 5000 사용 중" 오류 | Claude가 서버를 백그라운드로 켜 둠 + 터미널 1개만 사용 | 터미널 2개 + CLAUDE.md "서버 실행 금지" + 승인 화면에서 No |
| 7 | `no such table: contacts` | init-db 명령을 빠뜨림 | 앱 시작 시 `CREATE TABLE IF NOT EXISTS`로 자동 생성 |
| 8 | "이게 된 건가?" 판단 불가 | 결과가 안 보이는 단계, 성공 기준 모호 | 11단계 → 9단계(0~8), 단계마다 성공 확인·막혔을 때 |

---

## B. 단계별 실습 (0~8단계)

### 공통 규칙
- **터미널 ① = Claude 전용**, **터미널 ② = 서버 전용**. Claude 입력창에는 Claude에게 하는 말만 쓰세요.
- 승인 화면이 나오면 **내용을 읽고** 1번 Yes. **2번 "Yes, and switch to accept edits"는 누르지 마세요.**
- Claude가 `python app.py`(서버 실행) 승인을 물으면 **No** → "서버는 내가 실행할게"라고 답하세요.
- `pytest` 실행 승인은 Yes 해도 돼요(실행하고 끝나는 명령).
- 단계마다 **성공 확인**이 다 체크돼야 다음 단계로 넘어가요.
- 📸 표시가 캡처할 장면이에요. D장 파일 이름 규칙대로 저장하세요.

---

### 0단계 — 준비 체크리스트 (Claude 없이 PowerShell에서)

VS Code에서 `` Ctrl + ` `` 로 터미널을 열고 한 줄씩 입력하세요.

```powershell
git config --global user.name "본인이름(영문)"
git config --global user.email "GitHub에 등록한 이메일"
git config --global --list          # user.name, user.email 보이면 성공
python --version                    # 3.10 이상이면 OK
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned   # 물어보면 Y 입력
mkdir C:\study\addressbook
cd C:\study\addressbook
code . -r                           # 이 폴더로 VS Code 다시 열기
```

새로 열린 VS Code에서:
1. 터미널 열기 → 이름을 오른쪽 클릭 → **이름 바꾸기 "Claude"** → `claude` 실행
2. 시작 화면에 `Not logged in` / `Login expired`가 보이면 `/login`
3. 입력창 아래 모드 표시를 보고 `Shift + Tab`으로 **manual**에 맞추기 (표시 이름은 버전마다 조금 다를 수 있어요)
4. 터미널 패널 `+` → 새 터미널 이름을 **"서버"** 로 바꾸기

**성공 확인**
- [ ] `git config --global --list`에 이름·이메일이 보인다
- [ ] Claude 시작 화면에 내 요금제(예: Claude Pro)가 보이고 로그인 오류가 없다
- [ ] 모드가 manual이다
- [ ] 터미널이 "Claude", "서버" 두 개다

**막혔을 때**
| 증상 | 해결 |
|---|---|
| `Set-ExecutionPolicy` 거부(학교 PC) | 이후 `.\.venv\Scripts\python app.py`, `.\.venv\Scripts\python -m pytest -v`처럼 가상환경 파이썬을 직접 지정 |
| `python`을 찾을 수 없음 | `py --version` 시도 → 되면 이후 `python` 대신 `py` |
| 시작 화면에 `API Usage Billing` | `/login` 다시 하고 구독 계정(Claude Pro 등) 쪽으로 로그인 |

📸 **00_setup.png** — `git config --global --list` 결과 + 터미널 2개(Claude/서버) 이름이 보이는 화면
📸 **01_login_manual.png** — Claude 시작 화면(요금제 표시) + 입력창 아래 manual 모드 표시

---

### 1단계 — Plan 모드로 설계하기

`Shift + Tab`으로 **plan 모드**로 바꾼 뒤 Claude 터미널에 입력:

```
Flask와 SQLite로 주소록 웹앱을 만들려고 해. 실행 환경은 Windows PowerShell이야.
아직 파일은 만들지 말고 계획만 세워 줘.
1) 폴더와 파일 구조
2) contacts 테이블 설계: id, 이름(필수), 전화번호, 이메일, 메모, 등록 시각.
   나중에 칸을 추가하지 않도록 처음부터 모든 칸을 넣어 줘.
3) 단계 순서: 2 골격과 첫 화면 → 3 DB와 등록·목록 → 4 검색 → 5 수정 → 6 삭제
   → 7 화면 다듬기 → 8 테스트와 README
서버는 내가 터미널에서 직접 실행할 거야.
```

계획이 나오면 승인 화면에서 **바로 실행하지 말고** 계획 내용만 확인한 뒤, `Shift + Tab`으로 **반드시 manual로 되돌리세요.**

**성공 확인**
- [ ] 테이블 설계에 6개 칸(id, name, phone, email, memo, created_at)이 다 있다
- [ ] 파일은 하나도 생기지 않았다 (탐색기가 비어 있음)
- [ ] 모드를 manual로 되돌렸다

📸 **02_plan.png** — Plan 모드에서 나온 테이블 설계 부분

---

### 2단계 — 골격과 첫 화면

```
1단계 계획대로 프로젝트 골격을 만들어 줘.
① CLAUDE.md에 이 규칙을 적어 줘:
   - 실행 환경은 Windows PowerShell
   - 서버(python app.py)는 Claude가 실행하지 말고 실행 명령만 알려 준다
   - DB 테이블은 앱 시작 시 CREATE TABLE IF NOT EXISTS로 자동 생성한다 (init-db 명령 만들지 않기)
   - SQL 값은 반드시 ? 파라미터 바인딩, UPDATE와 DELETE에는 반드시 WHERE id = ?
   - 테이블 구조는 1단계 설계대로 유지
   - 단계가 끝나면 "N단계: 내용" 형식으로 커밋
② .gitignore (.venv, __pycache__, *.db, .pytest_cache)
③ requirements.txt (flask, pytest)
④ app.py와 templates/index.html — 첫 화면에 "주소록" 제목만 보이면 돼
⑤ git init 하고 "2단계: 골격과 첫 화면"으로 커밋
서버는 실행하지 말고, 가상환경 만들기·패키지 설치·서버 실행 명령만 PowerShell 기준으로 알려 줘.
```

**승인할 때 확인할 점**: CLAUDE.md에 "서버 실행 금지"가 들어갔는지, .gitignore에 `*.db`가 있는지

터미널 ②(서버)에서:
```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1        # 줄 앞에 (.venv) 가 붙으면 성공
pip install -r requirements.txt
python app.py
```
브라우저에서 `http://127.0.0.1:5000` 열기

**성공 확인**
- [ ] 브라우저에 "주소록" 제목이 보인다
- [ ] 터미널 ②에 `GET / HTTP/1.1" 200` 기록이 찍힌다
- [ ] `git log --oneline`(터미널 ②에서 Ctrl+C 후, 또는 새 터미널)에 "2단계" 커밋이 있다

**막혔을 때**
| 증상 | 해결 |
|---|---|
| 커밋 실패 "Please tell me who you are" | 0단계 git config 다시 → Claude에게 "다시 커밋해 줘" |
| `Activate.ps1` 빨간 오류 | 0단계 실행 정책 다시 |
| `Address already in use` / 포트 사용 중 | Claude에게 "백그라운드에서 실행 중인 서버를 꺼 줘" → 터미널 ②에서 다시 실행 |
| `ModuleNotFoundError: flask` | 줄 앞에 `(.venv)`가 있는지 확인 → 없으면 Activate 다시 |

📸 **03_claude_md.png** — 에디터에 CLAUDE.md가 열려 있고 왼쪽 탐색기에 파일 구조가 보이는 화면
📸 **04_first_screen.png** — 터미널 ②의 `(.venv)` + 서버 로그 + 브라우저 첫 화면이 한 장에 (VS Code와 브라우저를 나란히)

---

### 3단계 — DB와 등록·목록

```
3단계: 연락처 등록과 목록 기능을 만들어 줘.
- 앱이 시작될 때 CREATE TABLE IF NOT EXISTS로 contacts 테이블을 자동으로 만들어 줘 (1단계 설계 그대로)
- 첫 화면 위쪽에 이름·전화번호·이메일·메모 입력 폼, 아래쪽에 연락처 목록 표
- POST /add 로 저장하고 다시 첫 화면으로 돌아가기
- 이름이 비어 있으면 저장하지 말고 "이름은 꼭 입력해 주세요" 안내를 보여 줘
- SQL은 ? 바인딩으로
서버는 실행하지 말고, 끝나면 "3단계: DB와 등록·목록"으로 커밋해 줘.
```

**승인할 때 확인할 점 (diff에서 찾기)**
- `CREATE TABLE IF NOT EXISTS` 가 있다 (IF NOT EXISTS 없으면 재시작 때 오류/데이터 삭제 위험)
- `INSERT ... VALUES (?, ?, ?, ?)` 처럼 `?`를 쓴다 (f-string으로 SQL을 만들면 거절하고 고쳐 달라고 하기)

서버 재시작: 터미널 ②에서 `Ctrl + C` → `python app.py`

**성공 확인**
- [ ] 연락처 3개를 등록하면 목록에 3개가 보인다
- [ ] 이름을 비우고 추가하면 저장되지 않고 안내가 나온다
- [ ] 서버를 껐다 켜도 3개가 그대로 있다
- [ ] 터미널 ②에 `POST /add` → `GET /` 순서로 기록이 찍힌다

**막혔을 때**
| 증상 | 해결 |
|---|---|
| `Internal Server Error` | 터미널 ②의 빨간 오류 **마지막 줄**을 복사 → Claude에게 붙여 넣고 "이 오류 고쳐 줘" |
| `no such table` | Claude에게 "앱 시작 시 테이블 자동 생성이 되는지 확인해 줘" |
| 꼬여서 처음부터 하고 싶음 | 서버 끄고 `addressbook.db` 삭제 → 서버 다시 켜기 (테이블 자동 생성) |

📸 **05_approve_create_table.png** — 승인 화면 diff에서 `CREATE TABLE IF NOT EXISTS`와 `?` 바인딩이 보이는 부분
📸 **06_add_list.png** — 3명 등록된 목록 + 터미널 ②의 `POST /add`, `GET /` 기록
📸 **07_name_required.png** — 이름 비우고 추가했을 때 안내 문구

---

### 4단계 — 검색

```
4단계: 목록 위에 검색창을 만들어 줘.
- GET /?q=검색어 로 이름·전화번호·이메일에서 LIKE ? 로 찾기
- 검색 중이면 "'검색어' 검색 결과 N건"과 "전체 보기" 링크 보여 주기
- 결과가 없으면 "검색 결과가 없어요" 표시
서버는 실행하지 말고, 끝나면 "4단계: 검색"으로 커밋해 줘.
```

**승인할 때 확인할 점**: `LIKE ?` 에 값을 `%검색어%`로 **바인딩**하는지 (SQL 문자열에 검색어를 직접 붙이면 안 됨)

**성공 확인**
- [ ] 이름 일부로 검색하면 해당 연락처만 나온다
- [ ] 없는 이름 검색 → "검색 결과가 없어요"
- [ ] "전체 보기"를 누르면 다시 전체 목록

📸 **08_search.png** — 검색 결과 화면(주소창의 `?q=` 가 보이게)

---

### 5단계 — 수정

```
5단계: 목록 각 줄에 "수정" 링크를 만들어 줘.
- GET /edit/<id> 는 기존 값이 채워진 수정 폼, POST /edit/<id> 는 저장
- UPDATE ... WHERE id = ? 로 해당 연락처만 바꾸기
- 등록과 같은 입력 검사(이름 필수) 적용
- 없는 id면 목록으로 돌아가며 안내
테이블 구조는 바꾸지 마. 서버는 실행하지 말고, 끝나면 "5단계: 수정"으로 커밋해 줘.
```

**승인할 때 확인할 점**: `UPDATE` 문에 **`WHERE id = ?`** 가 있는가 (없으면 모든 연락처가 같은 값으로 덮어써져요!)

**성공 확인**
- [ ] 수정 폼에 기존 값이 채워져 있다
- [ ] 전화번호를 바꾸고 저장하면 그 사람만 바뀐다
- [ ] 수정에서 이름을 비우면 저장되지 않는다

📸 **09_approve_update.png** — 승인 화면 diff에서 `UPDATE ... WHERE id = ?`

---

### 6단계 — 삭제

```
6단계: 목록 각 줄에 "삭제" 버튼을 만들어 줘.
- POST /delete/<id> 로만 삭제 (GET 링크로 삭제하지 않기)
- 누르면 "삭제할까요?" 확인창
- DELETE FROM contacts WHERE id = ? 로 해당 연락처만 삭제
서버는 실행하지 말고, 끝나면 "6단계: 삭제"로 커밋해 줘.
```

**승인할 때 확인할 점**: `DELETE` 문에 **`WHERE id = ?`** 가 있는가 (없으면 전체 삭제), 삭제가 **POST**인가

**성공 확인**
- [ ] 확인창에서 취소하면 삭제되지 않는다
- [ ] 확인하면 그 사람만 삭제되고 나머지는 남는다

📸 **10_approve_delete.png** — 승인 화면 diff에서 `DELETE ... WHERE id = ?`

---

### 7단계 — 화면 다듬기

```
7단계: 화면을 보기 좋게 다듬어 줘.
- static/style.css 로 분리, 카드 형태 레이아웃
- 연락처 개수("N명") 표시, 추가·수정·삭제 후 결과 메시지(flash) 표시
- 연락처가 없을 때 안내 문구
테이블 구조와 기능은 바꾸지 마. 서버는 실행하지 말고, 끝나면 "7단계: 화면 다듬기"로 커밋해 줘.
```

**성공 확인**
- [ ] 화면이 바뀌었다 (안 바뀌면 브라우저에서 `Ctrl + F5`)
- [ ] 추가·수정·삭제 후 초록색 결과 메시지가 나온다

📸 **11_final_screen.png** — 완성된 주소록 화면 (VS Code의 style.css와 나란히)

---

### 8단계 — 테스트, README, 마무리

```
8단계: 마무리해 줘.
① test_app.py: 실제 addressbook.db 대신 pytest의 tmp_path로 매번 새 임시 DB를 쓰게 해 줘.
   첫 화면, 등록, 이름 빈칸 거부, 검색, 수정, 삭제(해당 연락처만), 
   SQL 인젝션 문자열을 넣어도 테이블이 지워지지 않음, 재시작 후 데이터 유지를 테스트해 줘.
   테스트하기 쉽게 app.py가 create_app() 형태가 아니면 그렇게 바꿔 줘.
② python -m pytest -v 를 실행해서 결과를 보여 줘.
③ README.md: 프로젝트 소개, 기능, PowerShell 실행 방법.
④ "8단계: 테스트와 README"로 커밋하고 git log --oneline 결과를 보여 줘.
서버는 실행하지 마.
```

**성공 확인**
- [ ] `python -m pytest -v` 가 모두 PASSED
- [ ] `git log --oneline`에 2~8단계 커밋이 순서대로 있다

📸 **12_pytest.png** — `pytest -v` 결과의 PASSED 목록
📸 **13_git_log.png** — `git log --oneline` 단계별 커밋 이력 (또는 VS Code 왼쪽 "소스 제어" 그래프)

---

### 🔥 꼭 남기면 좋은 "해결 흔적" 캡처 (생기면 무조건 찍기)

실습 중 오류가 나면 오히려 좋은 기회예요. 이게 진짜 "익힌 흔적"이에요.

📸 **14_error.png** — 터미널 ②의 빨간 오류 메시지
📸 **15_error_fix.png** — 그 오류를 Claude에게 붙여 넣은 장면과 Claude의 수정 diff
📸 **16_deny_server.png** — Claude가 서버 실행 승인을 물었을 때 No를 고른 장면 (나오면)

---

## C. 반복 연습 방법 (과제 2의 "반복연습")

| 회차 | 폴더 | 방법 | 목표 |
|---|---|---|---|
| 1회차 | `C:\study\addressbook` | B장 프롬프트를 **복사해서** 그대로 | 끝까지 성공해 보기 |
| 2회차 | `C:\study\addressbook2` | 프롬프트를 **보지 않고 내 말로** 쓰기 (단계 제목과 성공 확인만 보기) | 무엇을 요구해야 하는지 익히기 |
| 3회차(선택) | `C:\study\addressbook3` | 2회차 + 기능 하나 추가 (예: 이름순/최근 등록순 정렬 버튼) | 응용 |

- 2회차부터는 0단계 중 git config·실행 정책은 이미 되어 있으니 확인만 하면 돼요.
- 회차마다 **날짜, 걸린 시간, 막힌 곳, 해결 방법**을 메모해 두세요 → README 반복 연습 표에 그대로 씁니다.
- 반복한 증거로 좋은 캡처:
  📸 **20_repeat_folders.png** — VS Code 탐색기나 파일 탐색기에 addressbook, addressbook2 폴더가 함께 보이는 화면
  📸 **21_round2_git_log.png** — 2회차 `git log --oneline` (날짜가 다르면 `git log --format="%h %ad %s" --date=short`)

---

## D. 캡처하는 방법

- `Win + Shift + S` → 영역 선택 → 알림 클릭 → 저장. 또는 캡처 도구에서 저장.
- 프로젝트 안에 `screenshots` 폴더를 만들어 위의 파일 이름 그대로 저장하세요. (예: `screenshots/05_approve_create_table.png`)
  - 파일 이름은 **영문·숫자·밑줄**만 쓰세요. 한글이나 공백이 있으면 GitHub에서 이미지가 깨질 수 있어요.
  - 1회차·2회차를 나누고 싶으면 `screenshots/round1/`, `screenshots/round2/`
- **캡처 팁**
  - VS Code 전체 창이 나오게: 탐색기(파일 목록) + 에디터 + 터미널이 같이 보이면 "실제로 한 흔적"이 가장 잘 드러나요.
  - 브라우저 결과는 VS Code와 나란히(`Win + ←`, `Win + →`) 놓고 한 장에.
  - 작업표시줄 시계가 보이게 찍으면 날짜·시간이 자연스럽게 남아요.
- **개인정보 주의**: Claude 시작 화면이나 `git config` 결과에 이메일이 보이면 그림판 등으로 가리고 올리세요. API 키·비밀번호가 보이는 화면은 절대 올리지 마세요.

---

## E. GitHub에 올리기 (직접 하실 부분)

1. 마지막 회차 프로젝트 폴더에 `screenshots/` 폴더가 있는지 확인하고, 같이 받은 `README_제출용.md`의 내용으로 Claude가 만든 `README.md`를 바꾸기(또는 아래쪽에 붙이기) → `(직접 작성)` 부분 채우기
   - 찍지 않은 캡처(예: 16번은 서버 실행 승인이 안 나오면 없음)는 README에서 그 줄을 지우세요.
   - README의 "중요한 이유"는 예시예요. 본인이 판단한 말로 고치면 더 좋아요.
2. github.com → New repository → 이름 예: `claude-addressbook` → **Public**, README 추가 체크 **해제** → Create
3. 터미널에서:
```powershell
git add README.md screenshots
git commit -m "캡처와 README 추가"
git branch -M main
git remote add origin https://github.com/내아이디/claude-addressbook.git
git push -u origin main
```
4. GitHub 저장소 첫 화면에서 README의 캡처 이미지가 **바로 보이는지** 확인 (과제 조건: "github에서 바로 확인")
   - 이미지가 깨지면 README의 경로와 실제 파일 이름(대소문자, 한글, 공백)이 같은지 확인
5. 주소창의 저장소 URL을 학습사이트 [과제제출]에 제출
