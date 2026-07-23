# Contexto — o roteiro real, navegável, extraído em componentes

## O problema

O roteiro completo de Londres existe hoje em dois lugares que competem: `legacy/roteiro-londres.jsx`, um componente único de 852 linhas com o dado dos dias e das reservas hardcoded dentro do próprio JSX, e `data/roteiro.json`, a extração desse mesmo dado em formato neutro, mantida como fonte de verdade por decisão do ADR 0004. O projeto Vite, criado na task `scaffold-vite`, ainda não lê nenhum dos dois — está vazio.

## Por que isto importa

Se a migração copiar o dado do `.jsx` legado para dentro dos novos componentes, o `data/roteiro.json` deixa de ser a fonte de verdade na prática, mesmo que continue sendo por decisão. Toda edição futura do roteiro (um novo lugar, um horário ajustado) precisa acontecer só no JSON e refletir no app automaticamente — não pode exigir editar dois lugares.

## Restrição gerada por isto

O componente legado mistura, no mesmo arquivo, dado, estado (via `window.storage`) e apresentação. Esta task separa só a camada de dado e a de apresentação; a camada de estado do usuário (visitado, reservas, variante escolhida) fica deliberadamente sem funcionar nesta task — os controles existem visualmente, mas não persistem nada ainda, porque o mecanismo de persistência (Firestore, compartilhado entre os dois dispositivos) é decisão e escopo da task seguinte, `estado-compartilhado`, registrada no ADR 0006.

O mapa de cada dia continua sendo o SVG do protótipo, projeção e tudo — a troca por um mapa real do Google Maps é escopo isolado da task `google-maps-fotos`, para não misturar duas migrações independentes num único commit.

## Arquivos afetados

- `src/data/roteiro.js` — novo, carrega `data/roteiro.json`.
- `src/App.jsx` — abas de dia, seleção do dia atual por data.
- `src/components/DiaHeader.jsx`, `MapaDia.jsx`, `ListaParadas.jsx`, `CardParada.jsx`, `BlocoDecisao.jsx`, `PainelReservas.jsx`, `IndicePorCategoria.jsx`, `ExtrasDia.jsx` — novos, extraídos do monolito legado.
- `legacy/roteiro-londres.jsx` — deixa de ser executado, mas continua em disco (ignorado pelo git desde a task `git-inicial`) como referência durante a extração.

Ver `plano.md`, nesta mesma pasta, para os passos concretos.
