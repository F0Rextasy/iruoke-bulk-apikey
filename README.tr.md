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

**Otomatik oluşturulan API anahtarlarıyla toplu Iruoke hesabı açın — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-8-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

Iruoke, kardeşleriyle aynı **"nofield" kapısına** sahip bir New-API panelidir: `/api/user/register` yalnızca bir kullanıcı adı ve parola alır — **posta kutusu yok, captcha yok** — ve hesabın token'ı `unlimited_quota: true` ile oluşturulur. Bu paneli bir depoya değer kılan şey, bedavaya verdiği şerittir: **`longcat-2.5-preview-free`** her yeni hesapta, düz `default` grubunda yanıt verir — grup bağlaması gerekmez. Ölçeklenen birim *anahtar başına bir hesaptır* ve `keys.txt` içindeki her anahtar canlı bir sohbet çağrısıyla kanıtlanmıştır: operatörün filosunda **58 anahtar** (ilk kapı dalgasından 44'ü, ölçek çalıştırmasında **5.4 sn/anahtar, 14/14 canlı** hızıyla üretilen 14 tanesi daha; bu çalıştırma havuzu eşiğin üzerine taşıdı).

## 🎁 Ücretsiz katman — ne kadar alıyorsunuz

**Posta kutusu yok, captcha yok** — ücretsiz olan şey hesabın kendisidir ve token `unlimited_quota: true` ile oluşturulur.

| Ne | Değer |
| --- | --- |
| Kart gerekli mi | **Hayır** |
| Captcha / Turnstile | **yok** |
| Posta kutusu | **kullanılmaz** — kayıt çağrısı yalnızca `{username, password}` gönderir |
| Ücretsiz şerit | `default` grubunda `longcat-2.5-preview-free`, grup bağlaması gerekmez |
| Token | `unlimited_quota: true`, `expired_time: -1` |
| Ölçeklenen birim | **bir hesap = bir anahtar** → `--count N` demek "N hesap" demektir |

