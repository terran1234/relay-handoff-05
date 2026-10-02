# 인수인계 (AI A → AI B)

이 한 장만 읽고 이어서 작업할 수 있게 썼습니다. 대화 기록은 필요 없습니다.

- 작성: AI A (Claude), 2026-10-02 11:57 KST 멈춤
- 문서가 가리키는 코드 버전 ID: `20985908bd1353d826d49e5d8be9d263dd6c74c9` (저장소 `terran1234/daily-board-04`, 가지 `a-work`)

## 1. 목표
`daily-board-04` 환율 페이지의 "04 실제 일별 기록" 아래에 **기록 요약(최고·최저·평균)** 을 보여 준다. 검사 10개(T05-V01~V10, 정의는 이 저장소 `t05-card1.md` 3절)를 모두 통과시키는 것이 끝이다.
규칙: 단위 KRW per 1 USD, 소수 둘째 자리 반올림(half-up), 최고·최저에 KST 날짜 병기(동률이면 더 최근), 기존 보드 기능은 깨지면 안 됨.

## 2. 현재 상태
- 가지 `a-work`의 커밋 `20985908bd1353d826d49e5d8be9d263dd6c74c9`.
- `index.html`에 `paintSummary` 함수와 `<div id="summary">` 가 추가되어 있고, `t05-check.html`(검사 10개 실행 페이지)이 새로 있다.
- 핵심 요약(V01~V06)과 불러오기 실패 안내(V08)는 동작한다. V07·V09·V10은 **구현하지 않았다**(코드가 저장소에 없음).
- `main` 가지는 과제 4 제출물이라 시작 커밋 `8905f3d9c8b7f8d575fa668556016b8db2fd2672` 그대로다.

## 3. 실행 명령
필요한 것: Python 3 (3.8 이상이면 됨), 웹 브라우저. 설치할 패키지·환경값(.env)은 없다.
빈 폴더에서 (Windows PowerShell 기준):
```
Invoke-WebRequest https://github.com/terran1234/daily-board-04/archive/20985908bd1353d826d49e5d8be9d263dd6c74c9.zip -OutFile code.zip
Expand-Archive code.zip -DestinationPath .
cd daily-board-04-20985908bd1353d826d49e5d8be9d263dd6c74c9
python -m http.server 8000
```
브라우저에서 `http://localhost:8000/t05-check.html` 을 연다. (macOS/Linux는 `curl -L -o code.zip <같은 주소>` 후 `unzip code.zip`.)
git이 있으면 `git clone https://github.com/terran1234/daily-board-04` → `git checkout a-work` 도 된다.

## 4. 통과 검사
지금 **7 / 10 PASS**. 페이지 제목에도 `T05 7/10 PASS` 로 나온다.
- PASS: V01, V02, V03, V04, V05, V06, V08
- FAIL: V07, V09, V10

## 5. 남은 문제
1. **V07** — 값이 문자 `"1300"`인 줄이 숫자처럼 섞여 최저 1,300.00 / 평균 1,017.43이 나온다. 숫자가 아닌 줄은 빼고 `값이 숫자가 아닌 기록 1건 제외`를 보여야 한다.
2. **V09** — 원자료(`data/raw/날짜.json`의 `rates.KRW`)로 따로 계산한 값이 화면과 같을 때 `✓ 원자료와 일치`를 보여야 한다. (원자료가 없으면 `원자료를 읽지 못해 대조할 수 없습니다`)
3. **V10** — 다르면 `✗ 일치하지 않습니다`만 보이고 ✓는 없어야 한다.

## 6. 다음 행동
1. `index.html`에서 `function paintSummary` 를 찾는다. 위에 TODO 주석 2줄이 있다.
2. V07 → V09 → V10 순서로 고친다. 고칠 때마다 `t05-check.html`을 새로고침해 합계가 늘어나는지 본다.
3. 반올림은 `fmt2` 함수를 그대로 쓴다(`toFixed` 쓰면 1000.005가 1,000.00이 되어 V06이 깨진다).
4. 불일치 문구는 `일치하지 않습니다`로 시작한다(`원자료와 일치`가 들어가면 V09 검사가 오판한다).
5. `T05 10/10 PASS` 가 되면 `a-work`에 커밋하고 결과를 `b-results.md`에 남긴다.

## 7. 건드리지 말 것
- `main` 가지 전체(과제 4 제출물).
- 검사 10개의 정의와 기대값(`t05-card1.md` 3절). 검사를 바꿔서 통과시키지 않는다.
- `t05-check.html`의 판정 조건, 기존 보드·합성 시험대·어제 대비 계산, `engine.js`, `data/` 안의 실제 기록 파일.
- 이미 PASS인 V01~V06, V08이 깨지지 않게 한다.
