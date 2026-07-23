# ADR 0005 — Dois repositórios, o de planejamento como submódulo do app

## Status
Aceita.

## Contexto
Tudo que é planejamento de desenvolvimento (tasks, ADRs, agentes, skills, o `REQUIREMENTS.md`) estava misturado, no mesmo repositório, com o código e os dados que efetivamente viram o app publicado. O usuário quis que esse material ficasse fora do que sobe para o GitHub Pages.

## Decisão
Dois repositórios:

- **App** (este, o que você está lendo agora se estiver na raiz do app): código-fonte, `data/roteiro.json`, `data/lugares.json`, `plano/*.md`, o workflow de deploy.
- **Planejamento** (`planejamento/`, como submódulo git dentro do app): `REQUIREMENTS.md`, `tasks/`, `adr/`, `.claude/agents/`, `.claude/commands/`, `.claude/skills/`.

## Consequências

**Custo zero no build.** O workflow de deploy usa `actions/checkout@v4` sem `submodules: true`, então o CI nunca inicializa o submódulo, nunca baixa o conteúdo do repositório de planejamento, e o app publicado nunca o viu. Isso também significa que o repositório de planejamento pode ficar **privado** sem exigir nenhuma configuração extra de autenticação no pipeline, mesmo com o repositório do app público (necessário para o Pages gratuito).

**Descoberta do agente exige um passo explícito.** O Claude Code descobre `.claude/agents/` subindo a árvore de diretórios a partir de onde a sessão foi iniciada; um submódulo é um filho da raiz do app, não um ancestral, então não é achado automaticamente. A correção é `.claude/settings.json`, no repositório do app, com `permissions.additionalDirectories` apontando para a pasta do submódulo. Esse arquivo é versionado, então o comportamento é automático para qualquer pessoa (ou sessão futura) que abrir o app com o submódulo já inicializado. Na primeira vez que o Claude Code abrir esse repositório, ele vai pedir para confiar no workspace antes de aplicar essa configuração; é esperado, e só precisa ser aceito uma vez.

**Local, não automático.** Um `git clone` do app não traz o conteúdo do submódulo sozinho; é preciso `git submodule update --init` depois de clonar (comandos exatos em `SETUP.md`, na raiz do pacote).
