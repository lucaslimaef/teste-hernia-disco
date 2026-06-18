# Migração Netlify → Cloudflare Pages

Site: **testehernia.ptlucaslima.pt** (estático, sem build)
Registrar do domínio: **Domínios.pt**

O lado do código já está pronto neste repo (`_headers`, `wrangler.toml`).
Falta só a parte dos painéis — feita **uma vez** por ti, por segurança.

---

## Passo 1 — Ligar o repo ao Cloudflare Pages
1. Entra em https://dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** → **Connect to Git**.
2. Autoriza o GitHub e escolhe o repo `lucaslimaef/teste-hernia-disco`.
3. Branch de produção: **`main`** (ou a branch que quiseres publicar).
4. Configuração de build:
   - **Framework preset:** `None`
   - **Build command:** *(deixar vazio)*
   - **Build output directory:** `/`
5. **Save and Deploy.** Em segundos tens um URL tipo `teste-hernia-disco.pages.dev` — confirma que a página abre.

> A partir daqui, cada `git push` publica sozinho (igual ao Netlify antes).

## Passo 2 — Adicionar o domínio ao Cloudflare
1. No dash → **Add a site** → `ptlucaslima.pt` → plano **Free**.
2. O Cloudflare faz uma varredura e **importa os registos DNS atuais**.
3. ⚠️ **CONFERIR A LISTA antes de continuar** — especialmente:
   - **MX** (email `@ptlucaslima.pt`, se existir) → tem de aparecer igual ao atual.
   - **TXT** (SPF / DKIM / verificações) → preservar.
   - Outros A/CNAME de subdomínios ou sites que uses.
   - Se algo faltar, **adiciona manualmente antes do passo 3**.
4. O Cloudflare mostra **2 nameservers** (ex.: `xxxx.ns.cloudflare.com`).

## Passo 3 — Trocar nameservers no Domínios.pt
1. Login em https://www.dominios.pt → **Os meus domínios** → `ptlucaslima.pt`.
2. Secção de **Nameservers / DNS**.
3. Remove os nameservers atuais (Netlify) e coloca os **2 do Cloudflare**.
4. Guardar. Propagação: minutos a ~24h (no `.pt` costuma ser rápido).
5. O Cloudflare envia email *"ptlucaslima.pt is now active"*.

## Passo 4 — Apontar o subdomínio para o Pages
1. Cloudflare → **Workers & Pages** → projeto → **Custom domains** → **Set up a custom domain**.
2. Escreve `testehernia.ptlucaslima.pt` → o Cloudflare cria o registo automaticamente.
3. Espera o estado ficar **Active** (SSL emitido).

## Passo 5 — Validar e desligar o Netlify
1. Abre https://testehernia.ptlucaslima.pt → carrega por HTTPS, cadeado OK.
2. Confirma o **Meta Pixel** a disparar:
   - Extensão *Meta Pixel Helper*, ou
   - Gestor de Eventos do Meta → ver evento `PageView` a chegar.
3. Testa também `/postura-teste.html`.
4. Só depois de tudo OK: no Netlify, remove o domínio custom / desliga o site.

---

## Notas
- O ficheiro `CNAME` na raiz é específico do GitHub Pages e é **ignorado** pelo Cloudflare Pages — pode ficar, não atrapalha.
- `_headers` aplica segurança e cache; o Cloudflare lê no mesmo formato do Netlify.
- O ponto sensível da migração é o **email (MX)** no Passo 2.3 — é só ali que se pode partir algo. O site em si é risco zero.
