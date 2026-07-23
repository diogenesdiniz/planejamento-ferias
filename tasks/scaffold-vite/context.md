# Contexto — scaffold do projeto Vite, vazio mas buildável

## O problema

O repositório do app documenta, no `README.md`, comandos que hoje não têm o que executar: `npm install`, `npm run dev`. O workflow de deploy (`.github/workflows/deploy.yml`) já roda `npm ci` e `npm run build` a cada push em `main`, mas não existe `package.json`, `vite.config.js` nem `src/` — o pipeline falharia se disparado agora. As duas tasks já registradas antes desta (`dias-fratura`, `google-maps-fotos`) citam arquivos como `src/components/MapaDia.jsx` como se já existissem.

## Por que isto importa

Esta é a lacuna que bloqueia as outras tasks de código: sem um projeto Vite real, não há onde colocar componente nenhum. Em vez de resolver isso junto com dado, componentes e persistência numa única task grande, esta task entrega só a casca — vazia, mas com o pipeline de build e deploy comprovadamente funcionando de ponta a ponta.

## Restrição gerada por isto

O `base` do `vite.config.js` precisa bater exatamente com o nome do repositório no GitHub Pages (já registrado como ponto de atenção em `REQUIREMENTS.md §4.5`); errado, os assets carregam com caminho quebrado em produção. Isso depende de uma decisão ainda em aberto na seção 6 do `REQUIREMENTS.md`: nome e visibilidade definitivos do repositório.

## Arquivos afetados

- `package.json`, `vite.config.js`, `index.html` — novos.
- `src/main.jsx`, `src/App.jsx` — novos, só um placeholder nesta task, sem dado real do roteiro ainda.
- `.github/workflows/deploy.yml` — sem mudança de lógica, só passa a ter o que buildar de fato.

Ver `plano.md`, nesta mesma pasta, para os passos concretos.
