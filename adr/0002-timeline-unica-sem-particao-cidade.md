# ADR 0002 — Linha do tempo única de dias, sem seletor de cidade acima do dia

## Status
Aceita. Substitui uma decisão anterior tomada no mesmo planejamento (seletor de cidade como camada acima do seletor de dia), revogada antes de chegar a ser implementada.

## Contexto
Existem dias em que a viagem muda de cidade no meio do próprio dia: a última manhã em Londres termina com a chegada a Paris à noite, e o mesmo se repete entre Paris e Madrid. Um seletor de cidade como camada superior ao seletor de dia obrigaria o usuário a olhar duas abas diferentes para entender um único dia da própria viagem.

## Decisão
Uma única sequência cronológica de abas de dia, cobrindo a viagem inteira. Cada dia carrega a cidade como atributo (cor, rótulo), não como container. Um dia de fratura carrega duas cidades.

## Consequências
O mapa de cada dia precisa de uma regra que trate paradas de uma cidade diferente da cidade principal do dia como "fora da área local", reaproveitando o mecanismo já existente para o Warner Bros Studio Tour (parada distante, grampeada na borda do mapa, com distância real e linha pontilhada), em vez de inventar um segundo tipo de mapa para dias de fratura. Detalhamento em `tasks/dias-fratura/`.
