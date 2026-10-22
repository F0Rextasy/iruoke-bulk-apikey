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

**批量创建 Iruoke 账户并自动生成 API 密钥 — 兼容 OpenAI 的推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-8-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题反馈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 为什么会有这个项目

Iruoke 是一个 New-API 面板，和它的同类一样有着同样的 **“nofield” 后门**：`/api/user/register` 只需要用户名和密码，**不需要邮箱，不需要验证码**，并且账户的令牌以 `unlimited_quota: true` 创建。让这个面板值得开一个仓库的原因，是它免费开放的车道：**`longcat-2.5-preview-free`** 在每个新账户上都能响应，而且就在普通的 `default` 分组里 —— 无需绑定分组。可扩展的单元是*每个密钥对应一个账户*，`keys.txt` 中的每个密钥都通过一次实时聊天调用得到验证：在运营者的集群上有 **58 个密钥**（第一波后门产出 44 个，另外在规模测试中以 **5.4 秒/密钥，14/14 全部可用** 的速度又铸造了 14 个，将密钥池推过了阈值）。

## 🎁 免费额度 —— 你能得到多少

**不需要邮箱，不需要验证码** —— 账户本身就是免费的东西，令牌以 `unlimited_quota: true` 创建。

| 项目 | 值 |
| --- | --- |
| 需要银行卡 | **否** |
| 验证码 / Turnstile | **无** |
| 邮箱 | **不使用** —— 注册调用只发送 `{username, password}` |
| 免费车道 | `default` 分组中的 `longcat-2.5-preview-free`，无需绑定分组 |
| 令牌 | `unlimited_quota: true`，`expired_time: -1` |
| 可扩展的单元 | **一个账户 = 一个密钥** → `--count N` 即“N 个账户” |

