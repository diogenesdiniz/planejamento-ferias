# Contexto — app instalável (PWA)

## O problema

Hoje o app é só um site. `REQUIREMENTS.md §1` já registra o uso pretendido: "Consulta pelo iPad e pelo celular, na rua, com internet disponível" — ou seja, não uma visita única, mas consultas repetidas ao longo de toda a viagem (uma semana em Londres, depois Paris e Madrid). Cada consulta hoje depende de achar a aba do navegador ou digitar a URL de novo. Um ícone na tela de início, que abre direto em tela cheia sem barra de endereço, é a melhoria de usabilidade mais barata que dá pra fazer para esse padrão de uso.

## Por que agora

Não depende de nenhuma outra task pendente (a curadoria de lugares de Paris/Madrid é conteúdo, não código). É aditivo: não muda nenhum comportamento existente do app, só a forma como ele é aberto e exibido no sistema.

## Decisão que precisa da palavra do usuário: nível de instalabilidade

Duas opções, com trade-off real entre elas:

**(a) Mínimo** — `manifest.json` + ícones + meta tags. Funciona com "Adicionar à Tela de Início" manual (menu ⋮ no Android Chrome, Compartilhar no iOS Safari). Sem service worker, sem cache de nada. Zero risco de o app mostrar uma versão desatualizada depois de um novo deploy.

**(b) Completo** — tudo do item (a) mais um service worker (via `vite-plugin-pwa`), que faz o Android Chrome oferecer o banner automático "Instalar app" em vez de exigir o menu manual. Em troca, introduz cache do app-shell (HTML/JS/CSS), que precisa de estratégia de invalidação correta — sem isso, o risco real é o usuário abrir o app instalado e ver o roteiro de uma versão antiga, presa em cache, sem saber que existe uma atualização.

Recomendação: **(a)**. O app já depende de conexão em tempo real para tudo que importa (Firestore para o estado compartilhado, Google Maps, Places para as fotos) — não teria sentido funcionar "meio offline" só para abrir mais rápido, e isso vai contra `REQUIREMENTS.md §1`, que já assume internet disponível. Dá para evoluir para (b) depois, como task separada, se o banner automático de instalação fizer falta na prática.

## Decisão que precisa da palavra do usuário: origem do ícone

Não existe nenhuma arte pronta no projeto. Duas opções:

- O usuário fornece uma imagem (foto, logo, ilustração) para servir de base.
- Gero um ícone simples e abstrato (ex.: um quadrado ou pin de mapa numa cor da paleta do roteiro, com iniciais ou um símbolo), via SVG convertido para PNG nos tamanhos exigidos, sem depender de nenhuma ferramenta de design externa.

## Arquivos afetados

- `public/manifest.json` — novo.
- `public/icons/*.png` — novos: pelo menos 512×512 e 192×192 (manifest, Android), 180×180 (`apple-touch-icon`), 32×32 (favicon).
- `index.html` — `<link rel="manifest">`, `<link rel="icon">`, `<link rel="apple-touch-icon">`, `<meta name="theme-color">`, `<meta name="apple-mobile-web-app-capable">`, `<meta name="apple-mobile-web-app-status-bar-style">`, `<meta name="mobile-web-app-capable">`.
- Só se a decisão for pelo nível (b): `vite.config.js` (plugin `vite-plugin-pwa`) e `package.json` (nova dependência).

Ver `plano.md`, nesta mesma pasta, para as tarefas concretas.
