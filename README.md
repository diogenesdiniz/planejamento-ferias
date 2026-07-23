# Planejamento — Roteiro de viagem 2026

Este é o repositório de planejamento de desenvolvimento do app de roteiro de Londres, Paris e Madrid. Ele é usado como **submódulo git** dentro do repositório do app, e não faz parte do que é publicado no GitHub Pages.

## O que vive aqui

- **`REQUIREMENTS.md`** — requisitos funcionais, o que já está implementado, o que ainda está em aberto.
- **`adr/`** — decisões arquiteturais, uma por arquivo, numeradas, no formato contexto → decisão → consequências.
- **`tasks/<slug>/`** — planejamento de funcionalidades específicas, cada pasta com `context.md` (o problema e por que importa) e `plano.md` (tarefas concretas, pequenas o bastante para virar um commit cada).
- **`.claude/agents/`** — o subagente `planejador-viagem`, que ajuda a montar e ajustar o roteiro de cada cidade em conversa.
- **`.claude/commands/`** — o atalho `/planejar <cidade>`.
- **`.claude/skills/`** — vazio por enquanto, reservado para quando algum critério do agente precisar ser reaproveitado fora dele.

## O que não vive aqui

Código do app, dados do roteiro (`roteiro.json`, `lugares.json`) e o conteúdo em prosa do roteiro (`plano/plano-<cidade>.md`) vivem no repositório do app, não aqui. Esses arquivos são produto; este repositório é processo.

## Como este repositório é usado

Ele nunca é aberto ou clonado sozinho no dia a dia. Ele é inicializado como submódulo dentro do repositório do app (ver `SETUP.md` no pacote de início de projeto), na pasta `planejamento/`. O arquivo `.claude/settings.json` do repositório do app aponta para essa pasta, para que o Claude Code descubra o agente e o comando automaticamente ao abrir o app.

Pode ficar privado no GitHub. O workflow de deploy do app não inicializa submódulos, então este conteúdo nunca é lido pelo CI nem chega ao build publicado.
