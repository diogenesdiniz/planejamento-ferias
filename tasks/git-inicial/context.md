# Contexto — primeiro commit limpo, antes do scaffold

## O problema

O repositório do app não tem nenhum commit real além da adição do submódulo `planejamento`; `git status` mostra tudo como não rastreado. Nesse estado inicial, o repositório mistura três naturezas de conteúdo diferentes:

- **Produto**: `data/roteiro.json`, `data/lugares.json`, `plano/plano-*.md` — o dado e a prosa que o app vai consumir de verdade.
- **Configuração**: `README.md`, `.gitignore`, `.gitmodules`, `.github/workflows/deploy.yml`, `.claude/settings.json`.
- **Material de referência da migração**: `legacy/roteiro-londres.jsx` (o protótipo original, um componente de 852 linhas) e `legacy/roteiro-londres-19-25-agosto.md` (o primeiro rascunho em prosa do roteiro de Londres).

O terceiro grupo existe só para ser consultado durante a extração de componentes na task `dado-e-componentes` — não é algo que o projeto precisa carregar para sempre no seu histórico de git.

## Por que isto importa

Um primeiro commit feito com `git add -A`, sem essa distinção, mistura para sempre, no histórico do repositório, o protótipo descartável com o produto real. Depois disso vira mais difícil de limpar (reescrever histórico é mais custoso do que simplesmente nunca commitar). É mais barato decidir agora, antes do primeiro commit, do que depois.

## Decisão

`legacy/` continua existindo em disco — ainda é referência ativa, necessária para a task `dado-e-componentes` — mas nunca é rastreado pelo git. Entra no `.gitignore` como qualquer artefato de build.

## Arquivos afetados

- `.gitignore` — adicionar a entrada `legacy/`.
- O primeiro commit do repositório em si, que passa a conter só o conjunto curado: `data/`, `plano/`, `README.md`, `.gitignore`, `.github/`, `.claude/settings.json`, `.gitmodules`.

Ver `plano.md`, nesta mesma pasta, para os passos concretos.
