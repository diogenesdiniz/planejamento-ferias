# Requisitos — Roteiro Londres 2026

Este documento descreve, com o máximo de precisão possível, o que já existe no protótipo (`legacy/roteiro-londres.jsx`), o que muda na migração para projeto real, o que é novo, e o que ainda depende de decisão sua. Serve como ponto de partida para o planejamento no Claude Code.

---

## 0. Decisões arquiteturais registradas

As decisões estruturais deste projeto (app único multi-cidade, timeline sem partição por cidade, rotas sempre externas, separação entre prosa e dado, divisão em dois repositórios) estão registradas como ADRs em `adr/`, não repetidas aqui. Este documento foca em requisitos funcionais e no que ainda está em aberto.

## 1. Contexto da viagem

| Item | Valor |
|---|---|
| Data de ida | 19/08/2026, pouso em Heathrow às 16h20 |
| Data de volta | 25/08/2026, Eurostar de St Pancras às 15h31 |
| Hospedagem | hub by Premier Inn London City Bank, St Swithin's Lane, EC4N 8AL |
| Viajantes | Diógenes e sua parceira |
| Dias compromissados | 21/08 09h00 Warner Bros Studio Tour · 22/08 10h00 Natural History Museum · 23/08 10h10 British Museum |
| Uso pretendido | Consulta pelo iPad e pelo celular, na rua, com internet disponível |

---

## 2. O que já está implementado no protótipo

O protótipo é um único componente React (`RoteiroLondres`), sem build, pensado para rodar como artefato isolado. Ele cobre:

### 2.1 Navegação por dia
Sete abas, uma por dia (19 a 25/08), cada uma com cor própria. A aba correspondente à data atual é selecionada automaticamente ao abrir, se a data cair dentro do intervalo da viagem.

### 2.2 Estrutura de cada dia
- Cabeçalho com dia da semana, data, título, resumo de uma ou duas frases e horário de nascer/pôr do sol.
- Mapa das paradas do dia.
- Bloco de decisão, apenas nos dias que têm mais de uma variante (sábado e segunda).
- Contador de paradas e de quantas já foram marcadas como visitadas.
- Lista ordenada de paradas, cada uma com o trecho até a próxima.
- Bloco recolhido "o que mais cabe neste dia", com sugestões extras que não entraram no roteiro principal.

### 2.3 Mapa (implementação atual: SVG, a ser substituída)
- Desenhado a partir das coordenadas reais das paradas do dia, com projeção que corrige a distorção de longitude na latitude de Londres (fator `cos(51.5°)`).
- Reprojeta e reenquadra a cada troca de dia ou de variante, com transição animada.
- Desenha o rio Tâmisa e quatro polígonos de parques (Hyde Park, Regent's Park, Richmond Park, St James's Park) como geometria simplificada, só para orientação visual — não são dados geográficos reais.
- Estações numeradas na ordem da visita, com raio maior e destaque quando selecionadas.
- Paradas fora da área de enquadramento (caso do Warner Bros Studio Tour, em Leavesden) aparecem "grampeadas" na borda do mapa, ligadas por uma linha pontilhada, com rótulo da distância real.
- Barra de escala em quilômetros, recalculada por dia.
- Parada "alternativa" (caso do Horizon 22 no último dia) é desenhada com contorno tracejado, não sólido.
- Pulso animado na estação correspondente ao horário atual, se o dia exibido for hoje.
- Clique na estação rola a lista até o card correspondente; clique no card destaca a estação.
- Botão que abre a rota completa do dia no Google Maps (link `https://www.google.com/maps/dir/?api=1&...`, sem precisar de API).

**Isto muda:** o SVG é substituído por um mapa real do Google Maps embutido (ver seção 4).

### 2.4 Cada parada (stop)
Campos por parada: horário (`h`), nome, latitude, longitude, tipo (categoria), nota de contexto, e opcionalmente: alerta prático, indicação de "fora do mapa" com rótulo, indicação de "alternativa", e o trecho até a próxima parada.

