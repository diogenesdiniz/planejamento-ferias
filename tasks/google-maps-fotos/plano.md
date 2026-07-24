# Plano — Google Maps real, com fotos, sem gastar API em rotas

## 1. Google Cloud

- [x] Habilitar **Maps JavaScript API** e **Places API (New)** no mesmo projeto. Já estava feito antes desta task; confirmado testando a chave em `.env.local` diretamente contra as duas APIs.
- [x] Restringir a chave por domínio (`https://SEU_USUARIO.github.io/*`) em Application restrictions. Confirmado: chamada sem `Referer` é bloqueada (`API_KEY_HTTP_REFERRER_BLOCKED`); com `Referer` de `github.io` ou `localhost`, funciona.
- [x] Restringir a chave, em API restrictions, às duas APIs acima e nenhuma outra (em especial, não habilitar Directions API, para não correr o risco de algum código futuro chamar sem querer). Confirmado: a Directions API rejeita a chave (`"API keys with referer restrictions cannot be used with this API"`) — estruturalmente impossível de usar essa chave para calcular rota.
- [ ] Configurar orçamento de alerta em Billing (sugestão: US$ 5), como rede de segurança. Não verificável por mim (não tenho acesso ao Billing do Cloud Console) — usuário deve confirmar que isso está configurado.
- [x] Gerar a chave, colocar em `.env.local` local e no Secret `VITE_GOOGLE_MAPS_API_KEY` do repositório no GitHub. `.env.local` já tinha a chave. Secret do GitHub não verificado por mim (sem acesso à API de Actions Secrets) — usuário deve confirmar que existe, senão o build de produção não vai ter mapa.

## 2. Dados: preencher o `placeId`

- [x] Para cada parada em `data/roteiro.json` (as ~47 de Londres primeiro), resolver e persistir o `placeId` do Google. Pode ser feito em lote, uma vez, fora do app (script Node ou chamada manual), já que isso é dado estático que não muda. Feito via script Node de uma vez só: 50 paradas únicas, 49 resolvidas automaticamente por nome+coordenada, 1 (The Serpentine) resolvida manualmente com busca mais específica.
- [x] Guardar o `placeId` junto da parada, como mais um campo, não em arquivo separado. Todas as 67 ocorrências (contando variantes) em `data/roteiro.json` têm `placeId` agora.
- [ ] Ao montar Paris e Madrid pelo agente `planejador-viagem`, o `placeId` passa a ser resolvido no mesmo momento em que a coordenada é resolvida, não como etapa posterior. Ainda não aplicável (Paris/Madrid sem lugares curados); reforçar essa instrução no agente quando essa curadoria começar.

## 3. Mapa real

- [x] Instalar `@vis.gl/react-google-maps`.
- [x] Substituir o SVG de `MapaDia` por um `<Map>` real, mantendo o comportamento que já existe: reenquadrar ao trocar de dia ou variante (via `fitBounds` num efeito imperativo), marcador numerado por ordem de visita (`AdvancedMarker` com conteúdo React customizado), cor por dia, destaque da parada do horário atual (pulso adaptado de SVG para CSS/div). Testado em vários dias no navegador, sem regressão.
- [x] Paradas fora do mapa (Warner Bros, e agora qualquer parada de outra cidade num dia de fratura) — comportamento adaptado, não idêntico: em vez de grampear a uma posição de pixel fixa (só possível no SVG estático), a parada fica na posição geográfica real (fora do enquadramento inicial, alcançável dando zoom out/pan, como um mapa de verdade permite) e a linha pontilhada conecta as coordenadas reais via `Polyline` com ícone repetido. Testado com Warner Bros (zoom out revela o marcador) e com a chegada em Paris (dia 25/08, linha pontilhada visível saindo da tela). Rótulo de distância/direção (`rotuloFora`) continua idêntico na barra inferior.
- [x] O botão "rota no Maps" e o "traçar" de cada trecho continuam exatamente como estão hoje: links `https://www.google.com/maps/dir/?api=1&...` abertos em nova aba/app, sem chamar Directions API. Nenhuma mudança de comportamento aqui — código não foi tocado, só a camada visual do mapa.

## 4. Fotos

- [x] Componente novo `FotosLocal`, que recebe o `placeId` de uma parada e busca as fotos via Places API (New).
- [x] Exibir de 1 a 3 fotos por parada, no card expandido (o mesmo lugar onde hoje aparece a nota e o alerta). Testado com Leadenhall Market (3 fotos reais carregadas).
- [x] Atribuição do fotógrafo sempre visível junto da foto, nunca só a imagem sozinha, conforme os termos do Google. Nome do autor como link para o perfil, abaixo de cada foto.
- [x] Cache simples em memória durante a sessão (não local storage), para não rebuscar a mesma foto se o usuário fechar e reabrir o mesmo card na mesma visita à página. `Map` em escopo de módulo, por `placeId`.
- [x] Estado de carregamento e de ausência de foto tratados explicitamente (nem toda parada vai ter foto boa, ex.: Claremont Square é só uma praça residencial). Testado: Claremont Square não mostra nada (nem placeholder vazio), exatamente o caso previsto.

**Pegadinha encontrada em produção:** as fotos carregavam em `localhost` mas não em `diogenesdiniz.github.io`. Causa: a chave é restrita por referer com o path completo (`.../roteiro-ferias-2026/*`), mas o navegador manda só a origem (sem path) em requisições cross-origin como `fetch`/`<img>` para `places.googleapis.com` — a Maps JavaScript API aceitou esse referer mais curto, a Places API (New) não. Corrigido com `referrerPolicy: 'unsafe-url'` no `fetch` e no atributo `referrerPolicy` do `<img>`, em `FotosLocal.jsx`, forçando o navegador a mandar a URL completa da página. Nenhuma mudança foi feita na restrição da chave no Google Cloud. Se o domínio de produção mudar algum dia, isto continua funcionando sem ajuste, já que não depende do valor exato do path.

## 5. Validação de custo

- [ ] Depois de subir para produção, checar o painel de uso do Google Cloud depois de uma semana de testes, para confirmar que o volume real bate com a expectativa de uso pessoal e não algo rodando em loop por engano (ex.: busca de foto disparando a cada re-render). Pendente — depende de uma semana de uso real após o deploy; usuário deve checar o Cloud Console.

## Fora deste plano

Rotas reais por ruas dentro do próprio mapa (Directions API) seguem fora de escopo, por decisão do usuário: toda rota é resolvida no Google Maps externo. Se isto mudar no futuro, é um novo planejamento, não uma extensão deste.
