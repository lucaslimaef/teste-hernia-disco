# Migração Netlify → Cloudflare

Registrar do domínio: **Domínios.pt** · DNS atual: **Netlify** · Email: **ImprovMX**

> ⚠️ O Netlify está **sem créditos** ("ran out of credits") — os sites hospedados
> lá podem ir abaixo. Migração é urgente.

---

## Inventário atual do domínio `ptlucaslima.pt`

| Registo | Tipo | Valor | Hospedagem |
|---|---|---|---|
| `ptlucaslima.pt` | MX 10 | mx1.improvmx.com | Email (ImprovMX) |
| `ptlucaslima.pt` | MX 20 | mx2.improvmx.com | Email (ImprovMX) |
| `ptlucaslima.pt` | TXT | `v=spf1 include:spf.improvmx.com ~all` | Email (SPF) |
| `ptlucaslima.pt` (apex) | NETLIFY | lucent-hotteok-9be691.netlify.app | **Netlify** |
| `www` | NETLIFY | lucent-hotteok-9be691.netlify.app | **Netlify** |
| `consultafuncional` | NETLIFY | consultafuncional-lucaslima.netlify.app | **Netlify** |
| `testehernia` | CNAME | lucaslimaef.github.io | GitHub Pages |
| `Bio` | CNAME | lucaslimaef.github.io | GitHub Pages |

O tipo **NETLIFY** é proprietário do Netlify DNS. No Cloudflare vira **CNAME**
(no apex, o Cloudflare faz "CNAME flattening" automaticamente, por isso é permitido).

---

## PARTE A — Mover o DNS para o Cloudflare (mantém tudo a funcionar)

### A1. Adicionar o domínio ao Cloudflare
1. dash.cloudflare.com → **Add a site** → `ptlucaslima.pt` → plano **Free**.
2. O Cloudflare importa os registos. **Confere** e garante que ficam EXATAMENTE estes:

| Nome | Tipo | Conteúdo | Prioridade | Proxy |
|---|---|---|---|---|
| `ptlucaslima.pt` | MX | mx1.improvmx.com | 10 | — |
| `ptlucaslima.pt` | MX | mx2.improvmx.com | 20 | — |
| `ptlucaslima.pt` | TXT | `v=spf1 include:spf.improvmx.com ~all` | — | — |
| `ptlucaslima.pt` | CNAME | lucent-hotteok-9be691.netlify.app | — | **DNS only (cinza)** |
| `www` | CNAME | lucent-hotteok-9be691.netlify.app | — | **DNS only (cinza)** |
| `consultafuncional` | CNAME | consultafuncional-lucaslima.netlify.app | — | **DNS only (cinza)** |
| `testehernia` | CNAME | lucaslimaef.github.io | — | **DNS only (cinza)** |
| `Bio` | CNAME | lucaslimaef.github.io | — | **DNS only (cinza)** |

> **Importante:** mantém os CNAME que apontam para Netlify/GitHub em **DNS only
> (nuvem cinzenta, não laranja)**. Com proxy laranja há risco de erro de SSL /
> loop de redirect, porque o certificado é emitido pelo Netlify/GitHub.

### A2. Trocar os nameservers no Domínios.pt
1. dominios.pt → **Os meus domínios** → `ptlucaslima.pt` → **Nameservers**.
2. Substituir pelos **2 nameservers do Cloudflare** (mostrados no fim do passo A1).
3. Guardar. Propagação: minutos a ~24h. Cloudflare envia email quando ativar.

### A3. Validar
- Email: enviar um teste para o teu `@ptlucaslima.pt` e ver se chega.
- Abrir cada site: `ptlucaslima.pt`, `www`, `consultafuncional`, `testehernia`, `Bio`.

---

## PARTE B — Tirar os sites do Netlify (sair de vez do Netlify)

Os sites **principal** (lucent-hotteok) e **consultafuncional** estão HOSPEDADOS
no Netlify. Mover o DNS não os tira de lá. Para deixar o Netlify por completo,
re-hospedar no **Cloudflare Pages** (mesmo processo do `testehernia`):

Para cada site:
1. Cloudflare → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Escolher o repositório GitHub correspondente. Build conforme o projeto
   (se for estático: build vazio, output `/`).
3. Em **Custom domains** do projeto Pages, adicionar o subdomínio
   (ex.: `consultafuncional.ptlucaslima.pt`). O Cloudflare passa o CNAME a
   apontar para o Pages e gere o SSL — aí o registo pode ficar **proxied (laranja)**.
4. Confirmar que abre e só depois desligar o site no Netlify.

> Falta identificar os repositórios GitHub do site principal e do consultafuncional.

---

## testehernia (este repo) — já preparado
- Config do Cloudflare Pages pronta (`wrangler.toml`, `_headers`).
- Está em GitHub Pages hoje; pode continuar assim ou migrar para Cloudflare Pages.
- O ficheiro `CNAME` na raiz é do GitHub Pages e é ignorado pelo Cloudflare Pages.
