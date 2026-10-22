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

**Crea cuentas de Iruoke en masa con claves API autogeneradas — inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-8-of-them) · [🧪 Usa la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por qué existe esto

Iruoke es un panel New-API con la misma **puerta "nofield"** que sus hermanos: `/api/user/register` acepta un nombre de usuario y una contraseña y **ni buzón, ni captcha**, y el token de la cuenta se crea con `unlimited_quota: true`. Lo que hace que este panel merezca un repositorio es el carril que regala: **`longcat-2.5-preview-free`** responde en cada cuenta nueva, en el grupo `default` sin más — no hace falta vincular ningún grupo. La unidad que escala es *una cuenta por clave*, y cada clave en `keys.txt` fue probada con una llamada de chat en vivo: **58 claves** en la flota del operador (44 de la primera oleada de puertas, 14 más acuñadas a **5.4 s/clave, 14/14 en vivo** en la ejecución de escalado que empujó el grupo por encima del umbral).

## 🎁 Nivel gratuito — cuánto obtienes

**Sin buzón, sin captcha** — la cuenta es lo gratuito, y el token se crea con `unlimited_quota: true`.

| Qué | Valor |
| --- | --- |
| Tarjeta requerida | **No** |
| Captcha / Turnstile | **ninguno** |
| Buzón | **no se usa** — la llamada de registro envía solo `{username, password}` |
| Carril gratuito | `longcat-2.5-preview-free` en el grupo `default`, no requiere vincular grupo |
| Token | `unlimited_quota: true`, `expired_time: -1` |
| La unidad que escala | **una cuenta = una clave** → `--count N` son "N cuentas" |

> 💡 El nivel gratuito lo define Iruoke y puede cambiar. Consulta los números en vivo en [api.iruoke.top](https://api.iruoke.top) — y deja que la propia puerta de la herramienta decida:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — los 8

El panel pone precio a **8 ids y todos ellos tienen un grupo habilitado** (`GET /api/pricing`). Solo se listan los ids que respondieron un **200** en vivo con una clave real:

| ID del modelo | ✓ | Grupo | Notas |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | el carril gratuito — el id más barato, así que la puerta se detiene aquí en cada cuenta |

Ids que el panel ofrece en `default` y que este carril **no** filtró: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Permanecen en el orden de respaldo de la puerta y fuera de la tabla verificada.

## 🧪 Cómo usar la API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Inicio rápido

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

`keys.txt` se escribe **solo** después de que una finalización de chat en vivo demuestre que la clave funciona.

| Modo | Qué hace |
| --- | --- |
| `run` (por defecto) | acuña cuentas hasta `--count`, filtra cada clave con un chat en vivo |
| `--verify-only` | vuelve a revisar `keys.txt`, moviendo lo que falle a `keys-ungated.txt` |
| `--screen` | prueba tu lista de proxies contra el panel → `proxies.live.txt` |
| `--probe` | comprobación del entorno: panel, precios, orden de la puerta, salidas |
| `--collect` | reconstruye `keys.txt` desde `accounts.json` (cada fila con `chat_status: 200`) |

### 🖱️ El lanzador

El lanzador pide la lista de proxies, la cantidad y el modelo, los recuerda en `launcher.config.json` y ejecuta el CLI con las banderas correctas.

## ✅ Verificado y funcionando — cada clave se prueba

| Comprobación | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro abierto, sin captcha, sin paso de correo |
| `GET /api/pricing` | **200** — 8 ids con precio, todos con un grupo habilitado; el más barato es `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — sin campo de buzón (5xx reintentado, luego reintentado con un campo de correo) |
| `POST /api/user/login` | **200** — `access_token` (cookie + respaldo `New-Api-User` para forks antiguos) |
| `GET /api/user/self` | **200** — lectura de cuota para el registro de la cuenta |
| `POST /api/token/` → `POST /api/token/<id>/key` | token creado (`unlimited_quota: true`), clave en texto plano revelada |
| `POST /v1/chat/completions` — ejecutado para **cada** clave antes de escribirse | **200** con contenido real (`choices`, sin `error`) |
| una clave que falla su comprobación de chat | nunca se escribe en `keys.txt` |

Medido en la flota del operador (carril de puertas, 2026-10-19): **14/14 intentos en vivo**, 76 s en total con 3 workers → **5.4 s/clave**, cada clave filtrada en `longcat-2.5-preview-free`.

## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| registro | `POST /api/user/register {username,password}` | sin campo de correo; un 5xx se reintenta tras 2.5 s, luego la llamada se reintenta con una dirección |
| inicio de sesión | `POST /api/user/login {username,password}` | `access_token`, más la cookie de sesión para forks que solo devuelven una cookie |
| cuenta | `GET /api/user/self` | id + cuota |
| precios | `GET /api/pricing` | el modelo de chat más barato y su `enable_groups[0]` |
| clave | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| comprobación | `POST /v1/chat/completions` | se requiere un 200 en vivo antes de `keys.txt`; un 429 es una señal de ritmo (espera 2.5 s, reintenta el mismo modelo) |

## ⚙️ Opciones del CLI

```bash
node src/cli.js [mode] [flags]
```

| Bandera | Por defecto | Significado |
| --- | --- | --- |
| `--count N` | 10 | cuentas (= claves) a acuñar |
| `--conc N` | 2 | cuentas en vuelo (máx 16) — se fija una salida por worker |
| `--host <name>` | `api.iruoke.top` | host del panel (el mismo carril funciona en réplicas de este panel) |
| `--model <id>` | el más barato con precio | fija la puerta a un id de modelo |
| `--group <name>` | el grupo del modelo | fija el grupo del token |
| `--pool <file>` | `proxies.txt` | lista de proxies (la variable de entorno `IRUOKE_PROXIES` también funciona) |
| `--gap MS` | 800 | pausa entre modelos dentro de una misma cuenta |
| `--tries N` | 2 | salidas probadas por cuenta |

La sonda de arranque recorre el frente del grupo hasta que una salida responde al panel, así que una primera línea muerta no detiene la ejecución.

## 🛰️ Detrás de un proxy

Cada solicitud sale a través de una salida de tu grupo — la herramienta nunca marca el panel directamente, y **una cuenta vive en una salida** durante toda su vida (el panel vincula la sesión a la IP). Medido: **9 salidas** en el grupo del operador pudieron alcanzar este host durante la ejecución de escalado, y la acuñación corrió a conc 2-3 sin un solo 503.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | claves con chat-200, una por línea — ignorado por git |
| `keys-ungated.txt` | claves que fallaron la última revisión de `--verify-only` — reintentar más tarde |
| `accounts.json` | nombre de usuario / cuota / grupo / modelo / estado de chat por cuenta — ignorado por git |
| `proxies.txt` | tu lista de salidas — ignorado por git |

## 🔌 Conectar con 9Router

[9Router](https://9router.com) es un router autoalojado y compatible con OpenAI. Un comando convierte `keys.txt` en un nodo proveedor con combos funcionales:

```bash
node src/connect-9router.js
```

Valores por defecto: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 Qué da el proveedor | una cuenta por clave — registro sin buzón, carril longcat gratuito, cada clave verificada por chat |
| 🔑 Qué te da una clave | una clave API limitada a su propia cuenta: los 8 ids de modelo en el endpoint de abajo, con los límites de tu plan. La cuota vive en la **cuenta**, que es exactamente por lo que esta herramienta crea una cuenta por clave |
| 🌐 Forma de la API | Compatible con OpenAI: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Tus datos | solo se contacta api.iruoke.top y el backend de buzón que configures; las claves permanecen en tu máquina |

## 🐞 Problemas

Si el panel cierra su puerta, elimina el carril gratuito o cambia el mapa de precios, la herramienta se detiene en ese paso y lo indica (`--probe` muestra lo que ve). **[Abre un issue](../../issues)** con el comando que ejecutaste y la salida exacta; los PR son bienvenidos.

## 📄 Licencia

MIT © 2026 — consulta [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>