# B에게 보낼 글 (이 파일 전체를 복사해서 새 ChatGPT 대화에 붙여넣기)

아래 `=====` 줄 아래부터 끝까지를 복사하세요.

=====

너는 다른 AI가 하던 작업을 중간부터 이어받는 개발자야. 앞선 대화 기록은 없고, 아래 두 가지만 있어.

**1. 저장소** (코드 버전은 아래 SHA로 고정, 이 가지 말고는 건드리지 마)
- 저장소: https://github.com/terran1234/daily-board-04 (가지 `a-work`, 커밋 `20985908bd1353d826d49e5d8be9d263dd6c74c9`)
- index.html 전체: https://raw.githubusercontent.com/terran1234/daily-board-04/20985908bd1353d826d49e5d8be9d263dd6c74c9/index.html
- 검사 페이지: https://raw.githubusercontent.com/terran1234/daily-board-04/20985908bd1353d826d49e5d8be9d263dd6c74c9/t05-check.html
- 검사 10개 정의: https://raw.githubusercontent.com/terran1234/relay-handoff-05/main/t05-card1.md (3절)
- 링크를 열 수 없으면, 코드는 아래 "저장소 발췌"를 쓰고 검사 정의는 아래 인수인계 문서와 발췌로 판단해. 모르는 건 지어내지 말고 "모른다"고 말해.

**2. 인수인계 문서** (아래 그대로)

----- 인수인계 문서 시작 -----
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
----- 인수인계 문서 끝 -----

**저장소 발췌** (`index.html` 안의 관련 부분 그대로)

```js
async function loadDaily() {
  let rows, from, failed = false;
  try {
    const r = await fetch('data/daily.json', { cache: 'no-store' });
    if (!r.ok) throw new Error('HTTP ' + r.status);
    rows = await r.json(); from = 'data/daily.json';
  } catch (_) {          // before the first scheduled run: the single real record we already have
    failed = true;
    const { stored } = await loadStored();
    rows = [{ record_id: 'usd-krw-' + stored.kst_date, signal_id: 'usd-krw', record_date: stored.kst_date, record_timezone: 'Asia/Seoul', normalized_value: stored.value, unit: stored.unit,
      source_name: 'ExchangeRate-API', source_url: stored.source_url, source_time: stored.source_time_utc, first_fetched_at: stored.fetched_at_utc, last_fetched_at: stored.fetched_at_utc, raw_file: 'data/raw/' + stored.kst_date + '.json' }];
    from = 'data/latest.json (data/daily.json은 첫 자동 기록 뒤에 생김)';
  }
  rows = rows.slice().sort((a, b) => a.record_date.localeCompare(b.record_date));
  const raws = {};
  for (const r of rows) { try { raws[r.record_date] = JSON.parse(await (await fetch(r.raw_file, { cache: 'no-store' })).text()); } catch (_) { raws[r.record_date] = null; } }
  return { rows, raws, from, failed };
}

/* ================= record summary: max / min / average ================= */
// round half up on the decimal value (1000.005 -> 1,000.01); 1e-7 absorbs binary floating-point error
const fmt2 = x => (Math.floor(x * 100 + 0.5 + 1e-7) / 100).toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 });
// vals: [{ date, v }] sorted by date ascending; ties go to the most recent date
function summarize(vals) {
  let hi = vals[0], lo = vals[0], sum = 0;
  for (const p of vals) { if (p.v >= hi.v) hi = p; if (p.v <= lo.v) lo = p; sum += p.v; }
  return { hi, lo, avg: sum / vals.length };
}

function paintSummary({ rows, raws, failed }) {
  const box = $('summary');
  if (failed) { box.innerHTML = '<div class="err-box"><b>기록을 불러오지 못했습니다.</b> (data/daily.json) 위쪽 보드의 환율과 어제 대비는 그대로 표시됩니다.</div>'; return; }
  // TODO(T05-V07): skip rows whose normalized_value is not a finite number and show "N건 제외"
  // TODO(T05-V09/V10): recompute from raws[date].rates.KRW and show a match / mismatch line
  if (!rows.length) { box.innerHTML = '<div class="waitbox"><b>기록 요약:</b> 아직 요약할 기록이 없습니다.</div>'; return; }
  const s = summarize(rows.map(r => ({ date: r.record_date, v: r.normalized_value })));
  const one = rows.length === 1 ? '<li>기록 1건</li>' : '';
  box.innerHTML = `<div class="chg"><div class="big">기록 요약 · 최고 ${fmt2(s.hi.v)} · 최저 ${fmt2(s.lo.v)} · 평균 ${fmt2(s.avg)}</div>
    <div class="fm">단위: KRW per 1 USD · 기록 ${rows.length}건 기준 · 소수 둘째 자리 반올림<br>최고 ${fmt2(s.hi.v)} (${esc(s.hi.date)}) / 최저 ${fmt2(s.lo.v)} (${esc(s.lo.date)}) / 평균 ${fmt2(s.avg)}</div></div>
    <div class="rchk"><ul>${one}</ul></div>`;
}

async function buildReal() {
  try { const d = await loadDaily(); deltaLine(d.rows); paintReal(d); paintSummary(d); }
  catch (e) { $('real').innerHTML = `<div class="err-box"><b>실제 일별 기록을 읽지 못했습니다.</b> (${esc(e.message)})</div>`; $('dline').textContent = ''; $('summary').innerHTML = ''; }
}
```

**네가 할 일**
- 인수인계 문서의 "5. 남은 문제"를 "6. 다음 행동" 순서대로 끝낸다. 목표는 검사 10개 모두 PASS.
- 검사(t05-check.html)와 검사 정의, 기대값은 절대 바꾸거나 완화하지 않는다. 코드만 고친다.
- 너는 코드를 실행할 수 없을 수 있다. 그러면 검사 10개를 하나씩 머릿속으로 따라가서, 각 검사의 입력에서 네 코드가 어떤 글을 화면에 만드는지 적고 PASS/FAIL을 판단해라. 실제 실행은 내가 한다.
- 앞 대화가 없어서 막히는 부분이 있으면, 무엇이 문서에 빠졌는지(누락)를 먼저 말해줘.
- 사용 상한: 60분, 내 요청 20회 이내.

**답변 형식**
1. 인수인계 문서에서 빠졌거나 헷갈린 점 (없으면 "없음")
2. 고친 `paintSummary` 함수 전체를 코드블록 하나로 (필요하면 `summarize`/`fmt2`도 바뀐 경우만)
3. 검사 10개 각각의 예상 결과(PASS/FAIL)와 이유 한 줄씩