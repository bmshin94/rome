# Rome 프로젝트 분석 및 활용 가이드 (한국어)

> Rome 저장소를 직접 열어보고 정리한 문서입니다.
> 프로젝트가 무엇인지, 어떻게 설치하고 쓰는지, 어떤 사업 기회가 있는지,
> 그리고 PHP 연동이 가능한지까지 한 번에 다룹니다.

## 관련 링크

| 구분 | 주소 |
| --- | --- |
| 원본 프로젝트 (upstream) | https://github.com/rome-os/rome |
| 이 저장소 (fork) | https://github.com/bmshin94/rome |
| 공식 웹사이트 | https://romeos.cc/ |
| Rome Cloud 로그인 | https://romeos.cc/login |
| 공식 문서 | https://romeos.cc/docs/rome |
| 앱 만들기 문서 | https://romeos.cc/docs/building-apps |
| 앱 스토어 | https://romeos.cc/store |
| 소개 영상 | https://www.youtube.com/watch?v=lyNGYEw4a6Y |
| Discord | https://discord.gg/g7EFmEtmqc |
| X (트위터) | https://x.com/RomeAILab |

---

## 1. Rome이 뭔가요

한 줄 요약: **AI 에이전트가 살면서 스스로 성장하는 운영체제(OS)**

공식 슬로건은 "The agentic OS for humans and agents"입니다. MIT 라이선스 오픈소스이고,
파일 2,899개 / TypeScript 1,835개 규모의 pnpm 모노레포입니다.

### 왜 만들어졌나 (VISION.md 요약)

요즘 AI는 대부분 "모델을 더 크게" 키우는 방향으로 갑니다. Rome은 반대 축을 팝니다.

> 모델은 지능을 키운다 → **Rome은 지능이 쓸 "환경"을 키운다**

일반 챗봇은 대화가 끝나면 결과가 증발합니다. 잘 만들어준 워크플로우도 스크롤 위로 사라집니다.
Rome은 이것을 **실행 가능한 코드 자산**으로 남깁니다. 그래서 쓸수록 쌓이고, 다음 요청은 더
어려운 것을 시킬 수 있게 됩니다.

```text
필요한 걸 말한다 → Rome이 능력(앱/액션)을 만든다 → 그게 계속 살아있다
      ↑                                                    ↓
      └─────────── 다시 쓰고, 조합하고, 공유 ──────────────┘
```

### 쉬운 비유

| | 일반 챗봇 | Rome |
| --- | --- | --- |
| 비유 | 지나가는 알바생 | 내 회사 정직원 |
| 일 시키면 | 대답하고 사라짐 | 도구를 만들어서 책상에 놔둠 |
| 다음에 또 시키면 | 처음부터 다시 | 만들어둔 도구로 바로 처리 |

예를 들어 "게임 가격 3만원 밑으로 떨어지면 알려줘"라고 하면, 일반 챗봇은 계속 지켜볼 수
없다고 답합니다. Rome은 가격 추적기를 만들어 계속 돌려놓고, 조건이 되면 알려줍니다.
그리고 그 추적기가 사라지지 않아서, 다음에 "신발 가격도 봐줘" 하면 재활용합니다.

---

## 2. 폴더 구조

집으로 비유하면 이렇습니다.

| 경로 | 역할 |
| --- | --- |
| `packages/core/` | 엔진실. 에이전트 런타임, 세션, 액션, 이벤트, 채널, 메모리, DB |
| `packages/web/` | 웹 대시보드 (localhost:7663 화면) |
| `packages/desktop/` | Electron 데스크톱 앱 |
| `packages/mobile/` | 모바일 |
| `packages/app-runtime-sdk/` | Rome 앱 백엔드 공개 SDK |
| `packages/app-web-sdk/` | Rome 앱 웹 UI 공개 SDK + 빌드 CLI |
| `rome_apps/` | 기본 탑재 앱 15개 |
| `opencli-plugins/` | 브라우저 자동화 플러그인 (google, twitter, linkedin, yelp, opentable, craigslist, chase, chatgpt) |
| `docs/` | 개념 사전, 아키텍처, ADR, 디자인 시스템 |
| `example_apps/` | 앱 제작 예제 (morning-brief) |
| `infra/`, `scripts/`, `Dockerfile` | 배포·개발 인프라 (Docker, Traefik, 옵저버빌리티) |