Ao abrir o card de uma parada:
- Calcula e mostra quanto tempo de fato sobra ali (diferença entre o horário da próxima parada, o horário desta e a duração do trecho de deslocamento).
- Mostra a nota completa.
- Mostra o alerta, se houver, em destaque visual (fundo âmbar).
- Botão que abre a localização exata no Google Maps.
- Marcação de "visitado", com checkbox próprio, independente da abertura do card. Fica salva localmente e sobrevive a fechar e reabrir a página.

### 2.5 Trechos entre paradas
Cada trecho tem: linha ou modo de transporte (`district`, `circle`, `central`, `northern`, `piccadilly`, `bakerloo`, `elizabeth`, `jubilee`, `dlr`, `overground`, `rail`, `barco`, `pe`), tempo estimado em minutos, e um texto de orientação de rua escrito à mão.

Visualmente: selo colorido com o nome da linha (cores oficiais da TfL), tempo, distância em linha reta (**calculada** a partir das coordenadas com fórmula de haversine, não escrita à mão), texto de orientação, e um botão "traçar" que abre aquele trecho específico no Google Maps.

Nos dias de sábado e domingo, aparece um aviso fixo sobre obras de fim de semana na rede da TfL, sugerindo checar no TfL Go ou Citymapper antes de descer.

**Isto muda:** os tempos são estimativas manuais, escritas com base em conhecimento da rede de transporte, não vêm de uma API de rotas. Ver seção 4 sobre se isso deve mudar.

### 2.6 Variantes e decisões
Sábado (Portobello × interior do Kensington Palace) e segunda (Abadia + State Rooms × dia mais folgado com Churchill War Rooms) têm duas variantes completas cada, com paradas, horários e trechos próprios e independentes.

Cada variante tem: nome, se é a recomendada, uma frase de descrição, e uma frase de "o que você perde escolhendo esta" (`tradeoff`). A escolha do usuário substitui a lista de paradas inteira daquele dia e recalcula mapa, trechos e contadores. A escolha é salva localmente por dia (chave por número do dia).

