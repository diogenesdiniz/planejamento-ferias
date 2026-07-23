# Contexto — dias de fratura entre cidades

## O problema

A viagem tem pelo menos duas transições de cidade dentro de um único dia corrido:

- **25/08**: manhã e início da tarde ainda em Londres (última manhã, Eurostar às 15h31 de St Pancras), chegada em Paris à noite.
- **Paris → Madrid**, data ainda a definir: mesma lógica, um dia que começa numa cidade e termina em outra.

A estrutura de dados e de navegação até aqui trata cada dia como pertencente inteiramente a uma cidade, e cogitava um seletor de cidade como camada acima do seletor de dia. Isso quebra exatamente nesses dois dias: forçaria o usuário a olhar dois lugares diferentes do app (aba de Londres, aba de Paris) para entender um único dia da própria vida, o que é o oposto de "consultar na rua".

## Por que isto importa

O pedido original do usuário para este projeto era justamente reduzir a distância entre "o que está na minha cabeça sobre o dia" e "o que o app mostra". Um dia de viagem não pausa na fronteira entre países; a experiência do usuário também não deveria.

## Restrição gerada por isto

O mapa de cada dia é desenhado com uma projeção local (centrada nas coordenadas daquele dia) e com pano de fundo geográfico específico da cidade (o Tâmisa, os parques de Londres). Se um dia de fratura tiver paradas em duas cidades, a projeção que inclui as duas achataria os dois núcleos urbanos a pontos minúsculos, e o pano de fundo de uma cidade não faz sentido perto da outra.

Já existe, no protótipo de Londres, um mecanismo que resolve um problema parecido: o Warner Bros Studio Tour fica em Leavesden, fora da área normal do mapa do dia, e é desenhado "grampeado" na borda, ligado por linha pontilhada, com a distância real escrita ao lado, sem distorcer a projeção do resto do dia. A ideia central deste planejamento é **reaproveitar esse mesmo mecanismo para a cidade de chegada**, em vez de inventar um segundo tipo de mapa.

## O que muda na decisão de navegação já tomada

A decisão anterior (registrada em `REQUIREMENTS.md`, seção 5.4) — app único, com seletor de cidade além do seletor de dia — está **revogada** por este planejamento. A substituição:

- Uma única sequência cronológica de abas de dia, cobrindo a viagem inteira (Londres, depois Paris, depois Madrid), sem partição por cidade acima dela.
- Cada dia carrega sua própria cidade como atributo (cor, rótulo), não como container.
- Um dia de fratura carrega duas cidades, e sua aba é visualmente marcada como tal (ver `plano.md` para o desenho exato).

## Arquivos afetados

- `data/roteiro.json` — schema do objeto "dia" e do objeto "parada" precisa aceitar cidade por parada, não só por dia.
- `src/components/MapaDia.jsx` (ou o equivalente em SVG do protótipo) — regra de quando uma parada é tratada como "fora do mapa" deixa de ser só uma flag manual, passa a incluir automaticamente qualquer parada cuja cidade seja diferente da cidade principal do dia.
- Navegação de abas — deixa de ter um nível de "cidade" acima de "dia".
- `.claude/agents/planejador-viagem.md` — precisa de uma regra para propor e desenhar um dia de fratura ao montar o roteiro de uma cidade nova que emenda na anterior.
- `plano/plano-<cidade>.md` — convenção de qual arquivo "é dono" do dia de fratura, para não duplicar o mesmo dia em dois arquivos com informação divergente.

Ver `plano.md`, nesta mesma pasta, para a lista de tarefas concretas derivadas deste contexto.
