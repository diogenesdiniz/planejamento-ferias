# Plano — estado compartilhado via Firestore

## 1. Firebase

- [x] Criar projeto no Firebase Console. Projeto `roteiro-ferias-2026`.
- [x] Habilitar Firestore (modo produção) e Authentication com o provedor Anônimo. Feito pelo usuário antes desta task.
- [x] Escrever as regras de segurança do Firestore: acesso de leitura e escrita restrito a um único documento fixo (ex.: `viagens/londres-paris-madrid-2026`), exigindo `request.auth != null`; nenhuma outra coleção ou documento acessível. Publicadas pelo usuário no Console.
- [x] Gerar a configuração do app web do Firebase, colocar em `.env.local` local e nos Secrets do GitHub Actions (`VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_PROJECT_ID`, etc., conforme os campos exigidos pelo SDK).
- [x] Atualizar `.github/workflows/deploy.yml` para repassar essas variáveis ao passo de build, no mesmo padrão já usado para `VITE_GOOGLE_MAPS_API_KEY`.

## 2. Módulo de sincronização

- [x] Instalar o SDK do Firebase (`firebase`).
- [x] `src/lib/estado.js`: inicializa o app Firebase, faz login anônimo silencioso na abertura, expõe o hook `useEstadoViagem()` que assina (`onSnapshot`) o documento único da viagem e retorna o estado atual (visitados, reservas, variantes escolhidas por dia) em tempo real. Cache local persistente (`persistentLocalCache`) para não quebrar offline.
- [x] Mesma API expõe funções de escrita (`alternarVisitado`, `alternarReserva`, `escolherVariante`), que atualizam o documento via `updateDoc` com caminho de campo pontilhado (`visitados.<id>`, não merge de objeto inteiro), para não sobrescrever o que a outra pessoa acabou de marcar.
- [x] Manter os três grupos de dado no mesmo formato JSON já usado no protótipo (só muda o transporte, de `window.storage`/`localStorage` para Firestore).

## 3. Ligação com os componentes

- [x] `App.jsx` passa a usar `useEstadoViagem()` como única fonte de verdade e repassa por props para `CardParada.jsx` (via `ListaParadas`), `PainelReservas.jsx` e `BlocoDecisao.jsx`, exatamente como já fazia com o estado em memória — nenhum desses três arquivos precisou mudar, já recebiam tudo via props.
- [x] Variante por dia continua por dia (chave `variantes.<diaN>`), não global.

## 4. Verificação

- [x] Testar com dois clientes simultâneos (duas abas simulando os dois usuários): marcar algo num, confirmar que aparece no outro em poucos segundos, sem precisar recarregar a página. Testado com visitado, reserva e variante — os três sincronizaram em tempo real.
- [x] Testar o caso de escrita quase simultânea nos dois clientes (marcar itens diferentes ao mesmo tempo) e confirmar que nenhuma marcação se perde. Testado: "Prospect of Whitby" marcado numa aba e "Tower Bridge" na outra, quase ao mesmo tempo — as duas marcações ficaram (4 de 4).
- [x] Confirmar que abrir o app offline não quebra a tela (mostrar o último estado conhecido, mesmo sem conseguir sincronizar, já que o uso pretendido do app é com internet disponível na rua, conforme `REQUIREMENTS.md §1`). Garantido por `persistentLocalCache` do Firestore e callback de erro silencioso no `onSnapshot`; não testado em modo avião real num device — vale conferir antes da viagem.
- [x] `npm run build` e deploy — fim desta task é o app funcional completo, com estado compartilhado entre os dois dispositivos.

## Fora deste plano

Mapa real do Google Maps e fotos (`google-maps-fotos`). Campo `cidade`/`cidadeDestino` e navegação multi-cidade (`dias-fratura`). Qualquer autenticação visível ao usuário (login com senha, múltiplos perfis) — fora de escopo combinado em `REQUIREMENTS.md §7`.
