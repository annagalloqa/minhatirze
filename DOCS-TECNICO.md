# Documento Técnico — Minha Tirze PWA

## Visão Geral

**Minha Tirze** é uma Progressive Web App (PWA) para acompanhamento de tratamento com tirzepatida. Em migração de localStorage para Firebase (Firestore + Auth).

- **URL:** https://annagalloqa.github.io/minhatirze/
- **Repositório:** github.com/annagalloqa/minhatirze
- **Arquivo principal:** `index.html` (~2.420 linhas atualmente)
- **Plataformas:** iOS (Safari/PWA) e Android (Chrome/Brave)

---

## Arquitetura

```
index.html (arquivo único)
├── <head>
│   ├── Meta tags PWA (apple-mobile-web-app, manifest, theme-color)
│   ├── Google Fonts (DM Sans, DM Serif Display)
│   ├── Firebase SDK (Auth + Firestore) ← NOVO
│   └── <style> (~500 linhas CSS)
├── <body>
│   ├── #onboardingScreen      — 3 slides de boas-vindas (só PWA)
│   ├── #installScreen         — Instruções de instalação (iOS/Android)
│   ├── #loginScreen           — Email + Senha (Firebase Auth) ← MUDOU
│   ├── .header                — Barra superior fixa
│   ├── .bottom-nav            — 5 abas
│   ├── .page#page-*           — Páginas do app
│   ├── #shareModal            — Modal de compartilhamento
│   └── .toast                 — Notificações temporárias
└── <script> (~2.000 linhas JavaScript)
```

---

## Stack Tecnológica

| Camada | Antes | Depois |
|--------|-------|--------|
| Estrutura | HTML5 | HTML5 |
| Estilo | CSS3 | CSS3 |
| Lógica | JavaScript ES6+ | JavaScript ES6+ |
| **Armazenamento** | **localStorage** | **Firestore (Firebase)** |
| **Autenticação** | **PIN 4 dígitos** | **Firebase Auth (email/senha)** |
| Gráficos | Canvas 2D | Canvas 2D |
| Fontes | Google Fonts | Google Fonts |
| PWA | Manifest + SW | Manifest + SW |
| Deploy | GitHub Pages | GitHub Pages |
| **Backend** | **nenhum** | **Firebase (serverless)** |

---

## Firebase — Plano de Migração

### Fase 1A: Setup do Projeto Firebase 🔥
- [x] Criar arquivo CREDENCIAIS.md com acessos
- [ ] Criar projeto no Firebase Console (`minha-tirze`)
- [ ] Ativar Authentication (Email/Senha)
- [ ] Ativar Firestore Database
- [ ] Registrar App Web → obter `firebaseConfig`
- [ ] Adicionar Firebase SDK no `index.html`

### Fase 1B: Substituir localStorage → Firestore
Cada chave do localStorage vira uma **coleção** no Firestore:

| localStorage (ANTES) | Firestore (DEPOIS) | Estrutura |
|----------------------|-------------------|-----------|
| `tirzName` | `users/{uid}` | `{name, email, createdAt}` |
| `tirzPin` | ❌ REMOVIDO | Firebase Auth gerencia |
| `tirzApps` | `users/{uid}/apps/{id}` | `{datetime, dose, site, notes}` |
| `tirzWeights` | `users/{uid}/weights/{id}` | `{date, kg}` |
| `tirzGoal` | `users/{uid}/goal` | `{start, current, target, startDate, height}` |
| `tirzSymptoms` | `users/{uid}/symptoms/{id}` | `{date, chips, notes}` |
| `tirzNextDose` | `users/{uid}` | `{nextDose}` |
| `tirzAch` | `users/{uid}` | `{achievements: [...]}` |
| `tirzVial` | `users/{uid}` | `{vial}` |
| `tirzAppVersion` | `users/{uid}` | `{appVersion}` |

### Fase 1C: Login (Firebase Auth)
- Email + senha (Firebase Auth)
- Onboarding adaptado: "Crie sua conta" em vez de "Crie seu PIN"
- Fluxo: nome → email → senha → confirmar senha → criar conta
- Login: email + senha
- "Esqueci minha senha" → Firebase manda email de reset

### Fase 1D: Migração de dados existentes
- Quando usuário faz login pela primeira vez:
  1. Detecta se tem dados no localStorage
  2. Oferece "Importar dados do app antigo"
  3. Sobe tudo pro Firestore
  4. Apaga localStorage (limpa)

### Fase 1E: Offline (opcional)
- Firestore tem suporte offline nativo
- Dados são salvos localmente e sincronizam quando online

---

## Sistema de Cores (CSS Variables)

```css
--rose:       #C4717A   /* cor principal */
--rose-light: #E8B4B8   /* bordas e detalhes */
--rose-pale:  #F9ECED   /* fundos suaves */
--sage:       #7A9E8E   /* verde (meta, proteína) */
--sage-light: #B8D4C8
--cream:      #FDF8F4   /* fundo da página */
--warm-white: #FFFAF8   /* fundo dos cards */
--text:       #2D1B1E   /* texto principal */
--text-soft:  #7A6568   /* texto secundário */
--gold:       #C9A96E   /* destaque decorativo */
```

---

## Fluxo de Inicialização (NOVO — Firebase)

```
window.onload
  ├── Cache bust (APP_VERSION)
  ├── Inicializa Firebase
  ├── checkInstallScreen()
  │     ├── isStandalone? → não mostra nada (PWA)
  │     └── !isStandalone? → mostra #installScreen
  ├── initOnboarding()
  │     ├── !isStandalone? → pula
  │     ├── firebase.auth().currentUser? → pula (já logado)
  │     └── sem sessão && isStandalone → mostra #onboardingScreen
  └── initLogin() ou onbFinish()
        ├── sem conta → fluxo de criação (nome → email → senha)
        ├── com conta → fluxo de entrada (email + senha)
        └── onAuthStateChanged() → unlockApp()
```

---

## Detecção de Plataforma

```javascript
const isAndroid = /Android/i.test(navigator.userAgent);
const isIOS = /iPhone|iPad|iPod/i.test(navigator.userAgent);
const isStandalone = navigator.standalone || matchMedia('(display-mode: standalone)');
```

---

## Funcionalidades Técnicas (mantidas)

1. Onboarding — 3 slides (adaptado pra Firebase)
2. Login — Email + Senha (Firebase Auth) ← MUDOU
3. Registro de Aplicação — mantido (Firestore)
4. Calendário ICS/Google Calendar — mantido
5. Diário de Sintomas — mantido (Firestore)
6. Peso e Meta — mantido (Firestore)
7. Resumo (Dashboard) — mantido (Firestore)
8. Share Card — mantido
9. Swipe-to-Delete — mantido
10. Conquistas — mantido (Firestore)
11. PWA / Instalação — mantido

---

## Versionamento

Versão atual: `16` ← Firebase migration

---

## Deploy

```bash
cd /Users/annacarolina/Downloads/tirze-app
git add index.html
git commit -m "firebase: <descrição>"
GIT_ASKPASS=/tmp/git_askpass.sh git push origin main
```

---

## Documentos relacionados

- **CREDENCIAIS.md** — Todas as contas, tokens e acessos
- **GUIA-RESUMIDO.md** — Guia rápido do usuário
- **GUIA-COMPLETO.md** — Manual completo
