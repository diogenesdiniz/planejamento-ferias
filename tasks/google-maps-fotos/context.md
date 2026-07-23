# Contexto — Google Maps real, com fotos, sem gastar API em rotas

## O que já foi decidido

- O mapa desenhado em SVG no protótipo vira um `<Map>` real do Google Maps JavaScript API, com ruas de verdade.
- Cada parada mostra as fotos do lugar, puxadas do Google.
- Rotas continuam sendo resolvidas **fora do app**: o botão "rota no Maps" e o "traçar" de cada trecho abrem o Google Maps de verdade (app ou navegador), como já acontece hoje via link `https://www.google.com/maps/dir/?api=1&...`. Isto é deliberado: não chamamos a Directions API, então não pagamos por cálculo de rota nenhuma vez. Os tempos e o texto de cada trecho continuam sendo estimativa escrita à mão, do jeito que já está em `data/roteiro.json`, não vêm de API.
- A chave do Google Maps vive em dois lugares, nunca no código: `.env.local` para desenvolvimento na máquina do usuário, e `VITE_GOOGLE_MAPS_API_KEY` nos Secrets do GitHub Actions para o build de produção. Isto já está refletido no `README.md` e no workflow `.github/workflows/deploy.yml`.

## O que este planejamento precisa resolver

### 1. Fotos custam uma chamada de API por lugar, e exigem um dado que ainda não temos
Hoje `data/roteiro.json` guarda só nome, coordenadas e nota de cada parada. Para buscar fotos, a Places API precisa do `placeId` de cada lugar, não só da coordenada. Esse `placeId` não está persistido em lugar nenhum do projeto ainda — foi usado de passagem, em buscas, durante a conversa em que o roteiro de Londres foi montado, mas não foi salvo nos arquivos de dados.

Isto vira uma tarefa de preenchimento: resolver o `placeId` de cada uma das paradas de Londres novamente, agora para persistir, antes que a tela de fotos tenha o que buscar.

### 2. Fotos do Google têm atribuição obrigatória
As fotos do Places vêm de fotógrafos individuais, com nome e link de perfil que o Google exige mostrar junto da imagem. Isto não é opcional; a interface precisa reservar espaço para essa atribuição em cada foto exibida, não só a imagem sozinha.

### 3. Uma API nova precisa ser habilitada, com o mesmo cuidado de custo
Buscar fotos exige habilitar a **Places API (New)** no mesmo projeto do Google Cloud, além da Maps JavaScript API já prevista. Cada lugar do roteiro (cerca de 90, contando Londres, Paris e Madrid ao final) vai gerar uma chamada de Place Details mais uma ou mais chamadas de foto. Para uso pessoal, único, numa viagem, o volume é baixo, mas o alerta de orçamento já recomendado em conversas anteriores continua sendo a proteção real, não o volume de uso.

### 4. A chave fica exposta no navegador de qualquer forma
Chave de Maps carregada no front-end é sempre visível a quem abrir o código da página, restrição por domínio (`https://SEU_USUARIO.github.io/*`) é o que impede uso indevido, não o fato de estar em Secrets. Isto vale tanto para a chave de mapa quanto para a de fotos, que normalmente é a mesma chave com mais uma API habilitada.

## Arquivos afetados

- `data/roteiro.json` e `data/lugares.json` — cada parada ganha um campo `placeId`.
- `src/components/MapaDia.jsx` — passa a renderizar o `<Map>` real, com marcadores, em vez do SVG.
- Um novo componente, por exemplo `src/components/FotosLocal.jsx` — busca e mostra as fotos de uma parada, com atribuição.
- `.env.local` e o Secret do GitHub — sem mudança de mecanismo, só de quais APIs a chave precisa ter habilitadas.

Ver `plano.md`, nesta mesma pasta, para as tarefas concretas.
