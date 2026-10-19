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

**Iruoke-Konten in großen Mengen erstellen mit automatisch generierten API-Schlüsseln — OpenAI-kompatible Inferenz + 9Router-Konnektor.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Kostenlose Stufe](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-8-of-them) · [🧪 Die API verwenden](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

Iruoke ist ein New-API-Panel mit derselben **„nofield"-Tür** wie seine Geschwister: `/api/user/register` nimmt einen Benutzernamen und ein Passwort und **keine Mailbox, kein Captcha**, und das Token des Kontos wird mit `unlimited_quota: true` erstellt. Was dieses Panel ein Repo wert macht, ist die Spur, die es verschenkt: **`longcat-2.5-preview-free`** antwortet auf jedem frischen Konto, in der einfachen `default`-Gruppe — keine Gruppenbindung nötig. Die Einheit, die skaliert, ist *ein Konto pro Schlüssel*, und jeder Schlüssel in `keys.txt` wurde mit einem Live-Chat-Aufruf bewiesen: **58 Schlüssel** in der Flotte des Betreibers (44 aus der ersten Tür-Welle, 14 weitere erzeugt mit **5,4 s/Schlüssel, 14/14 live** im Skalierungslauf, der den Pool über die Schwelle drückte).

## 🎁 Kostenlose Stufe — wie viel du bekommst

**Keine Mailbox, kein Captcha** — das Konto ist das Kostenlose, und das Token wird mit `unlimited_quota: true` erstellt.

| Was | Wert |
| --- | --- |
| Karte erforderlich | **Nein** |
| Captcha / Turnstile | **keins** |
| Mailbox | **nicht verwendet** — der Register-Aufruf sendet nur `{username, password}` |
| Kostenlose Spur | `longcat-2.5-preview-free` in Gruppe `default`, keine Gruppenbindung erforderlich |
| Token | `unlimited_quota: true`, `expired_time: -1` |
| Die Einheit, die skaliert | **ein Konto = ein Schlüssel** → `--count N` bedeutet „N Konten" |

> 💡 Die kostenlose Stufe wird von Iruoke festgelegt und kann sich ändern. Prüfe die Live-Zahlen unter [api.iruoke.top](https://api.iruoke.top) — und lass das eigene Gate des Tools entscheiden:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelle — alle 8

Das Panel bepreist **8 IDs und jede davon hat eine aktivierte Gruppe** (`GET /api/pricing`). Nur IDs, die mit einem echten Schlüssel eine Live-**200** geantwortet haben, sind aufgeführt:

| Modell-ID | ✓ | Gruppe | Hinweise |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | die kostenlose Spur — die günstigste ID, also stoppt das Gate bei jedem Konto hier |

IDs, die das Panel in `default` anbietet und die diese Rail **nicht** gegated hat: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Sie bleiben in der Fallback-Reihenfolge des Gates und außerhalb der verifizierten Tabelle.

## 🧪 Die API verwenden

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Schnellstart

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

`keys.txt` wird **erst** geschrieben, nachdem eine Live-Chat-Vervollständigung den Schlüssel bewiesen hat.

| Modus | Was er tut |
| --- | --- |
| `run` (Standard) | Konten erzeugen bis `--count`, jeden Schlüssel mit einem Live-Chat gaten |
| `--verify-only` | `keys.txt` erneut durchgehen und alles, was fehlschlägt, nach `keys-ungated.txt` verschieben |
| `--screen` | deine Proxy-Liste gegen das Panel testen → `proxies.live.txt` |
| `--probe` | Umgebungsprüfung: Panel, Preisgestaltung, Gate-Reihenfolge, Exits |
| `--collect` | `keys.txt` aus `accounts.json` neu aufbauen (jede Zeile mit `chat_status: 200`) |

### 🖱️ Der Launcher

Der Launcher fragt nach Proxy-Liste, Anzahl und Modell, merkt sie sich in `launcher.config.json` und führt die CLI mit den richtigen Flags aus.

## ✅ Verifiziert funktionierend — jeder Schlüssel wird getestet

| Prüfung | Ergebnis |
| --- | --- |
| `GET /api/status` | **200** — Registrierung offen, kein Captcha, kein Mail-Schritt |
| `GET /api/pricing` | **200** — 8 bepreiste IDs, alle mit einer aktivierten Gruppe; die günstigste ist `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — kein Mailbox-Feld (5xx wiederholt, dann mit einem E-Mail-Feld wiederholt) |
| `POST /api/user/login` | **200** — `access_token` (Cookie + `New-Api-User`-Fallback für ältere Forks) |
| `GET /api/user/self` | **200** — Quota für den Kontodatensatz gelesen |
| `POST /api/token/` → `POST /api/token/<id>/key` | Token erstellt (`unlimited_quota: true`), Klartext-Schlüssel offengelegt |
| `POST /v1/chat/completions` — für **jeden** Schlüssel ausgeführt, bevor er geschrieben wird | **200** mit echtem Inhalt (`choices`, kein `error`) |
| ein Schlüssel, der seine Chat-Prüfung nicht besteht | wird nie in `keys.txt` geschrieben |

Gemessen in der Flotte des Betreibers (Tür-Rail, 2026-10-19): **14/14 Versuche live**, 76 s insgesamt bei 3 Workern → **5,4 s/Schlüssel**, jeder Schlüssel auf `longcat-2.5-preview-free` gegated.

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| Registrierung | `POST /api/user/register {username,password}` | kein E-Mail-Feld; ein 5xx wird nach 2,5 s wiederholt, dann wird der Aufruf mit einer Adresse wiederholt |
| Login | `POST /api/user/login {username,password}` | `access_token`, plus das Session-Cookie für Forks, die nur ein Cookie zurückgeben |
| Konto | `GET /api/user/self` | ID + Quota |
| Preisgestaltung | `GET /api/pricing` | günstigstes Chat-Modell und seine `enable_groups[0]` |
| Schlüssel | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| Prüfung | `POST /v1/chat/completions` | Live-200 erforderlich vor `keys.txt`; ein 429 ist ein Pace-Signal (2,5 s warten, dasselbe Modell wiederholen) |

## ⚙️ CLI-Optionen

```bash
node src/cli.js [mode] [flags]
```

| Flag | Standard | Bedeutung |
| --- | --- | --- |
| `--count N` | 10 | Konten (= Schlüssel), die erzeugt werden sollen |
| `--conc N` | 2 | Konten gleichzeitig in Bearbeitung (max. 16) — pro Worker wird ein Exit festgelegt |
| `--host <name>` | `api.iruoke.top` | Panel-Host (dieselbe Rail läuft auf Mirrors dieses Panels) |
| `--model <id>` | günstigstes bepreistes | das Gate an eine Modell-ID binden |
| `--group <name>` | die Gruppe des Modells | die Token-Gruppe binden |
| `--pool <file>` | `proxies.txt` | Proxy-Liste (die Umgebungsvariable `IRUOKE_PROXIES` funktioniert ebenfalls) |
| `--gap MS` | 800 | Pause zwischen Modellen innerhalb eines Kontos |
| `--tries N` | 2 | Exits, die pro Konto versucht werden |

Die Startprüfung geht den Anfang des Pools durch, bis ein Exit dem Panel antwortet, sodass eine tote erste Zeile den Lauf nicht stoppt.

## 🛰️ Hinter einem Proxy

Jede Anfrage verlässt das System über einen Exit aus deinem Pool — das Tool wählt sich nie direkt in das Panel ein, und **ein Konto lebt auf einem Exit** für sein ganzes Leben (das Panel bindet die Sitzung an die IP). Gemessen: **9 Exits** im Pool des Betreibers konnten diesen Host während des Skalierungslaufs erreichen, und die Erzeugung lief bei conc 2-3 ohne einen einzigen 503.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | Chat-200-Schlüssel, einer pro Zeile — von Git ignoriert |
| `keys-ungated.txt` | Schlüssel, die den letzten `--verify-only`-Durchlauf nicht bestanden haben — später erneut versuchen |
| `accounts.json` | Benutzername / Quota / Gruppe / Modell / Chat-Status pro Konto — von Git ignoriert |
| `proxies.txt` | deine Exit-Liste — von Git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router. Ein Befehl verwandelt `keys.txt` in einen Provider-Node mit funktionierenden Kombos:

```bash
node src/connect-9router.js
```

Standardwerte: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔑 Was ein Schlüssel dir bringt

| | |
| --- | --- |
| 🏢 Anbieter | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 Was der Anbieter gibt | ein Konto pro Schlüssel — mailboxfreie Registrierung, kostenlose longcat-Spur, jeder Schlüssel chat-verifiziert |
| 🔑 Was ein Schlüssel dir bringt | einen API-Schlüssel, der auf sein eigenes Konto beschränkt ist: die 8 Modell-IDs am unten stehenden Endpunkt, im Rahmen der Limits deines Tarifs. Die Quota liegt auf dem **Konto**, weshalb dieses Tool genau ein Konto pro Schlüssel erstellt |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Deine Daten | nur api.iruoke.top und das von dir konfigurierte Inbox-Backend werden kontaktiert; Schlüssel bleiben auf deinem Rechner |

## 🐞 Issues

Wenn das Panel seine Tür schließt, die kostenlose Spur fallen lässt oder die Preisübersicht ändert, stoppt das Tool an diesem Schritt und sagt es (`--probe` zeigt, was es sieht). **[Öffne ein Issue](../../issues)** mit dem Befehl, den du ausgeführt hast, und der genauen Ausgabe; PRs sind willkommen.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>