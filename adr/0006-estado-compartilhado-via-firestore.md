# ADR 0006 — Estado do usuário sincronizado entre dispositivos, via Firestore

## Status
Aceita. Revoga a frase correspondente em `REQUIREMENTS.md §7` ("sincronização de estado entre dispositivos" como fora de escopo).

## Contexto
A decisão original, registrada como combinada em `REQUIREMENTS.md §7`, era que celular e iPad não compartilhariam o que foi marcado como visitado — cada aparelho guardaria o seu, sem sincronização. Essa decisão partia do pressuposto de um único usuário navegando em mais de um aparelho seu.

O uso real do app envolve duas pessoas — Diógenes e sua parceira —, cada uma no seu próprio device. Sem sincronização, cada um veria uma cópia divergente do que já foi visitado, de quais reservas já foram resolvidas e de qual variante foi escolhida em cada dia, o que derrota o propósito de checklist compartilhado da viagem.

## Decisão
O estado do usuário (visitados, reservas marcadas, variante escolhida por dia) deixa de viver só em `localStorage` por aparelho e passa a sincronizar em tempo real via **Firestore** (Firebase), num único documento da viagem, compartilhado pelos dois dispositivos.

Sem tela de login: autenticação anônima do Firebase acontece silenciosamente na abertura do app, só para que as regras de segurança do Firestore possam exigir `request.auth != null` sem pedir senha nem cadastro a ninguém. As regras restringem leitura e escrita ao documento fixo desta viagem, nenhuma outra coleção fica acessível.

## Consequências
- Exige um projeto novo no Firebase Console, com Firestore e Authentication (modo anônimo) habilitados.
- Novas variáveis de ambiente `VITE_FIREBASE_*`, no mesmo padrão já usado para a chave do Google Maps: `.env.local` para desenvolvimento, Secret do GitHub Actions para produção (`.github/workflows/deploy.yml` precisa ser atualizado para repassá-las ao build).
- A configuração do Firebase fica visível no bundle do client, como qualquer chave de API de front-end — a proteção real é a regra de segurança do Firestore, não o sigilo da configuração, no mesmo espírito já registrado para a chave do Maps em `tasks/google-maps-fotos/context.md`.
- Custo esperado é zero: o volume de leitura/escrita de duas pessoas numa semana de viagem fica muito abaixo do free tier do Firestore.
- `data/roteiro.json` (dado do roteiro em si) não muda de lugar nem de mecanismo — só o estado do usuário muda de `localStorage` para Firestore. A separação entre dado do roteiro e estado do usuário, registrada no ADR 0004, continua valendo.
- O detalhamento da implementação (regras de segurança, módulo de sincronização, ligação com os componentes) está em `tasks/estado-compartilhado/context.md` e `plano.md`.
