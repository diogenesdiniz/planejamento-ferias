# Plano — primeiro commit limpo

## 1. `.gitignore`

- [ ] Adicionar a entrada `legacy/`, ao lado das já existentes (`node_modules`, `dist`, `.env`, `.env.local`, `.DS_Store`, `*.log`).

## 2. Conferência antes de commitar

- [ ] Rodar `git status` e confirmar que `legacy/` não aparece mais como candidato a rastreamento.
- [ ] Rodar `git check-ignore -v legacy/roteiro-londres.jsx` e confirmar que a regra `legacy/` do `.gitignore` é a que está pegando o arquivo.
- [ ] Conferir que o que resta em `git status` é exatamente o esperado: `data/`, `plano/`, `README.md`, `.gitignore`, `.github/`, `.claude/settings.json`, `.gitmodules` — nada a mais, nada a menos.

## 3. Primeiro commit

- [ ] Adicionar os arquivos curados por nome (nunca `git add -A` ou `git add .`), para não depender de o `.gitignore` estar perfeito.
- [ ] Commit único, mensagem descrevendo que é o commit inicial do produto (dado, prosa, configuração), com o protótipo de referência deliberadamente fora do controle de versão.

## 4. Preparar para a próxima task

- [ ] Revisar se `node_modules` e `dist` já cobrem o que a task `scaffold-vite` vai precisar quando o `package.json` e o `vite.config.js` existirem — não deveria faltar nada, mas vale conferir depois que o scaffold estiver rodando.

## Fora deste plano

Nenhum código do app é escrito aqui — esta task é só higiene de repositório. O scaffold do projeto Vite é a próxima task, `tasks/scaffold-vite/`.
