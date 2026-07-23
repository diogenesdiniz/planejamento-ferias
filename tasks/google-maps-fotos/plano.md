# Plano — Google Maps real, com fotos, sem gastar API em rotas

## 1. Google Cloud

- [ ] Habilitar **Maps JavaScript API** e **Places API (New)** no mesmo projeto.
- [ ] Restringir a chave por domínio (`https://SEU_USUARIO.github.io/*`) em Application restrictions.
- [ ] Restringir a chave, em API restrictions, às duas APIs acima e nenhuma outra (em especial, não habilitar Directions API, para não correr o risco de algum código futuro chamar sem querer).
- [ ] Configurar orçamento de alerta em Billing (sugestão: US$ 5), como rede de segurança.
- [ ] Gerar a chave, colocar em `.env.local` local e no Secret `VITE_GOOGLE_MAPS_API_KEY` do repositório no GitHub.

## 2. Dados: preencher o `placeId`

- [ ] Para cada parada em `data/roteiro.json` (as ~47 de Londres primeiro), resolver e persistir o `placeId` do Google. Pode ser feito em lote, uma vez, fora do app (script Node ou chamada manual), já que isso é dado estático que não muda.
- [ ] Guardar o `placeId` junto da parada, como mais um campo, não em arquivo separado.
- [ ] Ao montar Paris e Madrid pelo agente `planejador-viagem`, o `placeId` passa a ser resolvido no mesmo momento em que a coordenada é resolvida, não como etapa posterior.

## 3. Mapa real

- [ ] Instalar `@vis.gl/react-google-maps`.
- [ ] Substituir o SVG de `MapaDia` por um `<Map>` real, mantendo o comportamento que já existe: reenquadrar ao trocar de dia ou variante, marcador numerado por ordem de visita, cor por dia, destaque da parada do horário atual.
- [ ] Paradas fora do mapa (Warner Bros, e agora qualquer parada de outra cidade num dia de fratura) continuam grampeadas na borda, com linha pontilhada e distância real — este comportamento não muda, só o que está por baixo do desenho muda de SVG para mapa real.
- [ ] O botão "rota no Maps" e o "traçar" de cada trecho continuam exatamente como estão hoje: links `https://www.google.com/maps/dir/?api=1&...` abertos em nova aba/app, sem chamar Directions API. Nenhuma mudança de comportamento aqui, só confirmar que a migração não introduz sem querer uma chamada de rota.

## 4. Fotos

- [ ] Componente novo `FotosLocal`, que recebe o `placeId` de uma parada e busca as fotos via Places API (New).
- [ ] Exibir de 1 a 3 fotos por parada, no card expandido (o mesmo lugar onde hoje aparece a nota e o alerta).
- [ ] Atribuição do fotógrafo sempre visível junto da foto, nunca só a imagem sozinha, conforme os termos do Google.
- [ ] Cache simples em memória durante a sessão (não local storage), para não rebuscar a mesma foto se o usuário fechar e reabrir o mesmo card na mesma visita à página.
- [ ] Estado de carregamento e de ausência de foto tratados explicitamente (nem toda parada vai ter foto boa, ex.: Claremont Square é só uma praça residencial).

## 5. Validação de custo

- [ ] Depois de subir para produção, checar o painel de uso do Google Cloud depois de uma semana de testes, para confirmar que o volume real bate com a expectativa de uso pessoal e não algo rodando em loop por engano (ex.: busca de foto disparando a cada re-render).

## Fora deste plano

Rotas reais por ruas dentro do próprio mapa (Directions API) seguem fora de escopo, por decisão do usuário: toda rota é resolvida no Google Maps externo. Se isto mudar no futuro, é um novo planejamento, não uma extensão deste.
