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

**自動生成された API キーで Iruoke アカウントを一括作成 — OpenAI 互換の推論 + 9Router コネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-8-of-them) · [🧪 API の使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 イシュー](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ これが存在する理由

Iruoke は New-API パネルであり、その兄弟たちと同じ **「nofield」の扉** を持っています。`/api/user/register` はユーザー名とパスワードを受け取り、**メールボックスもキャプチャも不要**で、アカウントのトークンは `unlimited_quota: true` で作成されます。このパネルがリポジトリにする価値があるのは、それが無償で提供するレーンです。**`longcat-2.5-preview-free`** は、すべての新規アカウントで、素の `default` グループにおいて応答します — グループの紐付けは不要です。スケールする単位は *1 アカウントにつき 1 キー* であり、`keys.txt` のすべてのキーは実際のチャット呼び出しで証明されています。運用者のフリートでは **58 キー**（最初の扉の波で 44、さらに **5.4 秒/キー、14/14 ライブ** でスケール実行時に 14 が鋳造され、プールをしきい値以上に押し上げました）。

## 🎁 無料枠 — どれだけ得られるか

**メールボックス不要、キャプチャ不要** — アカウントが無料の対象であり、トークンは `unlimited_quota: true` で作成されます。

| 項目 | 値 |
| --- | --- |
| カード要求 | **なし** |
| キャプチャ / Turnstile | **なし** |
| メールボックス | **未使用** — 登録呼び出しは `{username, password}` のみを送信 |
| 無料レーン | グループ `default` の `longcat-2.5-preview-free`、グループ紐付け不要 |
| トークン | `unlimited_quota: true`、`expired_time: -1` |
| スケールする単位 | **1 アカウント = 1 キー** → `--count N` は「N アカウント」 |

> 💡 無料枠は Iruoke によって設定され、変更される可能性があります。最新の数値は [api.iruoke.top](https://api.iruoke.top) で確認してください — そしてツール自身のゲートに判断させてください:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全 8 種

パネルは **8 つの id に価格を設定しており、そのすべてに有効なグループがあります**（`GET /api/pricing`）。実際のキーでライブの **200** を返した id のみを掲載しています:

| モデル ID | ✓ | グループ | 備考 |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | 無料レーン — 最も安価な id なので、ゲートはすべてのアカウントでここで停止します |

パネルが `default` で提供し、このレールがゲートし **なかった** id: `mimo-v2.6-flash`、`deepseek-v4.1-flash`。これらはゲートのフォールバック順序に残り、検証済みテーブルには含まれません。

## 🧪 API の使い方

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 クイックスタート

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

`keys.txt` は、ライブのチャット補完がキーを証明した **後にのみ** 書き込まれます。

| モード | 動作 |
| --- | --- |
| `run`（デフォルト） | `--count` までアカウントを鋳造し、すべてのキーをライブチャットでゲート |
| `--verify-only` | `keys.txt` を再スイープし、失敗したものを `keys-ungated.txt` に移動 |
| `--screen` | プロキシリストをパネルに対してテスト → `proxies.live.txt` |
| `--probe` | 環境チェック: パネル、価格、ゲート順序、終了 |
| `--collect` | `accounts.json` から `keys.txt` を再構築（すべての `chat_status: 200` 行） |

### 🖱️ ランチャー

ランチャーはプロキシリスト、カウント、モデルを尋ね、それらを `launcher.config.json` に記憶し、適切なフラグで CLI を実行します。

## ✅ 検証済み — すべてのキーがテストされています

| チェック | 結果 |
| --- | --- |
| `GET /api/status` | **200** — 登録オープン、キャプチャなし、メール手順なし |
| `GET /api/pricing` | **200** — 価格設定された 8 つの id、すべてに有効なグループあり。最も安価なのは `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — メールボックスフィールドなし（5xx は再試行、その後メールフィールド付きで再試行） |
| `POST /api/user/login` | **200** — `access_token`（古いフォーク向けに cookie + `New-Api-User` フォールバック） |
| `GET /api/user/self` | **200** — アカウントレコード用にクォータを読み取り |
| `POST /api/token/` → `POST /api/token/<id>/key` | トークン作成（`unlimited_quota: true`）、平文キーを開示 |
| `POST /v1/chat/completions` — 書き込まれる **前** に **すべての** キーに対して実行 | 実際のコンテンツで **200**（`choices`、`error` なし） |
| チャットチェックに失敗したキー | `keys.txt` に書き込まれることは決してない |

運用者のフリートで測定（扉レール、2026-10-19）: **14/14 試行ライブ**、3 ワーカーで合計 76 秒 → **5.4 秒/キー**、すべてのキーが `longcat-2.5-preview-free` でゲート済み。

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| サインアップ | `POST /api/user/register {username,password}` | メールフィールドなし。5xx は 2.5 秒後に再試行され、その後アドレス付きで呼び出しが再試行される |
| ログイン | `POST /api/user/login {username,password}` | `access_token`、加えて cookie のみを返すフォーク向けのセッション cookie |
| アカウント | `GET /api/user/self` | id + クォータ |
| 価格 | `GET /api/pricing` | 最も安価なチャットモデルとその `enable_groups[0]` |
| キー | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| チェック | `POST /v1/chat/completions` | `keys.txt` の前にライブ 200 が必須。429 はペースのシグナル（2.5 秒待機し、同じモデルを再試行） |

## ⚙️ CLI オプション

```bash
node src/cli.js [mode] [flags]
```

| フラグ | デフォルト | 意味 |
| --- | --- | --- |
| `--count N` | 10 | 鋳造するアカウント数（= キー） |
| `--conc N` | 2 | 同時実行中のアカウント数（最大 16） — ワーカーごとに 1 つの出口が固定される |
| `--host <name>` | `api.iruoke.top` | パネルホスト（同じレールがこのパネルのミラーでも動作する） |
| `--model <id>` | 最も安価な価格設定 | ゲートを 1 つのモデル id に固定 |
| `--group <name>` | モデルのグループ | トークングループを固定 |
| `--pool <file>` | `proxies.txt` | プロキシリスト（`IRUOKE_PROXIES` 環境変数も可） |
| `--gap MS` | 800 | 1 アカウント内のモデル間の一時停止 |
| `--tries N` | 2 | アカウントごとに試行する出口数 |

起動時のプローブは、出口がパネルに応答するまでプールの先頭をたどるので、最初の行が死んでいても実行は止まりません。

## 🛰️ プロキシの背後で

すべてのリクエストはプールからの出口を通って出ていきます — ツールがパネルに直接ダイヤルすることはなく、**1 つのアカウントはその生涯を通じて 1 つの出口上に存在します**（パネルはセッションを IP に紐付けます）。測定: 運用者のプールの **9 つの出口** がスケール実行中にこのホストに到達でき、鋳造は conc 2-3 で 503 を一度も出さずに実行されました。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | chat-200 キー、1 行に 1 つ — git 無視 |
| `keys-ungated.txt` | 最後の `--verify-only` スイープに失敗したキー — 後で再試行 |
| `accounts.json` | アカウントごとの username / quota / group / model / chat status — git 無視 |
| `proxies.txt` | あなたの出口リスト — git 無視 |

## 🔌 9Router に接続

[9Router](https://9router.com) はセルフホスト型の OpenAI 互換ルーターです。1 つのコマンドで `keys.txt` を動作するコンボを持つプロバイダーノードに変えます:

```bash
node src/connect-9router.js
```

デフォルト: `--base http://localhost:20128`、`--password 123456`、`--name "Iruoke"`、`--prefix iruoke`、`--base-url https://api.iruoke.top/v1`。

## 🔑 1 つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダー | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 プロバイダーが提供するもの | 1 キーにつき 1 アカウント — メールボックス不要のサインアップ、無料の longcat レーン、すべてのキーがチャット検証済み |
| 🔑 1 つのキーで得られるもの | 自身のアカウントにスコープされた 1 つの API キー: 以下のエンドポイントの 8 つのモデル id を、あなたのプランの制限で。クォータは **アカウント** に存在し、まさにそれがこのツールが 1 キーにつき 1 アカウントを作成する理由です |
| 🌐 API の形 | OpenAI 互換: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 あなたのデータ | api.iruoke.top と、あなたが設定したインボックスバックエンドのみが接触されます。キーはあなたのマシンに留まります |

## 🐞 イシュー

パネルが扉を閉じたり、無料レーンを廃止したり、価格マップを変更した場合、ツールはそのステップで停止し、その旨を報告します（`--probe` はそれが見ているものを表示します）。実行したコマンドと正確な出力を添えて **[イシューを開いてください](../../issues)**。PR も歓迎します。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE) を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>