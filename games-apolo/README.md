# Games Apolo — subir no GitHub Pages (passo a passo)

## O que tem neste pacote

```
games-apolo/
├── index.html          ← Portal (os 3 cartuchos já ativos)
├── tinywings/index.html   ← 1º jogo  (Tiny Wings Pixel Legends)
├── tinybomb/index.html    ← 2º jogo  (Tiny Bomb)
├── zabble/index.html      ← 3º jogo  (Tiny Zabble Pixel Pop)  ★ NOVO
├── .nojekyll              ← (arquivo oculto) evita processamento do Jekyll
└── firebase/              ← regras prontas para copiar no Firebase (item 6)
```

Tudo em minúsculas e sem espaços de propósito: o GitHub Pages diferencia maiúsculas de minúsculas,
e a pasta antiga `Tiny Wings Pixel Legends` foi renomeada para `tinywings`
(o portal já aponta para os nomes novos).

## 1) Criar o repositório

1. Entre em https://github.com (crie a conta se ainda não tiver).
2. Botão **+** (canto superior direito) → **New repository**.
3. Nome: `games-apolo`  *(dica: se chamar `SEUUSUARIO.github.io`, o endereço fica só `https://SEUUSUARIO.github.io/`)*.
4. Marque **Public** → **Create repository**.

## 2) Subir os arquivos

**Jeito mais fácil (pelo navegador):**
1. Descompacte o zip. No repositório novo clique em **uploading an existing file** (ou **Add file → Upload files**).
2. Abra a pasta `games-apolo` descompactada, selecione **tudo que está dentro dela** (o `index.html` e as pastas `tinywings`, `tinybomb`, `zabble`, `firebase`) e **arraste para a página**. Espere terminar o envio.
3. Clique em **Commit changes**.

> O `.nojekyll` é um arquivo oculto e talvez não apareça no seu explorador. Sem problema: o site funciona sem ele.
> Se quiser, crie depois em **Add file → Create new file**, com o nome `.nojekyll`.

**Jeito Git (opcional):**
```bash
cd games-apolo
git init -b main
git add .
git commit -m "Games Apolo"
git remote add origin https://github.com/SEUUSUARIO/games-apolo.git
git push -u origin main
```

## 3) Ligar o GitHub Pages

1. No repositório: **Settings → Pages**.
2. Em **Build and deployment → Source**, escolha **Deploy from a branch**.
3. **Branch:** `main`  e pasta **/ (root)** → **Save**.
4. Espere 1–3 minutos e atualize a página. Vai aparecer: *Your site is live at* `https://SEUUSUARIO.github.io/games-apolo/`.

## 4) Testar

- Abra o link e jogue os 3 cartuchos (o 3º é o Zabble).
- Zabble 2P: abra em **dois celulares**, um cria a sala e o outro entra pelo código ou pela lista.
- Zabble 1P: envie uma pontuação e confira o RANKING.

## 5) Se algo der errado

| Sintoma | O que fazer |
|---|---|
| Aparece "ATENÇÃO / pasta" ao clicar no jogo | A pasta/arquivo não está no lugar certo. Confira os nomes (minúsculas) e se cada pasta tem um `index.html`. |
| Página 404 | Aguarde mais 2 min; confira **Settings → Pages**. |
| Mudou um arquivo e nada mudou | Cache: aperte Ctrl+F5 (ou limpe os dados do navegador no celular). |
| Ranking do Zabble mostra "SEM CONEXÃO: RANKING LOCAL" | Falta a regra do Firestore (item 6a). |
| Salas 2P do Zabble não criam/aparecem | Falta a regra do Realtime Database (item 6b). |

## 6) Firebase (necessário para o Zabble funcionar online)

**a) Ranking — Firestore** (projeto `gamesapolo-d52b5`)
Firebase Console → **Firestore Database → Regras** → cole o bloco de `firebase/firestore-regras-zabble.txt`
**dentro** de `match /databases/{database}/documents { ... }`, sem apagar o bloco que já existe para `scores` → **Publicar**.

**b) Salas 2P — Realtime Database** (projeto `tiny-bomb-pixel-heroes`)
**Realtime Database → Regras** → adicione o nó `bb` de `firebase/database-regras-zabble.txt`
dentro de `"rules"`, mantendo o `"rooms"` (do Tiny Bomb) como está → **Publicar**.
*(Se as regras estiverem em "modo de teste", elas expiram depois de 30 dias — vale conferir.)*

**c) Domínio autorizado (Tiny Bomb usa login)**
**Authentication → Settings → Authorized domains → Add domain** → `SEUUSUARIO.github.io`.

**d) Proteger as chaves de API (recomendado)**
Google Cloud Console → **APIs e serviços → Credenciais** → abra a chave de navegador de cada projeto →
**Restrições de aplicativo: Sites (referenciadores HTTP)** → adicione
`https://SEUUSUARIO.github.io/*` (e `http://localhost/*` se for testar no computador).
Só faça isso **depois** que o site no GitHub estiver funcionando, e mantenha o endereço do Netlify na lista até desligá-lo.

## 7) Desligar o Netlify

Depois de confirmar que tudo funciona no GitHub Pages: Netlify → seu site → **Site configuration → Danger zone → Delete site**.
(Os ganchos que escondiam o selo do Netlify já foram removidos do portal.)

## 8) Domínio próprio (opcional)

**Settings → Pages → Custom domain** e aponte o DNS conforme o GitHub mostrar. Depois marque **Enforce HTTPS**.
Se usar domínio próprio, adicione-o também nos itens 6c e 6d.

## 9) Atualizar um jogo depois

Abra o arquivo no GitHub (ex.: `zabble/index.html`) → ícone do lápis (**Edit**) ou **Add file → Upload files** com o mesmo nome → **Commit changes**. Em 1–2 minutos o site atualiza.

## Sobre "esconder o código-fonte"

Não existe como esconder de verdade um jogo que roda no navegador: o código precisa chegar ao celular para rodar.
O que protege o seu projeto é: regras do Firebase restritivas (item 6), chaves restritas por domínio (6d) e,
para trapaça no ranking, validação no servidor (Cloud Functions). Se quiser, dá para **ofuscar/minificar** os jogos
antes de publicar para dificultar cópia — isso é uma etapa extra opcional.
