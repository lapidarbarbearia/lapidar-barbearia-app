# AGENTS.md

Guia do projeto para desenvolvedores e agentes de IA.

## Visão geral

Site/app da **Lapidar Barbearia** (pt-BR, mobile-first, paleta verde/preto/branco). Página única com capa, serviços, galeria, informações e formulário de agendamento, mais uma agenda da equipe protegida por senha em `/agenda`. Agendamentos são persistidos no Netlify Database.

## Stack

TanStack Start (React 19, TanStack Router, Vite 8), Tailwind CSS 4, shadcn/ui, Netlify Database + Drizzle ORM (`drizzle-orm@beta`/`drizzle-kit@beta` — obrigatório o dist-tag beta), Netlify Image CDN.

## Estrutura

```
db/schema.ts                     # Tabela `agendamentos` (fonte da verdade do schema)
db/index.ts                      # Cliente Drizzle (drizzle-orm/netlify-db)
drizzle.config.ts                # out: netlify/database/migrations
netlify/database/migrations/     # Migrações geradas — nunca editar as já aplicadas
public/img/                      # Fotos geradas por IA (capa e galeria)
src/lib/barbearia.ts             # Serviços, preços, horários, helpers de data/fuso (cliente + servidor)
src/lib/agendamentos.functions.ts# Server functions: horários ocupados, criar, listar e cancelar
src/lib/imagens.ts               # Helper de URL do Netlify Image CDN
src/components/                  # Cabeçalho, logo, galeria, formulário de agendamento
src/routes/index.tsx             # Página inicial
src/routes/agenda.tsx            # Agenda da equipe (senha via env AGENDA_SENHA)
```

## Decisões importantes

- **Datas/horários como texto** (`AAAA-MM-DD`, `HH:MM`) no fuso `America/Sao_Paulo`; todo cálculo de "agora" passa por `agoraEmSaoPaulo()`.
- **Sem reservas duplicadas**: índice único parcial em `(data, horario)` para status diferente de `cancelado`. O servidor converte o erro `23505` em mensagem amigável.
- **Validação dupla**: o servidor revalida dia/horário com `horariosDoDia()` (domingo fechado, passado bloqueado, até 60 dias à frente).
- **Confirmação só após salvar**: o formulário exibe sucesso apenas quando `criarAgendamento` retorna `ok: true`.
- **Agenda**: autenticação simples por senha compartilhada (`AGENDA_SENHA`, comparada com `timingSafeEqual`). Server functions da agenda retornam `{ ok: false, erro }` em vez de lançar exceção. Cancelamento é lógico (`status = 'cancelado'`).
- Cores via variáveis CSS em `src/styles.css` (`--primary` verde, `--ink` preto, `--brand-glow` verde claro para fundos escuros). Fontes: Big Shoulders Display (títulos) e Manrope (texto).
- Imagens sempre via `imagem()`/`imagemSrcSet()` (Image CDN), nunca o PNG original.

## Convenções

- Textos de interface em português do Brasil; nomes de código em português quando ligados ao domínio.
- Para mudar o schema: editar `db/schema.ts` e rodar `npx drizzle-kit generate --name <nome>`. Nunca aplicar migrações manualmente.
- Serviços e preços ficam só em `src/lib/barbearia.ts`.
