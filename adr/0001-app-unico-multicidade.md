# ADR 0001 — Um app único cobrindo Londres, Paris e Madrid

## Status
Aceita.

## Contexto
A viagem cobre três cidades em sequência. Um site por cidade seria mais simples de navegar em isolamento, mas triplicaria o deploy e obrigaria o usuário a saber, de antemão, qual site abrir.

## Decisão
Um único app, um único deploy, com todas as cidades representadas nele.

## Consequências
A estrutura de dados (`roteiro.json`) precisa suportar múltiplas cidades desde o início, com a cidade como atributo de cada dia, não como particionamento estrutural do arquivo. Isso é o que, mais tarde, tornou possível resolver dias de fratura entre cidades sem redesenho (ver ADR 0002).
