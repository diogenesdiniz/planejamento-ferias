# ADR 0003 — Rotas sempre externas, nunca calculadas dentro do app

## Status
Aceita.

## Contexto
Ao trocar o mapa desenhado em SVG por um mapa real do Google Maps, havia a opção de desenhar a rota real por ruas dentro do próprio mapa embutido, usando a Directions API.

## Decisão
Nenhuma rota é calculada dentro do app. Todo botão de "traçar" ou "rota no Maps" abre o Google Maps de verdade (app ou navegador), por um link simples que não conta como chamada de API (`https://www.google.com/maps/dir/?api=1&...`). A Directions API não é habilitada no projeto Google Cloud usado por este app.

## Consequências
Os tempos e o texto de orientação de cada trecho, no próprio app, continuam sendo estimativa escrita à mão em `roteiro.json`, não vêm de nenhuma API de rotas. Isso implica que, se a rede de transporte mudar (obra, alteração de linha), o texto do app pode ficar desatualizado até ser revisado manualmente; o app já avisa disso nos dias de fim de semana, sugerindo checar TfL Go ou Citymapper antes de descer.
