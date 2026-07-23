# Plano — dado real e componentes extraídos

## 1. Camada de dado

- [ ] `src/data/roteiro.js`: importa `data/roteiro.json` (import estático do Vite) e expõe dias, catálogo de linhas/modos de transporte e reservas para os componentes. Nenhum dado copiado à mão do `.jsx` legado.
- [ ] Tolerar a ausência do campo `cidade` por dia/parada (ainda não existe no JSON — adicioná-lo é escopo da task `dias-fratura`) com um fallback fixo "Londres", sem quebrar nada quando o campo aparecer depois.

## 2. Componentes, extraídos do monolito legado

- [ ] `src/App.jsx`: abas de dia (cor por dia), seleção automática do dia atual quando a data cai dentro do intervalo da viagem, estado de qual painel overlay está aberto (reservas, índice por categoria).
- [ ] `src/components/DiaHeader.jsx`: dia da semana, data, título, resumo, nascer/pôr do sol.
- [ ] `src/components/MapaDia.jsx`: o SVG tal como está hoje no protótipo (projeção com correção de longitude, grampeamento de paradas fora da área, pulso animado na parada do horário atual, escala em km) — só isolado em componente próprio, sem trocar a tecnologia do mapa.
- [ ] `src/components/ListaParadas.jsx` e `CardParada.jsx`: lista ordenada, cálculo de tempo livre na parada, nota, alerta, botão de mapa (link externo). Checkbox de "visitado" existe visualmente, mas ainda não persiste nada.
- [ ] `src/components/BlocoDecisao.jsx`: variantes de sábado e segunda, com nome, recomendação e trade-off. A escolha ainda não persiste, só troca a lista de paradas exibida na sessão atual.
- [ ] `src/components/PainelReservas.jsx`: os 14 itens de `data/roteiro.json`, contador de pendentes, contagem regressiva do Sky Garden. Checkbox visual, ainda sem persistência.
- [ ] `src/components/IndicePorCategoria.jsx`: agrupamento por categoria, respeitando a variante escolhida na sessão atual.
- [ ] `src/components/ExtrasDia.jsx`: bloco recolhido de sugestões extras por dia.

## 3. Estilo

- [ ] Paleta por dia (roxo, verde, laranja, amarelo, azul, vermelho, rosa), sem repetir cor entre dias consecutivos.
- [ ] Tipografia Gill Sans, cores oficiais da TfL nos selos de linha.
- [ ] Transições respeitando `prefers-reduced-motion`.

## 4. Verificação

- [ ] `npm run dev` reproduz visualmente o roteiro inteiro de Londres, navegável por abas, com o mesmo comportamento observável do protótipo em artefato — exceto persistência, que ainda não existe.
- [ ] `npm run build` sem erro; push de teste confirma que o site publicado no Pages reflete o roteiro real, não mais o placeholder da task anterior.
- [ ] Comparar manualmente, dia por dia, contra `legacy/roteiro-londres.jsx` e `plano/plano-londres.md`, para não perder nenhuma parada, trecho ou nota na extração.

## Fora deste plano

Persistência de visitado/reservas/variante entre sessões e entre dispositivos (`estado-compartilhado`, ADR 0006). Mapa real do Google Maps e fotos (`google-maps-fotos`). Campo `cidade`/`cidadeDestino` no schema e navegação sem partição por cidade (`dias-fratura`).