### 기본 탑재 앱 (`rome_apps/`)

| 앱 | 하는 일 |
| --- | --- |
| `inbox` | 들어온 메시지 분류·라우팅 (텔레그램/디스코드/왓츠앱 연동) |
| `briefing` | 캘린더 + 메시지를 묶은 아침/저녁 브리핑 |
| `coding` | 기획·코딩 에이전트 + UI 목업 갤러리 |
| `browser-automation` | 실제 브라우저를 조종해 사이트 방문·조작 |
| `connector` | Gmail, Slack, Notion, GitHub 등 20개 서비스 연결 |
| `workflow-studio` | 말로 설명한 자동화를 실행 가능한 워크플로우로 생성 |
| `dream` | 주기적으로 최근 대화를 되돌아보고 스스로 메모리를 갱신 |
| `skills` | 설치된 스킬 목록 조회, 새 스킬 임포트/생성 |
| `assistant` | 기본 비서 + 루틴 생성 스킬 |
| `system` | 스케줄링, 메시지 전송, 승인, 앱스토어 접근 |
| `recap` | 완료된 대화의 음성 요약 |
| `replay` | 저장된 실행 기록(trace.json) 재생 |
| `showcases` | 에이전트 실행 기록 공유 |
| `welcome-to-rome` | 첫 실행 온보딩 |

### 핵심 개념 3가지

1. **Action (액션)** — 이름 붙은 실행 단위. 에이전트도, 앱도, 스케줄러도 같은 것을 호출합니다.
   스크립트와 다른 점은 승인 대기가 가능하고 실행 기록이 남는다는 것입니다.
2. **Skill (스킬)** — 자연어로 쓴 절차서. 에이전트가 필요할 때 읽어서 배웁니다. (마크다운 문서)
3. **Rome App (앱)** — UI + 에이전트 + 액션 + DB를 한 덩어리로 묶은 설치 가능한 제품.

> 저장소에 있는 좋은 기준: **"워크플로우는 동사, 앱은 명사"**
> 한 번 하고 끝이면 워크플로우, 계속 살아있어야 하면 앱.

### 경쟁 제품과의 차이

| | 무엇이 쌓이나 | 반복 작업을 뭐가 실행하나 | 호스팅 |
| --- | --- | --- | --- |
| **Rome** | git으로 추적되는 액션·스킬·앱 + 메모리 | 저장된 액션 (모델 재실행 불필요) | 셀프호스팅 또는 클라우드 |
| Grok Bot (xAI) | 호스팅된 봇 상태 | 매번 모델 | 호스팅 전용 |
| Hermes (Nous) | 텍스트 메모, 스킬 문서 | 모델 또는 스크립트 크론 | 셀프호스팅 |
| Manus | 클라우드 머신과 파일 | 모델 또는 파킹된 스크립트 | 호스팅 |

핵심 차이는 **복리로 쌓이는 단위**입니다. Rome은 실행 가능하고 조합 가능한 능력이 쌓이며,
git 추적·내보내기 가능해서 모델을 갈아타도 자산이 유지됩니다.

---

## 3. 설치 방법

설치 경로는 세 가지입니다.

| 방법 | 난이도 | 추천 대상 |
| --- | --- | --- |
| ① Rome Cloud | 매우 쉬움 | 설치 없이 바로 써보고 싶은 경우 |
| ② Docker 한 줄 | 쉬움 | **대부분 여기.** 내 컴퓨터에 설치 |
| ③ 소스 코드 | 어려움 | Rome 자체를 수정하려는 경우 |

### ① Rome Cloud

https://romeos.cc/login 접속. 현재 프리뷰(초대제) 단계입니다.

### ② Docker로 설치 (추천)

#### STEP 1. Docker 설치