### 2.7 Reservas
Painel próprio (não é uma aba, é um overlay), com lista fixa de 14 itens (ETA do Reino Unido, Sky Garden, Buckingham State Rooms, Churchill War Rooms, Torre de Londres, Westminster Abbey, Tower Bridge Exhibition, Kensington Palace, Warner Bros, The Prince's Head, confirmar troca da guarda, Eurostar/EES, cartão por aproximação, mapa offline).

Cada item pode ser marcado como resolvido, com checkbox que persiste localmente. Alguns itens têm a marca "só se você escolher a variante X", para os itens condicionais às decisões de sábado e segunda. O item do Sky Garden tem uma data de abertura (3 de agosto de 2026) e mostra contagem regressiva em dias até lá.

O botão que abre este painel, no cabeçalho, mostra um contador de quantos itens ainda estão pendentes.

### 2.8 Índice por categoria
Segundo painel overlay, que agrupa **todas** as paradas de todos os dias por categoria (pubs, restaurantes e cafés, lojas, museus e palácios, mercados, parques e água, marcos e praças, miradouros, hotel, estações). Respeita as variantes escolhidas: se você escolheu "Kensington por dentro", o índice reflete essa lista, não a outra.

Cada item do índice mostra a bolinha da cor do dia a que pertence, nome, dia da semana, data, horário, e um botão que abre a localização no Google Maps.

### 2.9 Extras por dia
Bloco recolhido no fim de cada dia, com sugestões que não entraram no roteiro principal (por exemplo, V&A Museum e Holland Park no sábado; Sir John Soane's Museum na sexta). Cada item tem nome e uma nota de por que vale considerar.

### 2.10 Persistência
Três chaves de armazenamento local, usando a API de `window.storage` do ambiente de artefato: visitados (`londres2026:visitados`), reservas marcadas (`londres2026:reservas`) e variantes escolhidas (`londres2026:opcoes`). Tudo por dispositivo, sem sincronização entre aparelhos.

**Isto muda:** fora do ambiente de artefato, essa API não existe. Precisa virar `localStorage` do navegador padrão (ver seção 4).

### 2.11 Estilo visual
Linguagem de mapa do metrô de Londres: estações numeradas, linhas coloridas com as cores oficiais da TfL, tipografia Gill Sans (nativa em iOS e macOS, carrega sem depender de rede). Paleta por dia (roxo, verde, laranja, amarelo, azul, vermelho, rosa), sem repetir cor entre dias consecutivos. Efeitos de transição respeitam `prefers-reduced-motion`.

---

## 3. Dados completos (fonte de verdade)

Todos os dias, paradas, coordenadas, trechos, variantes, reservas e extras do protótipo estão extraídos em `data/roteiro.json`, incluso neste pacote, em formato neutro (sem JSX). Use esse arquivo como a fonte para popular `src/data/roteiro.js` na migração, em vez de copiar diretamente do `.jsx` legado.

Resumo do volume de dados:

| Dia | Data | Paradas (variante principal) | Variantes | Trechos com texto |
|---|---|---|---|---|
| 1 | Quarta 19/08 | 4 | 1 | 3 |
| 2 | Quinta 20/08 | 6 | 1 | 5 |
| 3 | Sexta 21/08 | 10 | 1 | 9 |
| 4 | Sábado 22/08 | 8 (Portobello) / 7 (Kensington) | 2 | 7 / 6 |
| 5 | Domingo 23/08 | 6 | 1 | 5 |
| 6 | Segunda 24/08 | 10 (denso) / 11 (folgado) | 2 | 9 / 10 |
| 7 | Terça 25/08 | 3 | 1 | 0 |

Total de locais distintos cobertos: 47, entre os que estão no roteiro principal, nas variantes alternativas, no índice de reservas e nos extras.

---

## 4. O que é novo nesta fase

### 4.1 Google Maps real, no lugar do SVG
**Requisito:** substituir o mapa desenhado por um `<Map>` do Google Maps JavaScript API, mantendo as mesmas informações e interações que o SVG já entrega hoje: marcadores numerados na ordem da visita, cor por dia, rota traçada entre paradas, clique no marcador sincronizado com a lista, destaque da parada do horário atual. Além disso, cada parada mostra as fotos do lugar, puxadas do Google.

O planejamento detalhado desta migração, incluindo o preenchimento do `placeId` por parada (dado que falta hoje) e a atribuição obrigatória de fotógrafo nas fotos, está em `tasks/google-maps-fotos/context.md` e `tasks/google-maps-fotos/plano.md`.

**Bibliotecas candidatas:**
- `@vis.gl/react-google-maps`, mantida pelo próprio Google, com componentes React nativos (`Map`, `AdvancedMarker`, `InfoWindow`). Recomendo esta.
- `@react-google-maps/api`, mais madura e usada, mas não é mantida pelo Google.

**APIs do Google Cloud necessárias:** Maps JavaScript API e Places API (New), ambas obrigatórias — a segunda por causa das fotos. Directions API **não é habilitada**, por decisão do usuário: toda rota é resolvida fora do app, no Google Maps externo (ver 4.2).

**Autenticação e custo:** chave de API restrita por domínio (`https://SEU_USUARIO.github.io/*`), com orçamento de alerta configurado no Google Cloud Billing. Para um site estático de uso pessoal numa semana, o consumo fica muito abaixo da cota gratuita mensal; o risco real é vazamento da chave, não custo de uso — daí a restrição por domínio ser obrigatória, não opcional.

### 4.2 Rotas: sempre externas, nunca via API
**Decisão fechada, registrada em `adr/0003-rotas-sempre-externas.md`.** Dentro do próprio site, nenhuma rota é calculada. O link "traçar", em cada trecho, e o botão "rota no Maps", no fim de cada dia, abrem o Google Maps de verdade (app instalado ou navegador), usando o link simples `https://www.google.com/maps/dir/?api=1&...`, que não conta como chamada de API. A Directions API não é habilitada no projeto. Os tempos e o texto de orientação de cada trecho continuam sendo estimativa escrita à mão, como já estão hoje em `data/roteiro.json`, e não vêm de nenhuma API de rotas.

### 4.3 Persistência sem o ambiente de artefato
**Requisito:** substituir as três chamadas de `window.storage` por `localStorage` do navegador, mantendo as mesmas três chaves e o mesmo formato JSON. Comportamento observável para o usuário não muda; muda só a implementação em `src/lib/storage.js`.

### 4.4 Integração com Git
**Requisito:** repositório versionado, com o histórico de decisões do roteiro rastreável por commit (por exemplo, quando você fechar a reserva do Buckingham, isso é estado do usuário e não deveria virar commit; mas se você adicionar uma parada nova ao roteiro, isso é dado e deveria).

Isso implica separar claramente, na arquitetura, **dado do roteiro** (vai para `data/roteiro.json`, versionado, editado por commit) de **estado do usuário** (visitado, reservas, variante escolhida — vive em `localStorage`, nunca commitado).

### 4.5 Deploy em GitHub Pages
**Requisito:** workflow de GitHub Actions que builda o projeto Vite e publica em Pages a cada push na branch principal. Incluído neste pacote como esqueleto em `.github/workflows/deploy.yml`, a ajustar durante a implementação.

Ponto de atenção específico do Vite com GitHub Pages: o `base` no `vite.config.js` precisa bater com o nome do repositório (`/londres-roteiro/`), senão os assets carregam com caminho errado em produção.

---

## 5. Paris e Madrid: agente de planejamento

### 5.1 Escopo
A viagem continua depois de Londres: 6 dias em Paris, 1 dia em Madrid. Diferente de Londres, a lista de lugares dessas duas cidades **não existe ainda**. Ela vai ser construída em conversa, dentro do Claude Code, não colada pronta de uma vez.

### 5.2 Duas camadas de arquivo, por decisão de projeto
Testamos nesta conversa, com Londres, tentar fazer um único markdown carregar tanto a prosa legível quanto o dado estruturado, e ficou claro que as duas coisas competem: um markdown solto o bastante para ser reescrito em conversa não é confiável o bastante para um script consumir sem ambiguidade. A solução adotada:

- **`plano/plano-<cidade>.md`** — prosa, para leitura humana e para servir de contexto ao agente em conversas futuras. Mesmo formato do que já existe para Londres.
- **`data/roteiro.json`** — o mesmo conteúdo, estruturado, organizado por cidade, que o app de fato consome.
- **`data/lugares.json`** — lista de lugares candidatos por cidade, cada um com status (`candidato`, `agendado`, `descartado`, `extra`) e origem (`usuario` ou `agente`). É aqui que um lugar mencionado numa conversa casual fica registrado antes de ganhar dia e horário.

Os dois primeiros são mantidos em sincronia pelo mesmo agente, no mesmo turno de edição. Nenhum deve ser editado sem o outro.

### 5.3 O subagente `planejador-viagem`
Definido em `.claude/agents/planejador-viagem.md`, com um comando de atalho em `.claude/commands/planejar.md` (`/planejar paris`, `/planejar madrid`). Ele:

- Registra em `lugares.json` qualquer lugar que você mencionar em conversa, mesmo sem saber ainda em que dia ele entra.
- Resolve sozinho categoria, coordenadas e horário de funcionamento por busca, do mesmo jeito que foi feito para Londres, só perguntando em caso de ambiguidade real.
- Sugere lugares complementares, marcados com `origem: "agente"`, sempre distinguíveis do que você pediu.
- Aplica, na hora de montar ou reajustar um dia, a mesma ordem de critérios usada em Londres: horário de funcionamento e compromissos fixos primeiro, depois proximidade geográfica, depois luz do dia, depois tempo real de deslocamento entre paradas (com trecho, linha ou modo, minutos, orientação de rua e distância calculada, não escrita à mão).
- Quando dois lugares bons não cabem juntos, monta variantes nomeadas com trade-off explícito, como foi feito no sábado e na segunda de Londres, e não decide sozinho qual escolher.
- Trata a transição entre cidades (Eurostar Londres–Paris, o deslocamento Paris–Madrid) como uma parada especial de transporte, presente no fim do plano de origem e no início do plano de destino.
- Ao final de qualquer alteração, entrega o `plano-<cidade>.md` inteiro atualizado, o trecho correspondente do `roteiro.json`, e um resumo curto do que mudou, sem repetir o roteiro inteiro na explicação.

### 5.4 Estrutura do app com múltiplas cidades

**Decisão revogada, e a nova decisão registrada em `adr/0002-timeline-unica-sem-particao-cidade.md`.** A proposta original desta seção (app único, com seletor de cidade além do seletor de dia) foi revogada depois de identificar dias de fratura: dias em que a viagem muda de cidade no meio do próprio dia, como 25/08 (manhã em Londres, chegada em Paris à noite) e o dia de deslocamento Paris–Madrid. Um seletor de cidade acima do seletor de dia obrigaria o usuário a olhar duas abas diferentes para entender um único dia da própria viagem.

**Decisão atual:** uma única sequência cronológica de abas de dia, cobrindo a viagem inteira, sem partição por cidade acima dela. Cada dia carrega a cidade como atributo (cor, rótulo), e um dia de fratura carrega duas. O detalhamento completo, incluindo o reaproveitamento do mecanismo já existente de parada "fora do mapa" para representar a cidade de chegada, está em `tasks/dias-fratura/context.md` e `tasks/dias-fratura/plano.md`.

---

## 6. Decisões em aberto (preciso da sua palavra antes de codar)

**Resolvidas nesta rodada:**
- ~~Linha reta ou rota real dentro do mapa embutido.~~ Nenhuma das duas: nenhuma rota é calculada dentro do site, tudo abre no Google Maps externo. Ver 4.2.
- ~~Tempos de trecho via API ou manual.~~ Manuais, sem API de rotas nenhuma. Ver 4.2.
- ~~Paris–Madrid de trem ou avião.~~ Avião. Ver `tasks/dias-fratura/plano.md`.
- ~~Sincronização entre dispositivos.~~ Sim: visitado, reservas marcadas e variante escolhida sincronizam entre os dois dispositivos (Diógenes e parceira) via Firestore, num documento único da viagem, sem tela de login. Ver `adr/0006-estado-compartilhado-via-firestore.md` e `tasks/estado-compartilhado/`.

**Ainda em aberto:**

1. **Nome e visibilidade do repositório.** Público, para o Pages gratuito funcionar sem plano pago. Algum problema em deixar os seus dados de viagem (hotel, roteiro) num repositório público?
2. **O painel de reservas deveria virar algo mais que checklist?** Por exemplo, campo de link ou código de confirmação por item. Hoje é só nome, detalhe e checkbox.
3. **Vale um modo "somente hoje"**, que abre direto no dia certo e esconde as abas dos outros dias, pensando em uso na rua onde você não vai querer navegar entre dias?
4. **Outros pontos que você mencionou querer discutir** e ainda não detalhou — deixo este item aberto para a nossa próxima conversa no Claude Code.

---

## 7. Fora de escopo (combinado)

- Geolocalização ao vivo do usuário.
- Login ou múltiplos perfis de usuário visíveis (a sincronização de estado entre dispositivos, seção 6, usa autenticação anônima nos bastidores, sem tela de login).
- Dados de clima ou status de linha em tempo real.