> 💡 Ücretsiz katman Iruoke tarafından belirlenir ve değişebilir. Güncel sayıları [api.iruoke.top](https://api.iruoke.top) adresinden kontrol edin — ve kararı aracın kendi kapısına bırakın:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 8'inin tamamı

Panel **8 id fiyatlandırır ve her birinin etkin bir grubu vardır** (`GET /api/pricing`). Yalnızca gerçek bir anahtarla canlı **200** yanıtı veren id'ler listelenmiştir:

| Model ID | ✓ | Grup | Notlar |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | ücretsiz şerit — en ucuz id, bu yüzden kapı her hesapta burada durur |

Panelin `default` içinde sunduğu ve bu rayın kapıdan geçirmediği id'ler: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Bunlar kapının yedek sırasında kalır ve doğrulanmış tablonun dışında tutulur.

## 🧪 API'yi nasıl kullanırım

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Hızlı başlangıç

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

`keys.txt` **yalnızca** canlı bir sohbet tamamlaması anahtarı kanıtladıktan sonra yazılır.

| Mod | Ne yapar |
| --- | --- |
| `run` (varsayılan) | `--count` değerine ulaşana kadar hesap üretir, her anahtarı canlı sohbetle kapıdan geçirir |
| `--verify-only` | `keys.txt` dosyasını yeniden tarar, başarısız olanları `keys-ungated.txt` dosyasına taşır |
| `--screen` | proxy listenizi panele karşı test eder → `proxies.live.txt` |
| `--probe` | ortam kontrolü: panel, fiyatlandırma, kapı sırası, çıkışlar |
| `--collect` | `accounts.json` dosyasından `keys.txt` dosyasını yeniden oluşturur (her `chat_status: 200` satırı) |

### 🖱️ Başlatıcı

Başlatıcı proxy listesini, sayıyı ve modeli sorar, bunları `launcher.config.json` dosyasında hatırlar ve CLI'yi doğru bayraklarla çalıştırır.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

| Kontrol | Sonuç |
| --- | --- |
| `GET /api/status` | **200** — kayıt açık, captcha yok, posta adımı yok |
| `GET /api/pricing` | **200** — 8 fiyatlı id, hepsinin etkin bir grubu var; en ucuzu `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — posta kutusu alanı yok (5xx yeniden denendi, ardından bir e-posta alanıyla yeniden denendi) |
| `POST /api/user/login` | **200** — `access_token` (eski çatallar için çerez + `New-Api-User` yedeği) |
| `GET /api/user/self` | **200** — hesap kaydı için kota okuması |
| `POST /api/token/` → `POST /api/token/<id>/key` | token oluşturuldu (`unlimited_quota: true`), düz metin anahtar açığa çıkarıldı |
| `POST /v1/chat/completions` — yazılmadan önce **her** anahtar için çalıştırılır | gerçek içerikle **200** (`choices`, `error` yok) |
| sohbet kontrolünde başarısız olan bir anahtar | `keys.txt` dosyasına asla yazılmaz |

Operatörün filosunda ölçüldü (kapı rayı, 2026-10-19): **14/14 deneme canlı**, 3 işçiyle toplam 76 sn → **5.4 sn/anahtar**, her anahtar `longcat-2.5-preview-free` üzerinde kapıdan geçirildi.

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| kayıt | `POST /api/user/register {username,password}` | e-posta alanı yok; 5xx 2.5 sn sonra yeniden denenir, ardından çağrı bir adresle yeniden denenir |
| giriş | `POST /api/user/login {username,password}` | `access_token`, ayrıca yalnızca çerez döndüren çatallar için oturum çerezi |
| hesap | `GET /api/user/self` | id + kota |
| fiyatlandırma | `GET /api/pricing` | en ucuz sohbet modeli ve `enable_groups[0]` |
| anahtar | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| kontrol | `POST /v1/chat/completions` | `keys.txt` öncesinde canlı 200 gerekir; 429 bir hız sinyalidir (2.5 sn bekle, aynı modeli yeniden dene) |

## ⚙️ CLI seçenekleri

```bash
node src/cli.js [mode] [flags]
```

| Bayrak | Varsayılan | Anlamı |
| --- | --- | --- |
| `--count N` | 10 | üretilecek hesap (= anahtar) sayısı |
| `--conc N` | 2 | eşzamanlı hesap (en fazla 16) — her işçiye bir çıkış sabitlenir |
| `--host <name>` | `api.iruoke.top` | panel ana bilgisayarı (aynı ray bu panelin yansımalarında da çalışır) |
| `--model <id>` | en ucuz fiyatlı | kapıyı tek bir model id'sine sabitler |
| `--group <name>` | modelin grubu | token grubunu sabitler |
| `--pool <file>` | `proxies.txt` | proxy listesi (`IRUOKE_PROXIES` ortam değişkeni de çalışır) |
| `--gap MS` | 800 | bir hesap içindeki modeller arasında bekleme |
| `--tries N` | 2 | hesap başına denenen çıkış sayısı |

Başlangıç sondası, havuzun başından itibaren panele yanıt veren bir çıkış bulana kadar ilerler; böylece ölü bir ilk satır çalıştırmayı durdurmaz.

## 🛰️ Proxy arkasında

Her istek havuzunuzdan bir çıkış üzerinden gider — araç panele asla doğrudan bağlanmaz ve **bir hesap tüm yaşamı boyunca tek bir çıkışta yaşar** (panel oturumu IP'ye bağlar). Ölçüldü: operatörün havuzundaki **9 çıkış** ölçek çalıştırması sırasında bu ana bilgisayara ulaşabildi ve üretim conc 2-3'te tek bir 503 olmadan çalıştı.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | sohbet-200 anahtarları, satır başına bir tane — git tarafından yok sayılır |
| `keys-ungated.txt` | son `--verify-only` taramasında başarısız olan anahtarlar — daha sonra yeniden deneyin |
| `accounts.json` | hesap başına kullanıcı adı / kota / grup / model / sohbet durumu — git tarafından yok sayılır |
| `proxies.txt` | çıkış listeniz — git tarafından yok sayılır |

## 🔌 9Router'a bağlanma

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir. Tek bir komut `keys.txt` dosyasını çalışan kombinasyonlara sahip bir sağlayıcı düğümüne dönüştürür:

```bash
node src/connect-9router.js
```

Varsayılanlar: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔌 OmniRoute'a bağlanma

[OmniRoute](https://omniroute.online) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir. Tek bir komut `keys.txt` dosyasını çalışan kombinasyonlara sahip bir sağlayıcı düğümüne dönüştürür:

```bash
node src/connect-omniroute.js
```

Varsayılanlar: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.
## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 Sağlayıcının verdiği | anahtar başına bir hesap — posta kutusuz kayıt, ücretsiz longcat şeridi, her anahtar sohbetle doğrulanmış |
| 🔑 Bir anahtar size ne kazandırır | kendi hesabına kapsanmış bir API anahtarı: aşağıdaki uç noktadaki 8 model id'si, planınızın sınırlarında. Kota **hesapta** yaşar, bu aracın anahtar başına bir hesap oluşturmasının nedeni tam da budur |
| 🌐 API şekli | OpenAI uyumlu: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca api.iruoke.top ve yapılandırdığınız posta kutusu arka ucuna bağlanılır; anahtarlar makinenizde kalır |

## 🐞 Sorunlar

Panel kapısını kapatırsa, ücretsiz şeridi düşürürse veya fiyatlandırma haritasını değiştirirse, araç o adımda durur ve bunu bildirir (`--probe` ne gördüğünü gösterir). Çalıştırdığınız komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**; PR'lar memnuniyetle karşılanır.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey katkıda bulunanlar

</div>