| OS | 방법 |
| --- | --- |
| Mac | [OrbStack](https://orbstack.dev) (가벼움) 또는 Docker Desktop |
| Windows | Docker Desktop (WSL2 백엔드 + WSL 터미널에서 실행) |
| Linux | `curl -fsSL https://get.docker.com \| sh` |

확인:

```bash
docker info
```

> Rome은 Windows 네이티브를 지원하지 않습니다. WSL 또는 개발 컨테이너를 사용하세요.

#### STEP 2. (선택) API 키

```bash
export ANTHROPIC_API_KEY="sk-ant-..."
```

키가 없으면 첫 실행 화면에서 **Claude 계정 로그인** 옵션이 제공됩니다.

#### STEP 3. 설치 실행

```bash
# 저장소 없이 바로
curl -fsSL https://raw.githubusercontent.com/rome-os/rome/main/scripts/quickstart-docker.sh | bash

# 저장소를 이미 받았다면
cd rome
./scripts/quickstart-docker.sh
```

스크립트가 하는 일:

1. Docker 설치·구동 확인
2. 로그인 비밀키 자동 생성 (`~/.rome/quickstart/jwt-secret`)
3. `yunfanye/rome:latest` 이미지 다운로드
4. 컨테이너 시작
5. 로그에 `Rome started`가 뜰 때까지 대기 (최대 600초)

> 첫 실행은 몇 분 걸립니다. 점(`.....`)이 찍히는 것은 정상입니다.

성공 시 출력:

```text
Rome is running.
  Dashboard : http://localhost:7663
  Logs      : docker logs -f rome
  Stop      : docker stop rome
```

#### STEP 4. 접속

http://localhost:7663

> 포트 7663은 전화 키패드로 R-O-M-E 입니다.

#### 설치 옵션

```bash
./scripts/quickstart-docker.sh --port 9000 --name my-rome
```

| 옵션 | 설명 | 기본값 |
| --- | --- | --- |
| `--port` | 대시보드 호스트 포트 | `7663` |
| `--name` | 컨테이너 이름 (볼륨 접두사도 됨) | `rome` |
| `--bind` | 포트를 바인딩할 주소 | `127.0.0.1` |
| `--image` | 실행할 이미지 | `yunfanye/rome:latest` |
| `--env-file` | 추가 env 파일 | — |
| `--no-pull` | 이미지 다운로드 건너뛰기 | — |
| `--help` | 전체 도움말 | — |

#### 보안 주의: `--bind`

기본값 `127.0.0.1`은 내 컴퓨터에서만 접속 가능합니다. 이게 안전합니다.

`0.0.0.0` 등으로 열면 **첫 접속자가 주인(Guardian) 자리를 차지**합니다.
첫 가입 화면(`/api/onboard/create-account`)은 인증이 없기 때문입니다.

> 권장 순서: 기본값으로 설치 → 내가 먼저 가입 완료 → 필요하면 그 다음에 개방

---

## 4. 첫 실행과 사용법

### 첫 실행 흐름

1. **계정 만들기** — Rome의 주인을 **Guardian(가디언)** 이라 부릅니다. 인스턴스당 한 명입니다.
2. **Welcome 온보딩** — 가입하면 환영 화면이 뜨고, `Start chat`을 누르면 대화가 시작됩니다.

   ```text
   인사 → 이메일 연결 → 자기소개 → 첫 앱 아이디어 브레인스토밍 → 완료
   ```

   > 이 온보딩 대화는 모델이 아니라 상태 머신(각본)으로 돌아갑니다. 토큰을 쓰지 않고
   > 엉뚱한 답을 하지 않습니다. 단, "자기소개 기억하기"와 "앱 아이디어" 단계는 실제 에이전트입니다.

3. **사용 시작** — 왼쪽 메뉴에 Chat, Briefing, Inbox, Connector, Workflows, Skills,
   App Store, Settings가 있습니다.

### 어떻게 일을 시키나

**한 번만 시키는 일**

- "이 사이트 내용 요약해줘"
- "이 코드 버그 좀 찾아줘"

**계속 지켜봐야 하는 일 (Rome의 강점)**

- "이 게임 가격 3만원 밑으로 떨어지면 알려줘"
- "매주 금요일 9시에 주간 회고 리마인드 해줘"
- "집주인한테 메일 오면 바로 문자 보내줘"

**반복 업무**

- "메일함 정리해줘. 광고는 보관하고, 급한 건 표시하고, 답장 필요한 건 초안 작성"

**개발**

- "이 PR의 P1, P2 리뷰 코멘트를 머지 블로커가 없어질 때까지 고쳐줘"

**앱 만들기**

- "우리 팀 휴가 관리 앱 하나 만들어줘"
  → 기획서 작성 → 승인 → 실제 앱 생성 후 메뉴에 추가

### 연결 가능한 외부 서비스

Gmail, Outlook, Google Calendar, GitHub, Slack, Notion, Google Drive/Sheets/Docs/Slides,
Dropbox, Linear, Discord, LinkedIn, Supabase, Neon, HubSpot, Stripe, Meta Ads, Google Ads

### 관리 명령어

```bash
docker ps                      # 상태 확인
docker logs -f rome            # 실시간 로그 (에러 확인)
docker stop rome               # 정지
docker start rome              # 재시작
./scripts/quickstart-docker.sh # 업데이트 (데이터 유지)

# 완전 삭제 (데이터도 삭제되니 주의)
docker rm -f rome
docker volume rm rome-app rome-user-home rome-rome-home rome-ssh-host-keys rome-tailscale
```

> 대화·메모리·생성한 앱은 Docker 볼륨에 저장됩니다. 컨테이너를 지우고 다시 만들어도(=업데이트)
> 데이터는 유지됩니다.

### ③ 소스 코드로 개발

필요 조건: Node.js 24+, pnpm 11.6, Docker(Compose 포함)

```bash
corepack enable
pnpm install
pnpm dev:all      # Rome 컨테이너 + Traefik + 옵저버빌리티 + 웹 개발 서버
```

개발 명령어:

```bash
./r pnpm test     # 컨테이너 안에서 명령 실행
./r bash          # 컨테이너 셸 진입

pnpm typecheck
pnpm lint
pnpm test:unit
pnpm build
```

모니터링 대시보드: http://obs.rome.localhost:3000

> `pnpm start`(호스트 직접 실행)와 `pnpm dev:all`(컨테이너)은 데이터 저장 위치가 다릅니다.
> 섞어 쓰면 상태가 두 벌로 갈립니다. `pnpm dev:all`이 정석입니다.

### 문제 해결

| 증상 | 해결 |
| --- | --- |
| `Docker is not installed` | Docker 설치 후 실행까지 확인 (`docker info`) |
| `daemon is not reachable` | Docker Desktop/OrbStack 실행. 리눅스는 `systemctl start docker` |
| 점만 계속 찍힘 | 정상. 10분 넘으면 `docker logs -f rome` 확인 |
| 컨테이너가 바로 종료 | `docker logs --tail 50 rome` 로 에러 확인 |
| 포트 충돌 | `--port 9000` 등 다른 포트 사용 |
| AI가 응답하지 않음 | API 키 또는 Claude 로그인 확인 (Settings) |
| 앱스토어가 안 보임 | `PANTHEON_BASE_ORIGIN` (기본 `https://romeos.cc`) 확인 |

---

## 5. 수익화 아이디어

### 저장소에서 확인한 사실

**Favor(페이버) 시스템이 이미 구현되어 있습니다.**
`packages/core/src/favors/` 와 `packages/api-types/src/favors.ts`:

```ts
FavorBalanceView      { available, lifetimeEarned, lifetimeSpent }  // 잔액 + 누적 수익
FavorRechargePackView { favors, displayPrice }                      // 충전 패키지
FavorRechargeCheckout { url }                                       // 결제 페이지
FavorLedgerKind: "recharge" | "action_request_debit" | ...          // 거래 장부
```

Favor는 **방문자가 다른 사람의 Rome 인스턴스에서 액션을 실행할 때 쓰는 화폐**입니다.
즉, **내 에이전트를 남에게 서비스로 파는 구조가 이미 존재**합니다.

```text
남이 충전 → 내 Rome에 일을 시킴 → 나에게 수익 누적(lifetimeEarned)
```

**라이선스는 MIT** 이므로 상업적 이용, 수정 후 판매, 사내 사용 모두 가능합니다.

**단, 솔직한 한계:** 앱스토어(`docs/concepts/apps.md`)에는 리스팅/버전/핸들/리믹스만 있고
**가격 필드가 없습니다.** 현재는 무료 배포 전용이며 Rome Cloud도 프리뷰입니다.
즉 초기 생태계 = 리스크도 있지만 선점 기회이기도 합니다.

### 티어 1 — 지금 당장 가능

1. **Rome 설치·구축 대행** (가장 빠른 현금화)
   중소기업·자영업자 대상 서버 설치 + 커넥터 연결 + 루틴 세팅 + 전용 앱 제작.
   초기 구축비 + 월 운영비 모델. MIT 라이선스라 합법적으로 가능합니다.

2. **버티컬 팩** (특정 직군용 세트)
   Skill은 마크다운 문서라 제작이 쉽습니다.

   | 팩 | 내용 |
   | --- | --- |
   | 병원 | 예약 확인, 노쇼 리마인드, 리뷰 응대 |
   | 부동산 | 매물 추적, 시세 리포트, 문의 자동응답 |
   | 쇼핑몰 | CS 자동응답, 재고 알림, 경쟁사 가격 추적 |
   | 마케팅 | 광고 성과 리포트, 콘텐츠 캘린더 |

   앱스토어에 무료 배포로 인지도 확보 → 설치·커스터마이징 유료화.

3. **콘텐츠 선점**
   한국어 자료가 거의 없습니다. 유튜브/블로그/뉴스레터 → 광고 + 강의 + 컨설팅 유입.

### 티어 2 — 준비 필요

4. **Favor로 AI 서비스 판매**
   이력서 첨삭 봇, 분야별 리서치, 디자인 피드백, 상권 분석 등.
   Rome Cloud가 프리뷰라 지금은 준비 단계입니다.

5. **모니터링 리포트 B2B 구독**
   브라우저 자동화로 경쟁사 가격·신제품, 채용/입찰 공고, 키워드 여론 추적.
   고객은 Rome을 몰라도 됩니다. 결과만 받으면 됩니다.

6. **opencli 플러그인 개발**
   현재 8개뿐이고 **한국 사이트는 하나도 없습니다.**
   네이버, 쿠팡, 당근, 배민, 잡코리아 등을 만들면 국내 선점이 가능합니다.

### 티어 3 — 자본 필요

7. **매니지드 호스팅** (국내판 Rome Cloud) — 서버비 + API 비용 + 보안 책임이 큽니다.
8. **사내 자동화로 비용 절감** — 안 쓰는 돈이 곧 번 돈입니다. ROI가 가장 확실합니다.

### 리스크

| 리스크 | 설명 |
| --- | --- |
| 초기 단계 | Rome Cloud 프리뷰, 앱 유료 판매 미지원 |
| AI API 비용 | 사용량에 비례해 원가 증가 → 단가 계산 필수 |
| 보안 책임 | 고객의 Gmail·Slack을 다루므로 사고 시 책임 발생 |
| 생태계 리스크 | Rome이 성장하지 못하면 투자 시간 손실 |

### 추천 순서

```text
1단계: 내 업무부터 자동화 (실력 축적)
   ↓
2단계: 그 과정을 콘텐츠로 (블로그/유튜브)
   ↓
3단계: 문의가 오면 구축 대행 (첫 수익)
   ↓
4단계: 반복 패턴을 버티컬 팩으로 상품화
   ↓
5단계: Favor가 열리면 서비스 런칭
```

---

## 6. PHP로 만들 수 있나

결론: **반은 되고 반은 안 됩니다.**

### ① Rome 앱을 PHP로 만들기 → 불가능

```yaml
# action.yaml
entry: ./index.ts        # .ts 파일을 직접 지정
```

```ts
// index.ts — Rome이 이 함수를 직접 불러서 실행
import { defineAction, z } from "@rome-os/app-runtime";
export function createAction(config, deps) { ... }
```

Rome은 앱을 별도 프로세스로 실행하지 않고 **Node.js 프로세스 안으로 로드**합니다.
그래서 PHP 파일은 읽을 수 없습니다. 컨테이너에 **PHP가 설치되어 있지도 않습니다**
(Dockerfile 확인 결과).

| 부품 | 언어 | PHP 가능 |
| --- | --- | --- |
| 액션 (Action) | TypeScript | 불가 |
| 훅 (Hook) | TypeScript | 불가 |
| 웹 화면 | React 19 | 불가 |
| DB | Drizzle (SQLite) | 불가 |
| **스킬 (Skill)** | **마크다운** | **가능 (언어 무관)** |

### ② Rome이 내 PHP 코드를 쓰게 하기 → 가능 (추천)

Rome이 PHP를 직접 실행하지 못할 뿐, 불러다 쓰는 것은 가능합니다.
실제로 `browser-automation` 앱이 이 방식을 씁니다.

```ts
import { execFile } from "node:child_process";   // 외부 프로그램 실행
```

#### 방법 A: HTTP 호출 (가장 추천)

```text
Rome (TS 액션 20줄)  ──HTTP──▶  내 PHP 서버 (Laravel 등)
                     ◀──JSON──
```

```ts
export default defineAction({
  name: "my_php_thing",
  description: "내 PHP 서비스 호출",
  schema: z.object({ query: z.string() }),
  async run({ query }) {
    const res = await fetch("https://내서버.com/api/work", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ query }),
    });
    return await res.json();
  },
});
```

장점: 기존 PHP 프로젝트 재활용, 서버 분리로 장애 격리, 배포 독립.

#### 방법 B: 컨테이너에 PHP 설치 후 직접 실행

```ts
await execFile("php", ["/app/my-script.php", input]);
```

Dockerfile에 `apt-get install php-cli` 추가가 필요합니다. 방법 A가 더 깔끔합니다.

#### 방법 C: PHP → Rome 호출

Rome은 웹훅과 HTTP API를 제공하므로, PHP 서비스가 Rome에 작업을 지시할 수도 있습니다.

### ③ Rome 자체를 PHP로 재작성 → 비추천

| 문제 | 이유 |
| --- | --- |
| 규모 | TypeScript 파일 1,835개 |
| 긴 작업 | 에이전트는 수 분~수 시간 실행. PHP의 요청-응답 모델과 상성이 나쁨 |
| 실시간 스트리밍 | SSE 기반 스트리밍은 Node가 유리 |
| 데스크톱 앱 | Electron 기반이라 PHP로 대체 불가 |
| 생태계 | AI SDK는 대체로 Python/JS 우선 지원 |

Swoole이나 ReactPHP로 우회는 가능하지만, 실익 대비 비용이 너무 큽니다.

### PHP 개발자를 위한 전략

```text
기존 PHP 서비스 (이미 보유한 자산)
        ↑ HTTP
   [TS 브릿지 20줄]  ← 이것만 새로 작성
        ↑
   Rome 에이전트  ← AI가 알아서 내 서비스를 호출
```

- 기존 PHP 서비스가 그대로 "AI 연동 서비스"가 됩니다.
- 앱스토어에 무료 배포해 유입을 만들고, 결제는 내 PHP 서비스에서 처리합니다.
- 새 언어 학습 없이 TS 브릿지 코드만 작성하면 됩니다.

> 발상을 뒤집는 것이 핵심입니다.
> "PHP로 Rome을 만든다"가 아니라 **"Rome이 내 PHP를 쓰게 한다"**.

### 정리

| 하고 싶은 것 | 결론 |
| --- | --- |
| 앱 전체를 PHP로 | 불가능 (TypeScript 고정) |
| **내 PHP 로직 연동** | **가능하며 쉬움 (추천)** |
| Rome 자체를 PHP로 | 가능하지만 비추천 |
