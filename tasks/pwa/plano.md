# Plano — app instalável (PWA)

## 1. Decisão prévia

- [x] Confirmar com o usuário: instalabilidade mínima (manifest + ícones, sem service worker) ou completa (+ `vite-plugin-pwa`, banner de instalar, cache do app-shell)? Ver trade-off em `context.md`. **Decidido: mínima.**
- [x] Confirmar a origem do ícone: arte fornecida pelo usuário, ou gerado de forma simples pelo Claude Code. **Decidido: gerado.** Pin de mapa branco sobre fundo escuro (`#0C131D`, a mesma cor de fundo do mapa no app), desenhado em SVG e capturado via screenshot do navegador — sem precisar de nenhuma ferramenta de design externa nem dependência nova.

## 2. Ícones e manifest

- [x] Produzir o ícone-base em pelo menos 512×512, fundo sólido (sem transparência — o Android recorta mal ícone com fundo transparente).
- [x] Gerar os tamanhos derivados: 512×512 e 192×192 (manifest/Android), 180×180 (`apple-touch-icon`), favicon 32×32/16×16 num `.ico` só. Gerados com Python/Pillow a partir do ícone-base, em `public/icons/` e `public/favicon.ico`.
- [x] `public/manifest.json`: `name`, `short_name`, `start_url` e `scope` (`/roteiro-ferias-2026/`, batendo com o `base` do Vite), `display: "standalone"`, `background_color`/`theme_color` (`#0C131D`), lista de ícones.
- [x] `index.html`: `<link rel="manifest">`, `<link rel="icon">`, `<link rel="apple-touch-icon">`, `<meta name="theme-color">`, `<meta name="apple-mobile-web-app-capable" content="yes">`, `<meta name="mobile-web-app-capable" content="yes">`, `<meta name="apple-mobile-web-app-status-bar-style">`, `<meta name="apple-mobile-web-app-title">`. **Pegadinha encontrada:** usar `%BASE_URL%` nesses hrefs duplicava o caminho base no `npm run dev` (Vite já reescreve hrefs relativos do `index.html` sozinho); corrigido usando caminhos relativos simples (`"manifest.json"`, não `"%BASE_URL%manifest.json"`), que funcionam sem duplicação tanto em dev quanto na build de produção.

## 3. Service worker — não se aplica

Fora deste plano por decisão da seção 1 (nível mínimo escolhido).

## 4. Verificação

- [x] `npm run build` sem erro.
- [x] Confirmado com `npm run preview` (build real, não o servidor de dev) que `manifest.json` (content-type `application/json`), os ícones e o favicon respondem 200, sem erro no console.
- [ ] No Android Chrome (dispositivo real): confirmar a opção de instalar/adicionar à tela inicial, com ícone e nome corretos. Não verificável por mim (preciso de um device Android real ou emulação que este ambiente não tem) — pendente para o usuário confirmar antes da viagem.
- [ ] No iOS/iPadOS Safari (dispositivo real): Compartilhar → Adicionar à Tela de Início; confirmar ícone, nome, e que abre em tela cheia, sem barra de endereço. Mesma limitação — pendente para o usuário.
- [x] Confirmar que o app carrega o roteiro real normalmente com as mudanças (Firestore, Maps, fotos) — testado via `npm run preview`, nenhuma regressão.

## Fora deste plano

Funcionamento totalmente offline (fora de escopo por `REQUIREMENTS.md §1`, que já assume internet disponível na rua). Notificações push. Ícone "dinâmico" com badge (ex.: número de reservas pendentes no ícone do app) — feature separada, não pedida.
