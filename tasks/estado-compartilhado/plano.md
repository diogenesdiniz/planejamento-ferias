# Plano — estado compartilhado via Firestore

## 1. Firebase

- [ ] Criar projeto no Firebase Console.
- [ ] Habilitar Firestore (modo produção) e Authentication com o provedor Anônimo.
- [ ] Escrever as regras de segurança do Firestore: acesso de leitura e escrita restrito a um único documento fixo (ex.: `viagens/londres-paris-madrid-2026`), exigindo `request.auth != null`; nenhuma outra coleção ou documento acessível.
- [ ] Gerar a configuração do app web do Firebase, colocar em `.env.local` local e nos Secrets do GitHub Actions (`VITE_FIREBASE_API_KEY`, `VITE_FIREBASE_PROJECT_ID`, etc., conforme os campos exigidos pelo SDK).
- [ ] Atualizar `.github/workflows/deploy.yml` para repassar essas variáveis ao passo de build, no mesmo padrão já usado para `VITE_GOOGLE_MAPS_API_KEY`.

## 2. Módulo de sincronização

- [ ] Instalar o SDK do Firebase (`firebase`).
- [ ] `src/lib/estado.js`: inicializa o app Firebase, faz login anônimo silencioso na abertura, expõe um hook ou função que assina (`onSnapshot`) o documento único da viagem e retorna o estado atual (visitados, reservas, variantes escolhidas por dia) em tempo real.
- [ ] Mesma API expõe funções de escrita (`marcarVisitado`, `marcarReserva`, `escolherVariante`), que atualizam o documento via `setDoc`/`updateDoc` com merge, para não sobrescrever o que a outra pessoa acabou de marcar.
- [ ] Manter os três grupos de dado no mesmo formato JSON já usado no protótipo (só muda o transporte, de `window.storage`/`localStorage` para Firestore).

## 3. Ligação com os componentes

- [ ] `CardParada.jsx`: checkbox de "visitado" lê e escreve através de `estado.js`.
- [ ] `PainelReservas.jsx`: checkbox de reserva resolvida, idem.
- [ ] `BlocoDecisao.jsx`: escolha de variante por dia, idem — precisa continuar por dia (mesma chave), não global.

## 4. Verificação

- [ ] Testar com dois clientes simultâneos (dois devices reais, ou duas abas em modo anônimo/privado, simulando os dois usuários): marcar algo num, confirmar que aparece no outro em poucos segundos, sem precisar recarregar a página.
- [ ] Testar o caso de escrita quase simultânea nos dois clientes (marcar itens diferentes ao mesmo tempo) e confirmar que nenhuma marcação se perde.
- [ ] Confirmar que abrir o app offline não quebra a tela (mostrar o último estado conhecido, mesmo sem conseguir sincronizar, já que o uso pretendido do app é com internet disponível na rua, conforme `REQUIREMENTS.md §1`).
- [ ] `npm run build` e deploy — fim desta task é o app funcional completo, com estado compartilhado entre os dois dispositivos.

## Fora deste plano

Mapa real do Google Maps e fotos (`google-maps-fotos`). Campo `cidade`/`cidadeDestino` e navegação multi-cidade (`dias-fratura`). Qualquer autenticação visível ao usuário (login com senha, múltiplos perfis) — fora de escopo combinado em `REQUIREMENTS.md §7`.
