# Plano — dado real e componentes extraídos

## 1. Camada de dado

- [x] `src/data/roteiro.js`: importa `data/roteiro.json` (import estático do Vite) e expõe dias, catálogo de linhas/modos de transporte e reservas para os componentes. Nenhum dado copiado à mão do `.jsx` legado.
- [x] Tolerar a ausência do campo `cidade` por dia/parada (ainda não existe no JSON — adicioná-lo é escopo da task `dias-fratura`) com um fallback fixo "Londres", sem quebrar nada quando o campo aparecer depois.

## 2. Componentes, extraídos do monolito legado

- [x] `src/App.jsx`: abas de dia (cor por dia), seleção automática do dia atual quando a data cai dentro do intervalo da viagem, estado de qual painel overlay está aberto (reservas, índice por categoria).
- [x] `src/components/DiaHeader.jsx`: dia da semana, data, título, resumo, nascer/pôr do sol.
- [x] `src/components/MapaDia.jsx`: o SVG tal como está hoje no protótipo (projeção com correção de longitude, grampeamento de paradas fora da área, pulso animado na parada do horário atual, escala em km) — só isolado em componente próprio, sem trocar a tecnologia do mapa.
- [x] `src/components/ListaParadas.jsx` e `CardParada.jsx`: lista ordenada, cálculo de tempo livre na parada, nota, alerta, botão de mapa (link externo). Checkbox de "visitado" existe visualmente, mas ainda não persiste nada.
- [x] `src/components/BlocoDecisao.jsx`: variantes de sábado e segunda, com nome, recomendação e trade-off. A escolha ainda não persiste, só troca a lista de paradas exibida na sessão atual.
- [x] `src/components/PainelReservas.jsx`: os 14 itens de `data/roteiro.json`, contador de pendentes, contagem regressiva do Sky Garden. Checkbox visual, ainda sem persistência.
- [x] `src/components/IndicePorCategoria.jsx`: agrupamento por categoria, respeitando a variante escolhida na sessão atual.
- [x] `src/components/ExtrasDia.jsx`: bloco recolhido de sugestões extras por dia.

## 3. Estilo

- [x] Paleta por dia (roxo, verde, laranja, amarelo, azul, vermelho, rosa), sem repetir cor entre dias consecutivos.
- [x] Tipografia Gill Sans, cores oficiais da TfL nos selos de linha.
- [x] Transições respeitando `prefers-reduced-motion`.

## 4. Verificação

- [x] `npm run dev` reproduz visualmente o roteiro inteiro de Londres, navegável por abas, com o mesmo comportamento observável do protótipo em artefato — exceto persistência, que ainda não existe. Conferido no Chrome: mapa, lista, troca de variante (dias 4 e 6), painéis de Reservas e Índice, checkbox de visitado sincronizado com o mapa. Console sem erros.
- [x] `npm run build` sem erro.
- [ ] Push de teste confirma que o site publicado no Pages reflete o roteiro real, não mais o placeholder da task anterior. (pendente — depende do commit/push desta task)
- [x] Comparar manualmente, dia por dia, contra `legacy/roteiro-londres.jsx` e `plano/plano-londres.md`, para não perder nenhuma parada, trecho ou nota na extração. `data/roteiro.json` já era 1:1 com o legado; a extração em componentes foi conferida visualmente sem perdas.

## Fora deste plano

Persistência de visitado/reservas/variante entre sessões e entre dispositivos (`estado-compartilhado`, ADR 0006). Mapa real do Google Maps e fotos (`google-maps-fotos`). Campo `cidade`/`cidadeDestino` no schema e navegação sem partição por cidade (`dias-fratura`).
