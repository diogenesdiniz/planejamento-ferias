# Plano — dias de fratura entre cidades

Tarefas concretas, para consumo direto no Claude Code. Cada uma é pequena o bastante para ser um commit.

## 1. Modelo de dados

- [ ] Em `data/roteiro.json`, adicionar ao objeto de cada dia: `cidade` (string, a cidade principal, que define o pano de fundo do mapa) e `cidadeDestino` (string ou null, presente só em dias de fratura).
- [ ] Adicionar ao objeto de cada parada um campo `cidade` (string). Se ausente, herda a `cidade` do dia.
- [ ] Adicionar ao catálogo de linhas/modos de transporte (`LINHA` no protótipo) as entradas `eurostar` (cor da identidade visual do Eurostar) e `voo` (para qualquer trecho aéreo, caso Paris–Madrid seja de avião), do mesmo jeito que já existem `district`, `dlr`, `rail` etc.
- [x] Confirmar em conversa com o usuário se Paris–Madrid será de trem ou avião, antes de fixar o ícone e o texto padrão desse trecho. **Resolvido: avião.** O trecho usa o modo `voo`, com o mesmo tratamento visual de "fora do mapa" nos dois lados: o aeroporto de origem e o de chegada ficam grampeados na borda de seus respectivos mapas locais, ligados por linha pontilhada, já que a distância não cabe em nenhuma das duas projeções locais.

## 2. Regra de renderização do mapa

- [ ] Mudar a condição que hoje decide `foraDoMapa` (hoje uma flag manual, usada só no Warner Bros) para: `foraDoMapa = flag manual OU parada.cidade !== dia.cidade`. Isto cobre automaticamente qualquer parada da cidade de chegada num dia de fratura, sem precisar marcar cada uma à mão.
- [ ] Confirmar visualmente que o rótulo de distância (`rotuloFora`) faz sentido para cidade inteira (ex.: "Paris, 340 km a sudeste") e não só para um ponto único como o estúdio da Warner.
- [ ] Ajustar o pano de fundo do mapa (rio, parques) para ser função da `cidade` do dia, não fixo em Londres. Preparar os polígonos de apoio de Paris (Sena) e Madrid antes de ligar o dia de fratura Paris–Madrid.

## 3. Navegação

- [ ] Remover a camada de seletor de cidade acima do seletor de dia (revoga a decisão registrada antes na seção 5.4 do `REQUIREMENTS.md`).
- [ ] Único carrossel de abas, cronológico, cobrindo a viagem inteira. Cor da aba vem da `cidade` do dia; dia de fratura usa um indicador de duas cores (ex.: metade esquerda na cor da cidade de origem, metade direita na cor da cidade de destino) ou um pequeno rótulo "→ Paris à noite" dentro da aba.
- [ ] Garantir que o app ainda "abre no dia de hoje" corretamente com a lista de abas estendida a Paris e Madrid.

## 4. Conteúdo e convenção de arquivo

- [ ] Definir a convenção: um dia de fratura é escrito por inteiro em `plano-<cidade-de-origem>.md`, como o último dia daquela cidade. `plano-<cidade-de-destino>.md` apenas referencia esse dia no início ("dia de chegada, ver plano-londres.md"), sem duplicar horários.
- [ ] Atualizar `plano-londres.md`: o dia 25/08 passa a incluir, no fim, a parada de chegada em Paris (hospedagem, primeira impressão da noite), como parada com `cidade: "paris"`, uma vez que a hospedagem de Paris for definida.
- [ ] Mesma convenção aplicada ao dia Paris–Madrid quando as datas forem fechadas.

## 5. Agente `planejador-viagem`

- [ ] Adicionar instrução explícita: ao montar o primeiro dia de uma cidade nova que emenda numa cidade anterior, o agente deve propor esse dia como fratura da cidade anterior, não como dia 1 isolado da cidade nova — a menos que o usuário already tenha decidido não viajar no mesmo dia da chegada.
- [ ] O agente deve perguntar, apenas quando genuinamente ambíguo, se a chegada à noite justifica qualquer parada agendada naquela noite ou se o dia de fratura termina só no check-in.

## 6. Validação

- [ ] Ao ter a hospedagem de Paris definida, montar o dia 25/08 completo como teste de ponta a ponta desta funcionalidade, antes de generalizar para Paris–Madrid.
- [ ] Conferir que o índice por categoria (painel existente) continua agrupando corretamente lugares de cidades diferentes sem confundir datas.

## Fora deste plano

Rotas reais dentro do mapa (linha reta × Directions API) e a integração de fato do Google Maps continuam como itens separados, já registrados em `REQUIREMENTS.md`, seção 4. Este plano cobre só a fratura entre cidades.
