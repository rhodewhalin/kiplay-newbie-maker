# 신입사원 메이커 — 인수인계 노트 (HANDOFF)

> **새 Claude 세션 사용법**: 이 파일을 통째로 첨부/붙여넣고 "이 프로젝트 이어서 작업하자"라고 하세요.
> 이 파일 하나로 프로젝트 전체 맥락 + 수정/배포 방법 + 함정 + 재사용 스크립트를 파악할 수 있게 작성했습니다.
> (작성 시점 기준 최신 커밋: `2002522` · main)

---

## 0. 한 줄 요약
KIPI(한국특허정보원) 사내 AI 공모전용 미니게임 **"신입사원 메이커: 20년 후의 나"** — 15번의 선택으로 신입을 육성해 20년 후 모습(강점·성향·능력치·사원증 카드)을 보여주는 **단일 자체완결 정적 HTML 게임**. 나중에 팀원 고나래님의 **"KIPI Playground" 로비**에 미니게임으로 합쳐질 예정.

---

## 1. 핵심 위치 (가장 먼저 확인)
| 항목 | 위치 |
|---|---|
| **게임 본체** | `C:\Users\가공_서혜연\Game\newbie-maker\index.html` (약 4MB, 모든 이미지 base64 임베드) |
| **Git 저장소(비공개)** | `C:\Users\가공_서혜연\Game` (원격: https://github.com/skyhyeyeon-art/kiplay-newbie-maker , `main`) |
| **공유 플레이 링크(Artifact)** | https://claude.ai/code/artifact/644e3054-5820-4804-99a6-a4e2627351ce |
| **로컬 미리보기 사본** | `C:\Users\Public\nm_16char.html` |
| **이 인수인계 파일** | `C:\Users\가공_서혜연\Game\HANDOFF.md` |

- 이미지 원본(캐릭터 16장 등)은 `C:\Users\가공_서혜연\Downloads` 에 `열정만렙형_여자_20.png` 식으로 있음.
- 담당자: **서혜연 대리** (kipi8503@gmail.com) · 팀 **KIPlay** · 팀원 **고나래**(rhodewhalin@gmail.com, GitHub 협업자).

---

## 2. 게임 구조 (index.html 내부)
`<style>` + `<body>`(#app 하나) + `<script>` 로 구성된 단일 파일. 함수는 이름으로 grep 하면 바로 찾음.

### 화면 흐름
`screenStart()` → `screenTurn()` (15문항 반복) → `screenReveal()` (두구두구 카드) → `screenResult()` (20년 후 결과) → `makeCard()` (사원증 카드 캔버스 저장)

### 핵심 데이터/함수 (grep 키워드)
- `STAT_KEYS` — 6능력치: 기획력·소통력·행정력·디지털·조직이해도·창의력
- `CHARS[4]` — 4유형 메타(열정🔥/긍정☀️/차분🧊/자유🎨): tag, line, 성격 등
- `CHAR_IMG` — **이미지 데이터 (한 줄, 약 2.8MB base64)**. 구조: `[{ "남":{y:20대,o:40대}, "여":{y,o} }, ...]` (유형 순서 = CHARS 순서)
- `imgFor(idx, gender, aged)` — 이미지 선택 헬퍼
- `chosenChar` / `chosenGender` — 시작화면 선택값 (**기본 성별 = "여"**)
- `G` — 게임 상태 객체: `stats, like, trait, burnout, turns, t, char, gender, charY(20대 src), charO(40대 src), log, snap(뒤로가기 스냅샷)`
- `scoreOf(s,burnout)` / `gradeOf(sc)` / `GRADE_LABEL` — 점수·등급(닉네임식)
- `decideChar(growth,trait)` / `BASE_CHAR` / `SPECIAL` / `FIT` / `PAIN` — 결과 캐릭터·직무·성향 판정
- `BG_IMG[7]` (0사무실 등) / `BG_EVENT`(돌발) / `RESULT_BG`(결과 방 매핑) / `MASCOT_IMG`
- `Q`(문항 배열) / `buildTurns(seed)` — 6회사현안+7성향+2돌발 = 15턴
- `choose(i)` → `showTurnResult()`(팝업 `.fxpop`) → `nextTurn()`(자동 진행) / `goBack()`(뒤로가기, 스냅샷 복원)
- `makeCard()` — 결과 사원증 카드(canvas 680×920). 캐릭터를 방 배경 위에 종횡비 유지·바닥정렬로 그림.

### 캐릭터/에셋 규칙 (중요)
- **16종** = 4유형 × 남/여 × 20대/40대.
- **시작·문항 화면 = 20대 신입 버전(`G.charY`)**, **결과·저장 카드 = 20년 후 40대 프로 버전(`G.charO`)**.
- 성별은 시작화면 `남/여` 토글(`selectGender`), 기본 여자.
- 결과 방(배경)은 캐릭터가 아니라 **최고 능력치(top stat)** 로 결정(`RESULT_BG`).
- 캐릭터 이미지는 **투명 배경 PNG**여야 함(방 위에 얹힘). 20대 256px, 40대 336px로 리사이즈해 임베드.

---

## 3. 수정 → 배포 워크플로우 (매 변경마다 이 순서로!)
1. **`index.html` 편집** (Edit 도구).
2. **문법 검증**: `<script>` 추출 → `node --check` (아래 §6-A).
3. (선택) **미리보기**: node 정적 서버 띄우고 브라우저로 확인 (§6-C).
4. **로컬 사본 갱신**: `index.html` → `C:\Users\Public\nm_16char.html` 복사.
5. **Git 커밋+푸시** (`main`). 커밋 메시지 끝에 `Co-Authored-By: Claude ...` 붙이는 관례.
6. **Artifact 재배포**: `<style>` + `<body>` innerHTML 추출 → `share_page.html` → **Artifact 도구로 같은 URL에 재배포** (title `신입사원 메이커`, favicon `🧑‍💼`). (§6-B)
7. **사용자 안내**: `Ctrl+Shift+R` 강력 새로고침. **팀원 공유는 claude.ai Share 메뉴에서 껐다 켜 재공유**해야 최신 반영(아래 함정 참고).

---

## 4. 함정 (Gotchas) — 꼭 알아둘 것
- **Artifact 공유 버전은 "고정(pinned)"**: 소유자(본인)는 재배포 즉시 최신이 보이지만, **뷰어(팀원)는 사용자가 Share 메뉴에서 재공유하기 전까지 옛 버전**을 봄. → "팀원이 이상한/옛 버전 본다"의 원인은 거의 이것.
- **Artifact 뷰어는 다운로드 권한 없음**: 페이지 안 "이미지 저장" 버튼은 아티팩트 미리보기에선 안 먹힘(실제 파일/로비에선 됨). 뷰어에겐 우클릭(PC)·꾹 누르기(폰)로 저장 안내.
- **PowerShell 5.1 `-File` 인코딩**: `.ps1`이 UTF-8(BOM 없음)이면 한글이 깨짐 → **UTF-8 with BOM으로 저장** 후 실행. (Write 도구는 BOM 없이 저장하므로, `[IO.File]::WriteAllText(path, txt, (New-Object Text.UTF8Encoding($true)))` 로 다시 저장)
- **PowerShell 배열 리터럴 안 산술**: `@(0,$h-1)` 같은 표현이 파싱 오류 → `$h1=$h-1` 처럼 변수로 빼서 사용.
- **COM 속성 캐스팅**: PowerPoint COM에서 `Font.Size = $x` 는 `[single]$x` 로 캐스팅 필요.
- **CHAR_IMG는 한 줄**: `const CHAR_IMG=[...]` 가 통째로 한 줄(거대). Edit 도구로 부분 수정 말고, **PowerShell regex로 그 줄 전체를 교체**(§6-D).
- **poppler 없음**: PDF→이미지는 WinRT 스크립트(§6-E)로.
- **PowerPoint COM 사용 가능**: pptx 생성/렌더(조직도 등)에 활용 가능. LibreOffice·python-pptx는 없음.
- **로컬 python은 WindowsApps 스텁**이라 잘 안 됨 → 미리보기 서버는 **node** 사용.
- **파일 되읽기 금지 관례**: Edit 후 확인차 다시 Read 하지 말 것(도구가 실패 시 에러 냄).

---

## 5. 최근 작업 이력 (핵심 결정 · 왜 그렇게 했나)
- 배경 7종 + 캐릭터 + 마스코트 + 돌발 컷 통합, 씬(배경+캐릭터) 레이어링.
- 등급 S/A/B/C/D → **친근한 닉네임**(점수는 랭킹용 유지).
- 결과 리포트 재구성(잘하는/끌리는/힘든 + 성향 + 능력치 레이더 + 사원증 카드), 두구두구 공개 카드.
- 응답 후 **능력치 증감 팝업 → 1.7초 후 자동 진행**, 문항 **뒤로가기 버튼**(스냅샷 복원).
- **캐릭터 16종** 도입(4유형×남녀×20/40대), 성별 토글, 20→40대 전환.
- 결과 화면: 사원증 크게 대신 **방+캐릭터(68%)**, 사원증은 **저장 카드**로 이동(공식 증명). 파란 카드 그림자 제거.
- 두구두구 배지 흰 글씨(가독성), 카드 아이콘 🎴(화투 느낌)→✨. 기본 성별 여자, 닉네임 예시 '키피'.
- **열정형 여자 40대 흰 배경 이슈**(길었던 삽질): 원본이 흰 배경 → 자동 제거 시 **머리카락에 갇힌 흰 포켓**이 남고, 무리하게 지우면 **치아·눈·사원증·셔츠(다 흰색)**가 뚫림. → **결론: 흰 배경 이미지는 GPT에서 투명 PNG로 재생성하는 게 정답.** 결국 새 투명 원본으로 교체해 해결. (교훈: 흰 배경 자동 키잉은 최후수단, 투명 원본이 최선)

---

## 6. 재사용 스크립트 (부록) — 새 세션엔 이전 scratchpad가 없으니 필요시 아래로 재생성
> 경로 주의: **scratchpad는 세션마다 다름**. 새 세션의 스크래치 폴더를 쓰거나 임시 경로를 정해서 사용.

### 6-A. 문법 검증
```powershell
$path="C:\Users\가공_서혜연\Game\newbie-maker\index.html"
$txt=[IO.File]::ReadAllText($path,[Text.Encoding]::UTF8)
$js=[regex]::Match($txt,'(?s)<script>(.*)</script>').Groups[1].Value
[IO.File]::WriteAllText("$env:TEMP\check.js",$js,(New-Object Text.UTF8Encoding($false)))
& node --check "$env:TEMP\check.js"; if($?){ "NODE SYNTAX OK" }
```

### 6-B. Artifact 재배포용 조각 만들기
```powershell
$path="C:\Users\가공_서혜연\Game\newbie-maker\index.html"
$html=[IO.File]::ReadAllText($path,[Text.Encoding]::UTF8)
$style=[regex]::Match($html,'(?s)<style>.*?</style>').Value
$body=[regex]::Match($html,'(?s)<body[^>]*>(.*)</body>').Groups[1].Value
[IO.File]::WriteAllText("$env:TEMP\share_page.html",($style+"`n"+$body),(New-Object Text.UTF8Encoding($false)))
```
그 다음 **Artifact 도구**로 `$env:TEMP\share_page.html` 를 URL `https://claude.ai/code/artifact/644e3054-5820-4804-99a6-a4e2627351ce` 에 재배포 (title `신입사원 메이커`, favicon `🧑‍💼`).

### 6-C. node 미리보기 서버 (브라우저 확인용)
`serve.js`:
```javascript
const http=require('http'),fs=require('fs'),path=require('path');
const ROOT="C:\\Users\\가공_서혜연\\Game\\newbie-maker";
const types={'.html':'text/html; charset=utf-8','.js':'text/javascript','.png':'image/png'};
http.createServer((req,res)=>{
  let p=decodeURIComponent(req.url.split('?')[0]); if(p==='/')p='/index.html';
  fs.readFile(path.join(ROOT,p),(e,d)=>{ if(e){res.writeHead(404);res.end('nf');return;}
    res.writeHead(200,{'Content-Type':types[path.extname(p).toLowerCase()]||'application/octet-stream'}); res.end(d); });
}).listen(8777,'127.0.0.1',()=>console.log('listening 8777'));
```
`node serve.js` (백그라운드) 후 브라우저 도구로 `http://127.0.0.1:8777/index.html` 열기. 끝나면 `taskkill //F //IM node.exe`.

### 6-D. 캐릭터 이미지 교체(임베드) — CHAR_IMG 한 줄 전체 재생성
핵심: System.Drawing으로 리사이즈(투명 PNG, 20대 256 / 40대 336px) → base64 → `[Convert]::ToBase64String` → `const CHAR_IMG=[...]` 문자열 재조립 → regex `(?m)^const CHAR_IMG=.*$` 로 그 줄만 교체. (한 장만 바꿀 땐 새 파일 경로만 그 슬롯에 넣고 전체 재생성)
```powershell
# 리사이즈+base64 함수
function ToB64($p,$max){ Add-Type -AssemblyName System.Drawing
  $s=New-Object System.Drawing.Bitmap($p); $w=$s.Width;$h=$s.Height
  if($w -ge $h){$nw=$max;$nh=[int]($h*$max/$w)}else{$nh=$max;$nw=[int]($w*$max/$h)}
  $b=New-Object System.Drawing.Bitmap($nw,$nh,[System.Drawing.Imaging.PixelFormat]::Format32bppArgb)
  $g=[System.Drawing.Graphics]::FromImage($b); $g.InterpolationMode=[System.Drawing.Drawing2D.InterpolationMode]::HighQualityBicubic
  $g.Clear([System.Drawing.Color]::Transparent); $g.DrawImage($s,0,0,$nw,$nh); $g.Dispose()
  $ms=New-Object System.IO.MemoryStream; $b.Save($ms,[System.Drawing.Imaging.ImageFormat]::Png)
  "data:image/png;base64,"+[Convert]::ToBase64String($ms.ToArray()) }
# 배포된 이미지 검증: index.html에서 첫 "여":{...o:...} 추출해 마젠타 배경에 얹어 확인 → 흰 포켓 여부 눈으로 체크
```
- **검증 팁**: 캐릭터 투명 이미지를 **진한 마젠타/회색 배경**에 얹어 렌더해 보면 흰 포켓·헤일로가 드러남. 크림/흰 배경에선 안 보이니 반드시 진한 색으로 검사.

### 6-E. PDF → PNG 렌더 (poppler 없이, WinRT) — ODA 문서 작업 등에서 재사용
`pdf2png_range.ps1` (params: -Path -OutDir -Start -Count -Scale):
```powershell
param([string]$Path,[string]$OutDir,[int]$Start=1,[int]$Count=6,[int]$Scale=2)
[Windows.Data.Pdf.PdfDocument,Windows.Data.Pdf,ContentType=WindowsRuntime]|Out-Null
[Windows.Storage.StorageFile,Windows.Storage,ContentType=WindowsRuntime]|Out-Null
Add-Type -AssemblyName System.Runtime.WindowsRuntime
$g=([System.WindowsRuntimeSystemExtensions].GetMethods()|?{$_.Name -eq 'AsTask' -and $_.GetParameters().Count -eq 1 -and $_.GetParameters()[0].ParameterType.Name -eq 'IAsyncOperation`1'})[0]
$a=([System.WindowsRuntimeSystemExtensions].GetMethods()|?{$_.Name -eq 'AsTask' -and $_.GetParameters().Count -eq 1 -and $_.GetParameters()[0].ParameterType.Name -eq 'IAsyncAction'})[0]
function Await($o,$t){$k=$g.MakeGenericMethod($t).Invoke($null,@($o));$k.Wait(-1)|Out-Null;$k.Result}
function AwaitA($o){$k=$a.Invoke($null,@($o));$k.Wait(-1)|Out-Null}
if(-not(Test-Path $OutDir)){New-Item -ItemType Directory $OutDir -Force|Out-Null}
$f=Await ([Windows.Storage.StorageFile]::GetFileFromPathAsync($Path)) ([Windows.Storage.StorageFile])
$pdf=Await ([Windows.Data.Pdf.PdfDocument]::LoadFromFileAsync($f)) ([Windows.Data.Pdf.PdfDocument])
$end=[Math]::Min($Start+$Count-1,[int]$pdf.PageCount)
for($p=$Start;$p -le $end;$p++){ $pg=$pdf.GetPage($p-1); $out=Join-Path $OutDir ("p{0:D3}.png" -f $p)
  [IO.File]::WriteAllBytes($out,@()); $sf=Await ([Windows.Storage.StorageFile]::GetFileFromPathAsync($out)) ([Windows.Storage.StorageFile])
  $st=Await ($sf.OpenAsync(1)) ([Windows.Storage.Streams.IRandomAccessStream])
  $opt=New-Object Windows.Data.Pdf.PdfPageRenderOptions; $opt.DestinationWidth=[uint32]($pg.Size.Width*$Scale)
  AwaitA ($pg.RenderToStreamAsync($st,$opt)); $st.Dispose(); $pg.Dispose(); $out }
```

---

## 7. 남은/선택 할 일
- 특별히 급한 것 없음(게임은 배포 완료 상태).
- (선택) GitHub PR #1 / 오래된 feat 브랜치 정리.
- (선택) 로비 통합 시: 고나래님 "KIPI Playground"에 이 index.html을 미니게임으로 연결.

## 8. 게임과 별개인 진행 업무 (참고)
- 사용자 본업: **KOICA ODA 사업**(긴 문서·입찰 제안서). Claude로 장문 문서/논리구조 작업 선호.
- 최근: **키르기스 과학·고등교육·혁신부 중앙본부 조직도**(영문 PDF→한글 PPT) 제작 — PowerPoint COM으로 네이티브 도형 생성. (`C:\Users\가공_서혜연\Downloads\키르기스 과학고등교육혁신부 중앙본부 조직도(한글).pptx`) 계층 용어: Department=국 / Division=과 / Unit=계.

---
*이 문서는 프로젝트 상태가 바뀌면 함께 업데이트하세요. 최신 커밋·결정이 여기에 반영돼 있어야 다음 인수인계가 매끄럽습니다.*
