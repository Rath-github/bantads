# Bantads

> Sistema bancário acadêmico desenvolvido para o curso de Tecnologia em Análise e Desenvolvimento de Sistemas (TADS).

[![Angular](https://img.shields.io/badge/Angular-17-DD0031?logo=angular&logoColor=white)](https://angular.dev)
[![AnalogJS](https://img.shields.io/badge/Analog-0.2-1A1A1A)](https://analogjs.org)
[![Vite](https://img.shields.io/badge/Vite-5-646CFF?logo=vite&logoColor=white)](https://vitejs.dev)
[![TailwindCSS](https://img.shields.io/badge/Tailwind-3.3-06B6D4?logo=tailwindcss&logoColor=white)](https://tailwindcss.com)
[![pnpm](https://img.shields.io/badge/pnpm-8+-F69220?logo=pnpm&logoColor=white)](https://pnpm.io)

Repositório monorepo em estágio inicial — contém o `frontend` como submódulo Git, placeholder de banco de dados e `docker-compose` para ambiente de desenvolvimento.

## Sumário

- [Sobre](#sobre)
- [Tecnologias](#tecnologias)
- [Estrutura do Repositório](#estrutura-do-repositório)
- [Pré-requisitos](#pré-requisitos)
- [Como Começar](#como-começar)
- [Scripts](#scripts)
- [Banco de Dados](#banco-de-dados)
- [Docker](#docker)
- [VS Code](#vs-code)
- [Padrões e Regras](#padrões-e-regras)
- [Branches e Contribuição](#branches-e-contribuição)
- [Membros](#membros)
- [Licença](#licença)

## Sobre

O **Bantads** é um projeto acadêmico com objetivo de simular operações de um sistema bancário. O repositório atual funciona como orquestrador do projeto:

* **Frontend** isolado em submódulo (`bantads-frontend`) — permite versionamento independente e integração contínua separada.
* **Banco de dados** via `database/init.sql` (a ser definido).
* **Infra local** via `docker-compose-dev.yml` (a ser definido).

> **Status:** scaffold inicial. `database/init.sql`, `docker-compose-dev.yml` e `.github/regras.md` estão vazios e serão preenchidos ao longo do desenvolvimento.

## Tecnologias

| Camada | Tecnologia | Versão | Descrição |
|---|---|---|---|
| Framework | [AnalogJS](https://analogjs.org) | `^0.2.0` | Meta-framework fullstack para Angular (file-based routing, Vite, SSR) |
| UI | [Angular](https://angular.dev) | `^17.2.0` | Standalone Components, Router |
| Build | [Vite](https://vitejs.dev) + [Nx](https://nx.dev) | `~5.0.0` / `~18.0.0` | Dev server HMR, build otimizado |
| Estilo | [Tailwind CSS](https://tailwindcss.com) | `^3.3.1` | Utility-first + `postcss` / `autoprefixer` |
| Testes | [Vitest](https://vitest.dev) + `jsdom` | `~1.2.0` | `globals: true`, `setupFiles: src/test.ts` |
| Conteúdo | `@analogjs/content`, `marked`, `mermaid`, `prismjs` | — | Renderização de markdown/diagramas |
| Pacotes | [pnpm](https://pnpm.io) | `8+` | Gerenciador (há `pnpm-lock.yaml` no submodule) |
| Infra | Docker / Docker Compose | — | Ambiente de desenvolvimento (WIP) |

Requisitos do `package.json` do frontend: `Node >= 18.13.0`.

## Estrutura do Repositório

```
bantads/
├── bantads-frontend/        # SUBMÓDULO Git -> Analog + Angular 17 (porta 5173)
│   ├── src/
│   │   ├── app/
│   │   │   ├── app.component.ts
│   │   │   ├── app.config.ts / app.config.server.ts
│   │   │   └── pages/index.page.ts   # file-based routing ("/" -> index.page.ts)
│   │   ├── server/routes/v1/hello.ts # API route Analog (SSR)
│   │   ├── main.ts / main.server.ts
│   │   └── styles.css                # Tailwind entry
│   ├── angular.json         # builder @analogjs/platform:vite
│   ├── vite.config.ts       # plugins: [analog()], test: vitest/jsdom
│   ├── tailwind.config.js / postcss.config.js
│   ├── index.html
│   └── package.json
├── database/
│   └── init.sql             # placeholder - script de inicialização do BD
├── docker-compose-dev.yml   # placeholder - compose para ambiente dev
├── .github/
│   └── regras.md            # placeholder - regras de contribuição
├── .vscode/
│   ├── extensions.json      # recomenda angular.ng-template
│   ├── launch.json          # ng serve (chrome) + ng test
│   └── tasks.json           # npm:start / npm:test (background)
└── README.md
```

> `bantads-frontend` é registrado como `160000 commit` (gitlink) em `HEAD`. Localmente o diretório aparece vazio até inicializar o submódulo.

## Pré-requisitos

* **Git** `>= 2.13` (suporte a `--recurse-submodules`)
* **Node.js** `>= 18.13.0` (ver `package.json#engines`)
* **pnpm** `>= 8` — instale com `npm i -g pnpm` ou `corepack enable`
* **Docker** + **Docker Compose** (opcional, para quando `docker-compose-dev.yml` for preenchido)
* **VS Code** recomendado (para `launch.json`/`tasks.json`)

Verifique:

```bash
node -v   # >= v18.13.0
pnpm -v
git --version
docker --version
```

## Como Começar

### 1. Clone

**Clone novo (recomendado):**

```bash
git clone --recurse-submodules https://github.com/Rath-github/bantads.git
cd bantads
```

**Já clonou e `bantads-frontend/` está vazio?**

```bash
git submodule update --init --recursive
```

Verificação:

```bash
git submodule status
# deve exibir algo como: 9a869c5... bantads-frontend (heads/...)
ls bantads-frontend  # deve listar package.json, src/, vite.config.ts etc.
```

### 2. Frontend

```bash
cd bantads-frontend
pnpm install        # ou npm install / yarn, mas prefira pnpm (há pnpm-lock.yaml)

# desenvolvimento (HMR)
pnpm dev            # alias para pnpm start -> ng serve
# ou
pnpm start

# abrir http://localhost:5173
```

> `angular.json` define `serve.port: 5173` e `vite.config.ts` usa `publicDir: src/assets`. O `launch.json` espera `http://localhost:5173/` para debug com Chrome.

### 3. Atualizar submódulo após pull

Quando alguém atualiza o ponteiro do frontend em `main`:

```bash
git pull
git submodule update --remote --merge
# ou
git submodule update --init --recursive
```

## Scripts

Dentro de `bantads-frontend/` (`package.json#scripts`):

| Comando | Descrição |
|---|---|
| `pnpm dev` / `pnpm start` | `ng serve` — dev server Vite + Analog (HMR) em `http://localhost:5173` |
| `pnpm build` | `ng build` — build cliente (`dist/client`) + SSR |
| `pnpm watch` | `ng build --watch` |
| `pnpm test` | `ng test` — Vitest (`jsdom`, `globals`, `src/test.ts`) |
| `pnpm ng` | Acesso direto ao Angular CLI |

Build de produção:

```bash
pnpm build
# cliente: dist/client  (ou dist/analog/public conforme versão Analog)
# server:  dist/analog/server
```

Testes:

```bash
pnpm test        # watch mode
pnpm test -- --run  # run único
```

## Banco de Dados

`database/init.sql` está reservado para o script de inicialização do banco (DDL + seed).

Uso esperado com Docker (quando `docker-compose-dev.yml` for configurado):

```yaml
# exemplo futuro
# volumes:
#   - ./database/init.sql:/docker-entrypoint-initdb.d/init.sql
```

Por enquanto o arquivo está vazio — **WIP**. Ao definir o SGBD (Postgres/MySQL), documente aqui porta, credenciais de dev e como rodar migrations.

## Docker

`docker-compose-dev.yml` está vazio (WIP). Uso previsto:

```bash
docker compose -f docker-compose-dev.yml up -d
docker compose -f docker-compose-dev.yml logs -f
docker compose -f docker-compose-dev.yml down
```

Quando preenchido, deve orquestrar DB + (opcionalmente) frontend. Mantenha variáveis sensíveis em `.env` (não versionado).

## VS Code

O repositório já traz configuração pronta:

* **Extensão recomendada** (`extensions.json`): `angular.ng-template` — syntax highlight e language service para Angular.
* **Tasks** (`tasks.json`): `npm: start` e `npm: test` como background tasks com `problemMatcher: $tsc` (espera `bundle generation complete`).
* **Debug** (`launch.json`):
  * `ng serve` — lança Chrome em `http://localhost:5173/` com `preLaunchTask: npm: start`
  * `ng test` — lança Chrome com `preLaunchTask: npm: test`

Pressione `F5` e selecione a configuração desejada. Certifique-se de ter `pnpm`/`npm` e dependências instaladas antes.

## Padrões e Regras

* `.github/regras.md` — placeholder para convenções de branch, commits (ex.: Conventional Commits), PR e code review. Defina antes do desenvolvimento em equipe.
* `.editorconfig` e `.vscode/settings.json` (quando existir) — padronização de formatação.
* `angular.json` usa builder `@analogjs/platform:vite` — evite trocar para `@angular-devkit/build-angular` sem alinhar com o time, pois quebra file-based routing e SSR do Analog.

Sugestão de fluxo (a confirmar em `regras.md`):

* `main` — branch estável
* `Thiago` / `Igor` — branches de desenvolvimento ativas (visíveis em `origin/`)
* Feature branches: `feat/<nome>` a partir de `main`, PR para `main`

## Branches e Contribuição

```bash
# criar feature
git checkout main
git pull --recurse-submodules
git checkout -b feat/minha-feature

# ... commits ...

git push -u origin feat/minha-feature
# abrir PR no GitHub para main
```

Ao alterar o frontend, lembre-se que ele é um submódulo:

```bash
cd bantads-frontend
git checkout -b feat/ui-x
# ... commits no submodule ...
git push

cd ..
git add bantads-frontend  # atualiza o ponteiro do submódulo
git commit -m "chore: bump bantads-frontend"
git push
```

## Troubleshooting

| Problema | Solução |
|---|---|
| `bantads-frontend` vazio após clone | `git submodule update --init --recursive` |
| `pnpm: command not found` | `npm i -g pnpm` ou `corepack enable && corepack prepare pnpm@latest --activate` |
| `Port 5173 in use` | `lsof -i :5173` / `netstat -ano | findstr 5173` e mate o processo, ou mude `port` em `angular.json` |
| `ERRO Node version` | Atualize para `>=18.13.0` (`nvm use 18` / `nvm install 18`) |
| Submódulo em detached HEAD | `cd bantads-frontend && git checkout main && git pull` |

## Membros

- Igor
- Lucas
- Matheus
- Renan
- Thiago
- Vitor

## Licença

Não definida. Adicione uma licença (ex.: MIT) se o projeto for público, ou mantenha privado para uso acadêmico.

---

> Dúvidas sobre o frontend Analog? Veja [analogjs.org](https://analogjs.org), [Angular docs](https://angular.dev) e o `README.md` original do template Analog dentro de `bantads-frontend` após inicializar o submódulo.
