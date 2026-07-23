# ADR 0004 — Prosa do roteiro e dado estruturado em arquivos separados

## Status
Aceita.

## Contexto
A tentação inicial era manter o roteiro inteiro num único markdown, editado em conversa, servindo tanto de leitura humana quanto de fonte para o app. Na prática, as duas coisas competem: um markdown solto o bastante para ser reescrito fluidamente em conversa não é confiável o bastante para um script consumir sem ambiguidade.

## Decisão
Duas camadas: `plano/plano-<cidade>.md`, em prosa, para leitura humana e para servir de contexto ao agente; `data/roteiro.json`, estruturado, que o app de fato consome. `data/lugares.json` guarda lugares candidatos antes de virarem parada agendada.

## Consequências
Toda edição real (paradas, horários, ordem) precisa atualizar os dois arquivos no mesmo turno. O agente `planejador-viagem` é o responsável por manter essa sincronia; nunca editar um sem o outro.
