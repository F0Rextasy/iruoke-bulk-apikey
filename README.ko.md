<div align="center">

```
██╗██████╗ ██╗   ██╗ ██████╗ ██╗  ██╗███████╗
██║██╔══██╗██║   ██║██╔═══██╗██║ ██╔╝██╔════╝
██║██████╔╝██║   ██║██║   ██║█████╔╝ █████╗
██║██╔══██╗██║   ██║██║   ██║██╔═██╗ ██╔══╝
██║██║  ██║╚██████╔╝╚██████╔╝██║  ██╗███████╗
╚═╝╚═╝  ╚═╝ ╚═════╝  ╚═════╝ ╚═╝  ╚═╝╚══════╝
```

# iruoke-bulk-apikey

**자동 생성된 API 키로 Iruoke 계정을 대량 생성 — OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-8-of-them) · [🧪 API 사용하기](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>


---

## ✨ 왜 만들었나

Iruoke는 형제 패널들과 동일한 **"nofield" 문**을 가진 New-API 패널입니다: `/api/user/register`는 사용자 이름과 비밀번호만 받고 **메일함도, 캡차도 없으며**, 계정의 토큰은 `unlimited_quota: true`로 생성됩니다. 이 패널이 저장소를 만들 가치가 있는 이유는 그것이 내주는 레인입니다: **`longcat-2.5-preview-free`**가 모든 새 계정에서, 평범한 `default` 그룹에서 응답합니다 — 그룹 바인딩이 필요 없습니다. 확장되는 단위는 *키당 하나의 계정*이며, `keys.txt`의 모든 키는 실제 채팅 호출로 입증되었습니다: 운영자의 플릿에서 **58개의 키** (첫 번째 문 웨이브에서 44개, **5.4초/키, 14/14 라이브**로 추가 14개가 발급되어 풀을 임계값 위로 끌어올린 스케일 실행).

## 🎁 무료 티어 — 얼마나 받을 수 있나

**메일함도, 캡차도 없음** — 계정 자체가 무료인 것이고, 토큰은 `unlimited_quota: true`로 생성됩니다.

| 항목 | 값 |
| --- | --- |
| 카드 필요 | **아니오** |
| 캡차 / Turnstile | **없음** |
| 메일함 | **사용 안 함** — register 호출은 `{username, password}`만 전송 |
| 무료 레인 | 그룹 `default`의 `longcat-2.5-preview-free`, 그룹 바인딩 불필요 |
| 토큰 | `unlimited_quota: true`, `expired_time: -1` |
| 확장되는 단위 | **하나의 계정 = 하나의 키** → `--count N`은 "N개의 계정" |

> 💡 무료 티어는 Iruoke가 설정하며 변경될 수 있습니다. 실시간 수치는 [api.iruoke.top](https://api.iruoke.top)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하게 하세요:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 모델 — 8개 전부

패널은 **8개의 id에 가격을 매기며 그중 모든 id에 활성화된 그룹이 있습니다** (`GET /api/pricing`). 실제 키로 라이브 **200**을 응답한 id만 나열합니다:

| 모델 ID | ✓ | 그룹 | 비고 |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | 무료 레인 — 가장 저렴한 id이므로 게이트는 모든 계정에서 여기서 멈춤 |

패널이 `default`에서 제공하지만 이 레일이 게이트하지 **않은** id: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. 이들은 게이트의 폴백 순서에 남고 검증된 표에서는 제외됩니다.

## 🧪 API 사용 방법

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 빠른 시작

```bash
# clone or fork this repository, then:
cd iruoke-bulk-apikey

# 1) your proxy list, one per line (http://… or socks5://…)
printf 'http://127.0.0.1:8080\n' > proxies.txt

# 2) go
node src/launcher.js              # interactive
# or directly:
node src/cli.js --count 50 --conc 2
```

`keys.txt`는 라이브 채팅 완성이 키를 입증한 **후에만** 기록됩니다.

| 모드 | 하는 일 |
| --- | --- |
| `run` (기본) | `--count`까지 계정을 발급하고, 모든 키를 라이브 채팅으로 게이트 |
| `--verify-only` | `keys.txt`를 다시 훑고, 실패한 것은 `keys-ungated.txt`로 이동 |
| `--screen` | 프록시 목록을 패널에 대해 테스트 → `proxies.live.txt` |
| `--probe` | 환경 점검: 패널, 가격, 게이트 순서, 종료 |
| `--collect` | `accounts.json`에서 `keys.txt` 재구성 (모든 `chat_status: 200` 행) |

### 🖱️ 런처

런처는 프록시 목록, 개수, 모델을 묻고 `launcher.config.json`에 기억한 뒤 올바른 플래그로 CLI를 실행합니다.

## ✅ 검증됨 — 모든 키가 테스트됨

| 점검 | 결과 |
| --- | --- |
| `GET /api/status` | **200** — 가입 열림, 캡차 없음, 메일 단계 없음 |
| `GET /api/pricing` | **200** — 8개의 가격 책정된 id, 모두 활성화된 그룹 보유; 가장 저렴한 것은 `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — 메일함 필드 없음 (5xx는 재시도, 그 후 이메일 필드로 재시도) |
| `POST /api/user/login` | **200** — `access_token` (쿠키 + 구형 포크를 위한 `New-Api-User` 폴백) |
| `GET /api/user/self` | **200** — 계정 기록을 위한 할당량 읽기 |
| `POST /api/token/` → `POST /api/token/<id>/key` | 토큰 생성됨 (`unlimited_quota: true`), 평문 키 공개 |
| `POST /v1/chat/completions` — 기록되기 전 **모든** 키에 대해 실행 | 실제 콘텐츠와 함께 **200** (`choices`, `error` 없음) |
| 채팅 점검에 실패한 키 | `keys.txt`에 절대 기록되지 않음 |

운영자의 플릿에서 측정 (door rail, 2026-10-19): **14/14 시도 라이브**, 3개 워커로 총 76초 → **5.4초/키**, 모든 키가 `longcat-2.5-preview-free`에서 게이트됨.

## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| 가입 | `POST /api/user/register {username,password}` | 이메일 필드 없음; 5xx는 2.5초 후 재시도, 그 후 주소와 함께 호출 재시도 |
| 로그인 | `POST /api/user/login {username,password}` | `access_token`, 그리고 쿠키만 반환하는 포크를 위한 세션 쿠키 |
| 계정 | `GET /api/user/self` | id + 할당량 |
| 가격 | `GET /api/pricing` | 가장 저렴한 채팅 모델과 그 `enable_groups[0]` |
| 키 | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| 점검 | `POST /v1/chat/completions` | `keys.txt` 이전에 라이브 200 필요; 429는 속도 신호 (2.5초 대기, 같은 모델 재시도) |

## ⚙️ CLI 옵션

```bash
node src/cli.js [mode] [flags]
```

| 플래그 | 기본값 | 의미 |
| --- | --- | --- |
| `--count N` | 10 | 발급할 계정 (= 키) 수 |
| `--conc N` | 2 | 동시 진행 계정 (최대 16) — 워커당 하나의 출구가 고정됨 |
| `--host <name>` | `api.iruoke.top` | 패널 호스트 (같은 레일이 이 패널의 미러에서도 실행됨) |
| `--model <id>` | 가장 저렴한 가격 | 게이트를 하나의 모델 id로 고정 |
| `--group <name>` | 모델의 그룹 | 토큰 그룹 고정 |
| `--pool <file>` | `proxies.txt` | 프록시 목록 (`IRUOKE_PROXIES` 환경 변수도 작동) |
| `--gap MS` | 800 | 한 계정 내 모델 사이의 일시 정지 |
| `--tries N` | 2 | 계정당 시도할 출구 수 |

시작 프로브는 출구가 패널에 응답할 때까지 풀의 앞부분을 순회하므로, 첫 줄이 죽어 있어도 실행이 멈추지 않습니다.

## 🛰️ 프록시 뒤에서

모든 요청은 당신의 풀에서 나온 출구를 통해 나갑니다 — 도구는 패널에 직접 연결하지 않으며, **하나의 계정은 전체 수명 동안 하나의 출구에 삽니다** (패널이 세션을 IP에 바인딩합니다). 측정: 운영자의 풀에서 **9개의 출구**가 스케일 실행 중 이 호스트에 도달할 수 있었고, 발급은 단 한 번의 503도 없이 conc 2-3으로 실행되었습니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | chat-200 키, 한 줄에 하나 — git-ignored |
| `keys-ungated.txt` | 마지막 `--verify-only` 훑기에서 실패한 키 — 나중에 재시도 |
| `accounts.json` | 계정별 사용자 이름 / 할당량 / 그룹 / 모델 / 채팅 상태 — git-ignored |
| `proxies.txt` | 당신의 출구 목록 — git-ignored |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅, OpenAI 호환 라우터입니다. 명령 하나로 `keys.txt`를 작동하는 콤보가 있는 프로바이더 노드로 바꿉니다:

```bash
node src/connect-9router.js
```

기본값: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 프로바이더 | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 프로바이더가 주는 것 | 키당 하나의 계정 — 메일함 없는 가입, 무료 longcat 레인, 모든 키가 채팅 검증됨 |
| 🔑 키 하나로 얻는 것 | 자체 계정에 범위가 지정된 하나의 API 키: 아래 엔드포인트의 8개 모델 id를 당신의 플랜 한도 내에서. 할당량은 **계정**에 있으므로, 이 도구가 키당 하나의 계정을 만드는 것입니다 |
| 🌐 API 형태 | OpenAI 호환: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 당신의 데이터 | api.iruoke.top과 당신이 설정한 inbox 백엔드만 접촉됩니다; 키는 당신의 머신에 남습니다 |

## 🐞 이슈

패널이 문을 닫거나, 무료 레인을 없애거나, 가격 맵을 변경하면 도구는 그 단계에서 멈추고 그렇게 알립니다 (`--probe`가 보이는 것을 보여줍니다). 실행한 명령과 정확한 출력을 담아 **[이슈를 열어주세요](../../issues)**; PR을 환영합니다.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>