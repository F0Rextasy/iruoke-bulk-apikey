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

**Bulk-create Iruoke accounts with auto-generated API keys — OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-8-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

Iruoke is a New-API panel with the same **"nofield" door** as its siblings: `/api/user/register` takes a username and a password and **no mailbox, no captcha**, and the account's token is created with `unlimited_quota: true`. What makes this panel worth a repo is the lane it gives away: **`longcat-2.5-preview-free`** answers on every fresh account, in the plain `default` group — no group binding needed. The unit that scales is *one account per key*, and every key in `keys.txt` was proven with a live chat call: **keys verified** on the operator's fleet (44 from the first door wave, 14 more minted at **5.4 s/key, verified** in the scale run that pushed the pool over the threshold).

## 🎁 Free tier — how much you get

**No mailbox, no captcha** — the account is the free thing, and the token is created with `unlimited_quota: true`.

| What | Value |
| --- | --- |
| Card required | **No** |
| Captcha / Turnstile | **none** |
| Mailbox | **not used** — the register call sends `{username, password}` only |
| Free lane | `longcat-2.5-preview-free` in group `default`, no group binding required |
| Token | `unlimited_quota: true`, `expired_time: -1` |
| The unit that scales | **one account = one key** → `--count N` is "N accounts" |

> 💡 The free tier is set by Iruoke and can change. Check the live numbers at [api.iruoke.top](https://api.iruoke.top) — and let the tool's own gate decide:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Models — all 8 of them

The panel prices **8 ids and every one of them has an enabled group** (`GET /api/pricing`). Only ids that answered a live **200** with a real key are listed:

| Model ID | ✓ | Group | Notes |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | the free lane — the cheapest id, so the gate stops here on every account |

Ids the panel offers in `default` and this rail did **not** gate: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. They stay in the gate's fallback order and out of the verified table.

## 🧪 How to use the API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Quick start

```bash
# clone or fork this repository, then:
cd iruoke-bulk-apikey

# 1) your proxy list, one per line (http://… or socks5://…) — optional, see below
printf 'http://127.0.0.1:8080\n' > proxies.txt   # optional — without it the tool runs direct

# 2) go
node src/launcher.js              # interactive
# or directly:
node src/cli.js --count 50 --conc 2
```

`keys.txt` is written **only** after a live chat completion proves the key.

| Mode | What it does |
| --- | --- |
| `run` (default) | mint accounts until `--count`, gate every key with a live chat |
| `--verify-only` | re-sweep `keys.txt`, moving anything that fails to `keys-ungated.txt` |
| `--screen` | test your proxy list against the panel → `proxies.live.txt` |
| `--probe` | environment check: panel, pricing, gate order, exits |
| `--collect` | rebuild `keys.txt` from `accounts.json` (every `chat_status: 200` row) |

### 🖱️ The launcher

The launcher asks for the proxy list, count and model, remembers them in `launcher.config.json`, and runs the CLI with the right flags.

## ✅ Verified working — every key is tested

> **Quality gates.** Every key here passed a live `chat-200` before it was written to `keys.txt`
> (dead keys are never shipped). Router combos follow the same rule at the model level: an entry is
> added **only if the model answers `chat-200` and can call tools** — a model without tool support,
> a dead key or a dead provider is dropped instead of added. Works with **9Router and OmniRoute**.

| Check | Result |
| --- | --- |
| `GET /api/status` | **200** — register open, no captcha, no mail step |
| `GET /api/pricing` | **200** — 8 priced ids, all with an enabled group; the cheapest is `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — no mailbox field (5xx retried, then retried with an e-mail field) |
| `POST /api/user/login` | **200** — `access_token` (cookie + `New-Api-User` fallback for older forks) |
| `GET /api/user/self` | **200** — quota read for the account record |
| `POST /api/token/` → `POST /api/token/<id>/key` | token created (`unlimited_quota: true`), plaintext key revealed |
| `POST /v1/chat/completions` — run for **every** key before it is written | **200** with real content (`choices`, no `error`) |
| a key that fails its chat check | never written to `keys.txt` |

Measured on the operator's fleet (door rail, 2026-10-19): **14/14 attempts live**, 76 s total at 3 workers → **5.4 s/key**, every key gated on `longcat-2.5-preview-free`.

## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| signup | `POST /api/user/register {username,password}` | no e-mail field; a 5xx is retried after 2.5 s, then the call is retried with an address |
| login | `POST /api/user/login {username,password}` | `access_token`, plus the session cookie for forks that only return a cookie |
| account | `GET /api/user/self` | id + quota |
| pricing | `GET /api/pricing` | cheapest chat model and its `enable_groups[0]` |
| key | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| check | `POST /v1/chat/completions` | live 200 required before `keys.txt`; a 429 is a pace signal (wait 2.5 s, retry the same model) |

## ⚙️ CLI options

```bash
node src/cli.js [mode] [flags]
```

| Flag | Default | Meaning |
| --- | --- | --- |
| `--count N` | 10 | accounts (= keys) to mint |
| `--conc N` | 2 | accounts in flight (max 16) — one exit is pinned per worker |
| `--host <name>` | `api.iruoke.top` | panel host (the same rail runs on mirrors of this panel) |
| `--model <id>` | cheapest priced | pin the gate to one model id |
| `--group <name>` | the model's group | pin the token group |
| `--pool <file>` | `proxies.txt` | optional proxy list (`IRUOKE_PROXIES` env works too) |
| `--gap MS` | 800 | pause between models inside one account |
| `--tries N` | 2 | exits tried per account |

The startup probe walks the front of the pool until an exit answers the panel, so a dead first line does not stop the run.

## 🛰️ Behind a proxy

**Optional — recommended at scale (per-IP limits, bans).** The tool works without a proxy too.

- **With a pool:** pass `--pool <file>` (or set `IRUOKE_PROXIES`) and every request goes through an exit from that list. One account stays on one exit for its whole life (the panel binds the session to the IP).
- **Without a pool:** the CLI prints `running WITHOUT a proxy (direct)` and connects directly — sign-up, key creation and the chat gate all run the same way.
- `--screen` tests the exits you have against the panel, or the direct route when you have none.

Measured on the operator's fleet: **9 exits** in the pool could reach this host during the scale run, and the mint ran at conc 2-3 without a single 503.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | chat-200 keys, one per line — git-ignored |
| `keys-ungated.txt` | keys that failed the last `--verify-only` sweep — retry later |
| `accounts.json` | username / quota / group / model / chat status per account — git-ignored |
| `proxies.txt` | your exit list — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router. One command turns `keys.txt` into a provider-node with working combos:

```bash
node src/connect-9router.js
```

Defaults: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔌 Connect to OmniRoute

[OmniRoute](https://omniroute.online) is a self-hosted, OpenAI-compatible router. One command turns `keys.txt` into a provider-node with working combos:

```bash
node src/connect-omniroute.js
```

Defaults: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.
## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 What the provider gives | one account per key — mailbox-free sign-up, free longcat lane, every key chat-verified |
| 🔑 What one key gets you | one API key scoped to its own account: the 8 model ids on the endpoint below, at your plan's limits. The quota lives on the **account**, which is exactly why this tool creates one account per key |
| 🌐 API shape | OpenAI-compatible: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Your data | only api.iruoke.top and the inbox backend you configure are contacted; keys stay on your machine |

## 🐞 Issues

If the panel closes its door, drops the free lane or changes the pricing map, the tool stops at that step and says so (`--probe` shows what it sees). **[Open an issue](../../issues)** with the command you ran and the exact output; PRs are welcome.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>
