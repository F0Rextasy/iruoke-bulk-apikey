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

**Créez en masse des comptes Iruoke avec des clés API générées automatiquement — inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Niveau gratuit](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-8-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi ce projet existe

Iruoke est un panneau New-API avec la même **porte « nofield »** que ses semblables : `/api/user/register` prend un nom d'utilisateur et un mot de passe et **aucune boîte mail, aucun captcha**, et le jeton du compte est créé avec `unlimited_quota: true`. Ce qui rend ce panneau digne d'un dépôt, c'est la voie qu'il offre : **`longcat-2.5-preview-free`** répond sur chaque nouveau compte, dans le groupe `default` ordinaire — aucune liaison de groupe nécessaire. L'unité qui passe à l'échelle est *un compte par clé*, et chaque clé dans `keys.txt` a été prouvée par un appel de chat en direct : **58 clés** sur la flotte de l'opérateur (44 issues de la première vague de la porte, 14 de plus créées à **5,4 s/clé, 14/14 en direct** lors de l'exécution à l'échelle qui a poussé le pool au-delà du seuil).

## 🎁 Niveau gratuit — ce que vous obtenez

**Pas de boîte mail, pas de captcha** — le compte est la chose gratuite, et le jeton est créé avec `unlimited_quota: true`.

| Élément | Valeur |
| --- | --- |
| Carte requise | **Non** |
| Captcha / Turnstile | **aucun** |
| Boîte mail | **non utilisée** — l'appel d'inscription envoie uniquement `{username, password}` |
| Voie gratuite | `longcat-2.5-preview-free` dans le groupe `default`, aucune liaison de groupe requise |
| Jeton | `unlimited_quota: true`, `expired_time: -1` |
| L'unité qui passe à l'échelle | **un compte = une clé** → `--count N` signifie « N comptes » |

> 💡 Le niveau gratuit est défini par Iruoke et peut changer. Consultez les chiffres en direct sur [api.iruoke.top](https://api.iruoke.top) — et laissez la propre barrière de l'outil décider :

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modèles — les 8 au complet

Le panneau tarife **8 identifiants et chacun d'eux a un groupe activé** (`GET /api/pricing`). Seuls les identifiants ayant répondu un **200** en direct avec une vraie clé sont listés :

| ID du modèle | ✓ | Groupe | Remarques |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | la voie gratuite — l'identifiant le moins cher, donc la barrière s'arrête ici sur chaque compte |

Identifiants que le panneau propose dans `default` et que ce rail n'a **pas** filtrés : `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Ils restent dans l'ordre de repli de la barrière et hors du tableau vérifié.

## 🧪 Comment utiliser l'API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Démarrage rapide

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

`keys.txt` n'est écrit **qu'**après qu'une complétion de chat en direct a prouvé la clé.

| Mode | Ce qu'il fait |
| --- | --- |
| `run` (par défaut) | crée des comptes jusqu'à `--count`, filtre chaque clé avec un chat en direct |
| `--verify-only` | re-parcourt `keys.txt`, déplaçant tout ce qui échoue vers `keys-ungated.txt` |
| `--screen` | teste votre liste de proxies contre le panneau → `proxies.live.txt` |
| `--probe` | vérification de l'environnement : panneau, tarification, ordre de la barrière, sorties |
| `--collect` | reconstruit `keys.txt` à partir de `accounts.json` (chaque ligne `chat_status: 200`) |

### 🖱️ Le lanceur

Le lanceur demande la liste de proxies, le nombre et le modèle, les mémorise dans `launcher.config.json`, et exécute le CLI avec les bons drapeaux.

## ✅ Vérifié fonctionnel — chaque clé est testée

| Vérification | Résultat |
| --- | --- |
| `GET /api/status` | **200** — inscription ouverte, pas de captcha, pas d'étape mail |
| `GET /api/pricing` | **200** — 8 identifiants tarifés, tous avec un groupe activé ; le moins cher est `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — aucun champ boîte mail (5xx réessayé, puis réessayé avec un champ e-mail) |
| `POST /api/user/login` | **200** — `access_token` (cookie + repli `New-Api-User` pour les anciens forks) |
| `GET /api/user/self` | **200** — quota lu pour l'enregistrement du compte |
| `POST /api/token/` → `POST /api/token/<id>/key` | jeton créé (`unlimited_quota: true`), clé en clair révélée |
| `POST /v1/chat/completions` — exécuté pour **chaque** clé avant son écriture | **200** avec du contenu réel (`choices`, pas d'`error`) |
| une clé qui échoue à sa vérification de chat | jamais écrite dans `keys.txt` |

Mesuré sur la flotte de l'opérateur (rail de la porte, 2026-10-19) : **14/14 tentatives en direct**, 76 s au total à 3 workers → **5,4 s/clé**, chaque clé filtrée sur `longcat-2.5-preview-free`.

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Remarques |
| --- | --- | --- |
| inscription | `POST /api/user/register {username,password}` | aucun champ e-mail ; un 5xx est réessayé après 2,5 s, puis l'appel est réessayé avec une adresse |
| connexion | `POST /api/user/login {username,password}` | `access_token`, plus le cookie de session pour les forks qui ne renvoient qu'un cookie |
| compte | `GET /api/user/self` | id + quota |
| tarification | `GET /api/pricing` | modèle de chat le moins cher et son `enable_groups[0]` |
| clé | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| vérification | `POST /v1/chat/completions` | un 200 en direct requis avant `keys.txt` ; un 429 est un signal de rythme (attendre 2,5 s, réessayer le même modèle) |

## ⚙️ Options du CLI

```bash
node src/cli.js [mode] [flags]
```

| Drapeau | Défaut | Signification |
| --- | --- | --- |
| `--count N` | 10 | comptes (= clés) à créer |
| `--conc N` | 2 | comptes en cours (max 16) — une sortie est épinglée par worker |
| `--host <name>` | `api.iruoke.top` | hôte du panneau (le même rail fonctionne sur les miroirs de ce panneau) |
| `--model <id>` | le moins cher tarifé | épingle la barrière à un identifiant de modèle |
| `--group <name>` | le groupe du modèle | épingle le groupe du jeton |
| `--pool <file>` | `proxies.txt` | liste de proxies (la variable d'env `IRUOKE_PROXIES` fonctionne aussi) |
| `--gap MS` | 800 | pause entre les modèles au sein d'un même compte |
| `--tries N` | 2 | sorties essayées par compte |

La sonde de démarrage parcourt le début du pool jusqu'à ce qu'une sortie réponde au panneau, ainsi une première ligne morte n'arrête pas l'exécution.

## 🛰️ Derrière un proxy

Chaque requête sort par une sortie de votre pool — l'outil ne contacte jamais le panneau directement, et **un compte vit sur une seule sortie** pendant toute sa durée de vie (le panneau lie la session à l'IP). Mesuré : **9 sorties** dans le pool de l'opérateur pouvaient atteindre cet hôte pendant l'exécution à l'échelle, et la création s'est déroulée à conc 2-3 sans un seul 503.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | clés chat-200, une par ligne — ignoré par git |
| `keys-ungated.txt` | clés ayant échoué au dernier parcours `--verify-only` — à réessayer plus tard |
| `accounts.json` | nom d'utilisateur / quota / groupe / modèle / statut de chat par compte — ignoré par git |
| `proxies.txt` | votre liste de sorties — ignoré par git |

## 🔌 Se connecter à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé, compatible OpenAI. Une commande transforme `keys.txt` en un nœud fournisseur avec des combos fonctionnels :

```bash
node src/connect-9router.js
```

Valeurs par défaut : `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔌 Se connecter à OmniRoute

[OmniRoute](https://omniroute.online) est un routeur auto-hébergé, compatible OpenAI. Une commande transforme `keys.txt` en un nœud fournisseur avec des combos fonctionnels :

```bash
node src/connect-omniroute.js
```

Valeurs par défaut : `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.
## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 Ce que le fournisseur donne | un compte par clé — inscription sans boîte mail, voie longcat gratuite, chaque clé vérifiée par chat |
| 🔑 Ce qu'une clé vous apporte | une clé API limitée à son propre compte : les 8 identifiants de modèle sur le point de terminaison ci-dessous, aux limites de votre forfait. Le quota réside sur le **compte**, ce qui explique précisément pourquoi cet outil crée un compte par clé |
| 🌐 Forme de l'API | Compatible OpenAI : `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Vos données | seuls api.iruoke.top et le backend de boîte mail que vous configurez sont contactés ; les clés restent sur votre machine |

## 🐞 Problèmes

Si le panneau ferme sa porte, supprime la voie gratuite ou modifie la carte de tarification, l'outil s'arrête à cette étape et le signale (`--probe` montre ce qu'il voit). **[Ouvrez un ticket](../../issues)** avec la commande que vous avez exécutée et la sortie exacte ; les PR sont bienvenues.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>