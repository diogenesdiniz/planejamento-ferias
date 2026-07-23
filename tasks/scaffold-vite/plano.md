# Plano — scaffold do projeto Vite

## 1. Decisão prévia

- [ ] Confirmar com o usuário o nome definitivo do repositório GitHub (decisão em aberto #1 de `REQUIREMENTS.md §6`), antes de fixar o `base` do Vite — sem isso, o passo 3 fica provisório.

## 2. Projeto

- [ ] `npm create vite@latest` com template React, na raiz do repositório do app (sem sobrescrever `data/`, `plano/`, `planejamento/`).
- [ ] `vite.config.js` com `base: '/<nome-do-repo>/'`.
- [ ] `package.json` com os scripts padrão do Vite (`dev`, `build`, `preview`).

## 3. Placeholder mínimo

- [ ] `index.html`, `src/main.jsx`, `src/App.jsx` — um placeholder simples (ex.: título da viagem e uma frase), sem nenhum dado real do roteiro ainda. Isso é escopo da próxima task.
- [ ] Confirmar que a fonte Gill Sans carrega sem depender de rede, como já era no protótipo (REQUIREMENTS §2.11), mesmo que o estilo completo só venha na próxima task.

## 4. Verificação local

- [ ] `npm install` e `npm run dev` funcionam, mostrando o placeholder.
- [ ] `npm run build` gera `dist/` sem erro.

## 5. Verificação em produção

- [ ] Push de teste em `main` dispara `.github/workflows/deploy.yml`.
- [ ] Confirmar no Actions que o build passou e que o Pages publicou.
- [ ] Abrir a URL publicada e confirmar que os assets carregam (o teste real de que o `base` está correto) — tela em branco ou 404 de asset indica `base` errado.

## Fora deste plano

Qualquer dado real do roteiro, qualquer componente de UI além do placeholder, qualquer persistência. Essas três coisas são as próximas duas tasks (`dado-e-componentes`, `estado-compartilhado`).
