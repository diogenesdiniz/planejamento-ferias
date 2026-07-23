# Contexto — estado do usuário sincronizado entre os dois dispositivos

## O que já foi decidido

Registrado no ADR 0006: visitado, reservas marcadas e variante escolhida por dia deixam de viver só em `localStorage` por aparelho e passam a sincronizar via Firestore, num único documento da viagem, compartilhado entre o dispositivo de Diógenes e o da parceira. Sem tela de login — autenticação anônima do Firebase, silenciosa, só para as regras de segurança do Firestore exigirem `request.auth != null`.

## O problema

A task `dado-e-componentes` entrega os componentes com os controles de "visitado", "reserva resolvida" e "escolha de variante" já desenhados, mas sem persistir nada além da sessão atual em memória. Esta task liga esses controles a um mecanismo de sincronização de verdade, sem exigir cadastro de ninguém.

## Por que isto importa

Sem sincronização, cada pessoa vê uma cópia divergente do progresso da viagem — exatamente o problema que motivou a reversão da decisão original (ver ADR 0006). O valor do checklist compartilhado só existe se as duas pessoas veem o mesmo estado.

## Cuidado de segurança

A configuração do Firebase, como qualquer chave de API de front-end, fica visível a quem abrir o código da página — isso não é um vazamento, é esperado. A proteção real é a regra de segurança do Firestore, restringindo leitura e escrita ao único documento fixo desta viagem, e exigindo autenticação (mesmo anônima), no mesmo espírito já usado para a chave do Google Maps em `tasks/google-maps-fotos/context.md`.

## Arquivos afetados

- `src/lib/estado.js` — novo: login anônimo silencioso, `onSnapshot` do documento da viagem para leitura em tempo real, `setDoc`/`updateDoc` para escrita.
- `src/components/CardParada.jsx`, `PainelReservas.jsx`, `BlocoDecisao.jsx` — passam a ler e escrever através de `estado.js`, em vez de estado local em memória.
- `.env.local` e o Secret do GitHub Actions — novas variáveis `VITE_FIREBASE_*`.
- `.github/workflows/deploy.yml` — passa essas variáveis ao build.
- Firestore Security Rules, no Firebase Console — não versionadas neste repositório por padrão do Firebase, mas documentadas em `plano.md`.

Ver `plano.md`, nesta mesma pasta, para os passos concretos.
