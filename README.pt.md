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

**Crie contas Iruoke em massa com chaves de API geradas automaticamente — inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-8%20served-412991?style=for-the-badge)](https://api.iruoke.top/pricing)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Camada gratuita](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-8-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

Iruoke é um painel New-API com a mesma **porta "nofield"** que seus irmãos: `/api/user/register` aceita um nome de usuário e uma senha e **nenhuma caixa de correio, nenhum captcha**, e o token da conta é criado com `unlimited_quota: true`. O que torna este painel digno de um repositório é a faixa que ele libera: **`longcat-2.5-preview-free`** responde em toda conta nova, no grupo simples `default` — sem necessidade de vinculação de grupo. A unidade que escala é *uma conta por chave*, e cada chave em `keys.txt` foi comprovada com uma chamada de chat ao vivo: **58 chaves** na frota do operador (44 da primeira onda de portas, 14 a mais cunhadas a **5,4 s/chave, 14/14 ao vivo** na execução de escala que empurrou o pool além do limiar).

## 🎁 Camada gratuita — quanto você recebe

**Sem caixa de correio, sem captcha** — a conta é a coisa gratuita, e o token é criado com `unlimited_quota: true`.

| O quê | Valor |
| --- | --- |
| Cartão exigido | **Não** |
| Captcha / Turnstile | **nenhum** |
| Caixa de correio | **não usada** — a chamada de registro envia apenas `{username, password}` |
| Faixa gratuita | `longcat-2.5-preview-free` no grupo `default`, sem necessidade de vinculação de grupo |
| Token | `unlimited_quota: true`, `expired_time: -1` |
| A unidade que escala | **uma conta = uma chave** → `--count N` é "N contas" |

> 💡 A camada gratuita é definida pela Iruoke e pode mudar. Confira os números ao vivo em [api.iruoke.top](https://api.iruoke.top) — e deixe o próprio portão da ferramenta decidir:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — todos os 8

O painel precifica **8 ids e todos eles têm um grupo habilitado** (`GET /api/pricing`). Apenas ids que responderam um **200** ao vivo com uma chave real estão listados:

| ID do modelo | ✓ | Grupo | Notas |
| --- | --- | --- | --- |
| `longcat-2.5-preview-free` | ✓ | `default` | a faixa gratuita — o id mais barato, então o portão para aqui em toda conta |

Ids que o painel oferece em `default` e que este trilho **não** passou pelo portão: `mimo-v2.6-flash`, `deepseek-v4.1-flash`. Eles permanecem na ordem de fallback do portão e fora da tabela verificada.

## 🧪 Como usar a API

```bash
curl https://api.iruoke.top/v1/chat/completions \
  -H "Authorization: Bearer <your key>" \
  -H "Content-Type: application/json" \
  -d '{"model":"longcat-2.5-preview-free","messages":[{"role":"user","content":"hi"}],"max_tokens":16}'
```

## 🛠 Início rápido

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

`keys.txt` é escrito **somente** após uma conclusão de chat ao vivo provar a chave.

| Modo | O que faz |
| --- | --- |
| `run` (padrão) | cunha contas até `--count`, passa cada chave pelo portão com um chat ao vivo |
| `--verify-only` | varre novamente `keys.txt`, movendo qualquer coisa que falhe para `keys-ungated.txt` |
| `--screen` | testa sua lista de proxies contra o painel → `proxies.live.txt` |
| `--probe` | verificação de ambiente: painel, preços, ordem do portão, saídas |
| `--collect` | reconstrói `keys.txt` a partir de `accounts.json` (toda linha `chat_status: 200`) |

### 🖱️ O launcher

O launcher pede a lista de proxies, a contagem e o modelo, lembra-os em `launcher.config.json` e executa o CLI com as flags corretas.

## ✅ Verificado e funcionando — toda chave é testada

| Verificação | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro aberto, sem captcha, sem etapa de e-mail |
| `GET /api/pricing` | **200** — 8 ids precificados, todos com um grupo habilitado; o mais barato é `longcat-2.5-preview-free` |
| `POST /api/user/register` | **200** `{username,password}` — sem campo de caixa de correio (5xx repetido, depois repetido com um campo de e-mail) |
| `POST /api/user/login` | **200** — `access_token` (cookie + fallback `New-Api-User` para forks mais antigos) |
| `GET /api/user/self` | **200** — cota lida para o registro da conta |
| `POST /api/token/` → `POST /api/token/<id>/key` | token criado (`unlimited_quota: true`), chave em texto simples revelada |
| `POST /v1/chat/completions` — executado para **toda** chave antes de ser escrita | **200** com conteúdo real (`choices`, sem `error`) |
| uma chave que falha na verificação de chat | nunca escrita em `keys.txt` |

Medido na frota do operador (trilho de porta, 2026-10-19): **14/14 tentativas ao vivo**, 76 s no total com 3 workers → **5,4 s/chave**, toda chave passada pelo portão em `longcat-2.5-preview-free`.

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Iruoke page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://api.iruoke.top/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Notas |
| --- | --- | --- |
| cadastro | `POST /api/user/register {username,password}` | sem campo de e-mail; um 5xx é repetido após 2,5 s, depois a chamada é repetida com um endereço |
| login | `POST /api/user/login {username,password}` | `access_token`, mais o cookie de sessão para forks que retornam apenas um cookie |
| conta | `GET /api/user/self` | id + cota |
| preços | `GET /api/pricing` | modelo de chat mais barato e seu `enable_groups[0]` |
| chave | `POST /api/token/ {group}` → `POST /api/token/<id>/key` | `unlimited_quota: true` |
| verificação | `POST /v1/chat/completions` | 200 ao vivo exigido antes de `keys.txt`; um 429 é um sinal de ritmo (espere 2,5 s, repita o mesmo modelo) |

## ⚙️ Opções do CLI

```bash
node src/cli.js [mode] [flags]
```

| Flag | Padrão | Significado |
| --- | --- | --- |
| `--count N` | 10 | contas (= chaves) a cunhar |
| `--conc N` | 2 | contas em andamento (máx. 16) — uma saída é fixada por worker |
| `--host <name>` | `api.iruoke.top` | host do painel (o mesmo trilho roda em espelhos deste painel) |
| `--model <id>` | mais barato precificado | fixa o portão a um id de modelo |
| `--group <name>` | o grupo do modelo | fixa o grupo do token |
| `--pool <file>` | `proxies.txt` | lista de proxies (a variável de ambiente `IRUOKE_PROXIES` também funciona) |
| `--gap MS` | 800 | pausa entre modelos dentro de uma conta |
| `--tries N` | 2 | saídas tentadas por conta |

A sondagem de inicialização percorre a frente do pool até que uma saída responda ao painel, então uma primeira linha morta não interrompe a execução.

## 🛰️ Atrás de um proxy

Toda requisição sai através de uma saída do seu pool — a ferramenta nunca disca diretamente para o painel, e **uma conta vive em uma saída** durante toda a sua vida (o painel vincula a sessão ao IP). Medido: **9 saídas** no pool do operador conseguiram alcançar este host durante a execução de escala, e a cunhagem rodou em conc 2-3 sem um único 503.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | chaves com chat-200, uma por linha — ignorado pelo git |
| `keys-ungated.txt` | chaves que falharam na última varredura `--verify-only` — tente novamente depois |
| `accounts.json` | nome de usuário / cota / grupo / modelo / status de chat por conta — ignorado pelo git |
| `proxies.txt` | sua lista de saídas — ignorado pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado e compatível com OpenAI. Um comando transforma `keys.txt` em um nó provedor com combos funcionais:

```bash
node src/connect-9router.js
```

Padrões: `--base http://localhost:20128`, `--password 123456`, `--name "Iruoke"`, `--prefix iruoke`, `--base-url https://api.iruoke.top/v1`.

## 🔑 O que uma chave te dá

| | |
| --- | --- |
| 🏢 Provedor | **Iruoke** — [api.iruoke.top](https://api.iruoke.top) |
| 🎁 O que o provedor dá | uma conta por chave — cadastro sem caixa de correio, faixa longcat gratuita, toda chave verificada por chat |
| 🔑 O que uma chave te dá | uma chave de API limitada à sua própria conta: os 8 ids de modelo no endpoint abaixo, nos limites do seu plano. A cota vive na **conta**, que é exatamente por isso que esta ferramenta cria uma conta por chave |
| 🌐 Formato da API | Compatível com OpenAI: `POST https://api.iruoke.top/v1/chat/completions` |
| 🔒 Seus dados | apenas api.iruoke.top e o backend de caixa de entrada que você configurar são contatados; as chaves permanecem na sua máquina |

## 🐞 Problemas

Se o painel fechar sua porta, remover a faixa gratuita ou mudar o mapa de preços, a ferramenta para nessa etapa e avisa (`--probe` mostra o que ela vê). **[Abra uma issue](../../issues)** com o comando que você executou e a saída exata; PRs são bem-vindos.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 iruoke-bulk-apikey contributors

</div>