> 💡 免费额度由 Iruoke 设定，可能会变化。在 [api.iruoke.top](https://api.iruoke.top) 查看实时数据 —— 并让工具自身的关卡来做判断：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 模型 —— 全部 8 个

该面板定价了 **8 个 id，且每一个都有已启用的分组**（`GET /api/pricing`）。这里只列出用真实密钥返回实时 **200** 的 id：

| 模型 ID | ✓ | 分组 | 备注 |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | 免费车道 —— 最便宜的 id，所以关卡在每个账户上都停在这里 |

该面板在 `default` 中提供、而本轨道**未**把关的 id：`mimo-v2.6-flash`、`deepseek-v4.1-flash`。它们保留在关卡的备用顺序中，不在已验证表格里。

## 🧪 如何使用 API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 快速开始

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

`keys.txt` **只有**在一次实时聊天补全证明该密钥有效后才会写入。

| 模式 | 作用 |
| --- | --- |
| `run`（默认） | 铸造账户直到 `--count`，用实时聊天对每个密钥把关 |
| `--verify-only` | 重新扫描 `keys.txt`，将失败项移入 `keys-ungated.txt` |
| `--screen` | 用你的代理列表测试面板 → `proxies.live.txt` |
| `--probe` | 环境检查：面板、定价、关卡顺序、出口 |
| `--collect` | 从 `accounts.json` 重建 `keys.txt`（每一行 `chat_status: 200`） |

### 🖱️ 启动器

启动器会询问代理列表、数量和模型，将它们记住在 `launcher.config.json` 中，并用正确的标志运行 CLI。

## ✅ 已验证可用 —— 每个密钥都经过测试

| 检查 | 结果 |
| --- | --- |
| `GET /api/status` | **200** —— 注册开放，无验证码，无邮件步骤 |
| `GET /api/pricing` | **200** —— 8 个定价 id，全部有已启用的分组；最便宜的是 `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` —— 无邮箱字段（5xx 会重试，然后用邮箱字段重试） |
| `POST /api/user/login` | **200** —— `access_token`（cookie + `New-Api-User` 作为旧分支的回退） |
| `GET /api/user/self` | **200** —— 读取账户记录的配额 |
| `POST /api/token/` → `POST /api/token/<id>/key` | 令牌已创建（`unlimited_quota: true`），明文密钥已揭示 |
| `POST /v1/chat/completions` —— 在写入**每个**密钥前运行 | **200** 且返回真实内容（`choices`，无 `error`） |
| 聊天检查失败的密钥 | 从不写入 `keys.txt` |

在运营者的集群上测得（后门轨道，2026-10-19）：**14/14 次尝试全部可用**，3 个 worker 共耗时 76 秒 → **5.4 秒/密钥**，每个密钥都在 `longcat-2.5-preview-free` 上把关。

## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 备注 |
| --- | --- | --- |
| 注册 | `POST /api/user/register {username,password}` | 无邮箱字段；5xx 会在 2.5 秒后重试，然后用地址重试该调用 |
| 登录 | `POST /api/user/login {username,password}` | `access_token`，外加会话 cookie，用于只返回 cookie 的分支 |
| 账户 | `GET /api/user/self` | id + 配额 |
| 定价 | `GET /api/pricing` | 最便宜的聊天模型及其 `enable_groups[0]` |
| 密钥 | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| 检查 | `POST /v1/chat/completions` | 写入 `keys.txt` 前必须实时 200；429 是限速信号（等待 2.5 秒，重试同一模型） |

## ⚙️ CLI 选项

```bash
node src/cli.js [mode] [flags]
```

| 标志 | 默认值 | 含义 |
| --- | --- | --- |
| `--count N` | 10 | 要铸造的账户数（= 密钥数） |
| `--conc N` | 2 | 并发账户数（最大 16）—— 每个 worker 固定一个出口 |
| `--host <name>` | `api.iruoke.top` | 面板主机（同一轨道可运行在该面板的镜像上） |
| `--model <id>` | 最便宜的定价模型 | 将关卡固定到一个模型 id |
| `--group <name>` | 模型的分组 | 固定令牌分组 |
| `--pool <file>` | `proxies.txt` | 代理列表（`IRUOKE_PROXIES` 环境变量也可用） |
| `--gap MS` | 800 | 同一账户内模型之间的暂停 |
| `--tries N` | 2 | 每个账户尝试的出口数 |

启动探测会遍历密钥池的前端，直到某个出口能响应面板，因此第一行失效不会中断运行。

## 🛰️ 在代理之后

每个请求都通过你密钥池中的一个出口发出 —— 工具从不直接连接面板，并且**一个账户在其整个生命周期内都固定在一个出口上**（面板将会话绑定到 IP）。实测：在规模测试期间，运营者密钥池中有 **9 个出口** 能访问该主机，铸造以并发 2-3 运行，没有出现一次 503。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 聊天返回 200 的密钥，每行一个 —— 已被 git 忽略 |
| `keys-ungated.txt` | 上次 `--verify-only` 扫描失败的密钥 —— 稍后重试 |
| `accounts.json` | 每个账户的用户名 / 配额 / 分组 / 模型 / 聊天状态 —— 已被 git 忽略 |
| `proxies.txt` | 你的出口列表 —— 已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、兼容 OpenAI 的路由器。一条命令即可将 `keys.txt` 变成一个带有可用组合的 provider-node：

```bash
node src/connect-9router.js
```

默认值：`--base http://localhost:20128`、`--password 123456`、`--name "Iruoke"`、`--prefix iruoke`、`--base-url https://api.iruoke.top/v1`。

## 🔑 一个密钥能给你什么

| | |
| --- | --- |
| 🏢 提供商 | **Iruoke** —— [api.iruoke.top](https://api.iruoke.top) |
| 🎁 提供商提供什么 | 每个密钥一个账户 —— 无需邮箱注册，免费 longcat 车道，每个密钥都经过聊天验证 |
| 🔑 一个密钥能给你什么 | 一个作用域为其自身账户的 API 密钥：下方端点上的 8 个模型 id，受你套餐的限制。配额存在于**账户**上，这正是本工具为每个密钥创建一个账户的原因 |
| 🌐 API 形态 | 兼容 OpenAI：`POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 你的数据 | 只联系 api.iruoke.top 和你配置的收件箱后端；密钥留在你的机器上 |

## 🐞 问题反馈

如果面板关闭了后门、取消了免费车道或更改了定价图，工具会在该步骤停止并说明情况（`--probe` 会显示它看到的内容）。**[提交 issue](../../issues)**，附上你运行的命令和确切的输出；欢迎 PR。

## 📄 许可证

MIT © 2026 —— 见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>