# 🔑 Credenciais & Acessos — Minha Tirze

> **Atualizado:** 10 de Junho de 2026
> **Projeto:** minhatirze — PWA de rastreamento de tirzepatida
> **Fase atual:** Firebase (banco + login)

---

## 📦 Repositório

| Campo | Valor |
|-------|-------|
| **GitHub** | [annagalloqa/minhatirze](https://github.com/annagalloqa/minhatirze) |
| **Usuário GitHub** | annagalloqa |
| **Live URL** | https://annagalloqa.github.io/minhatirze/ |
| **Local** | `/Users/annacarolina/Downloads/tirze-app/` |
| **Token GitHub** | `/tmp/gh_token_tirze.txt` |
| **Askpass** | `/tmp/git_askpass.sh` |

---

## 🔥 Firebase (EM ANDAMENTO)

| Campo | Valor |
|-------|-------|
| **Console** | https://console.firebase.google.com/u/0/project/minha-tirze/overview |
| **Projeto** | `minha-tirze` |
| **Project ID** | `minha-tirze` |
| **App ID** | `1:521181940599:web:55b283be8cd962b578ef72` |
| **Auth Domain** | `minha-tirze.firebaseapp.com` |
| **Banco (Firestore)** | `southamerica-east1` (São Paulo) |

### firebaseConfig:
```javascript
const firebaseConfig = {
  apiKey: "AIzaSyChpE5Y1LBxaOfSDM8x7VtT3WFWcRyMR1w",
  authDomain: "minha-tirze.firebaseapp.com",
  projectId: "minha-tirze",
  storageBucket: "minha-tirze.firebasestorage.app",
  messagingSenderId: "521181940599",
  appId: "1:521181940599:web:55b283be8cd962b578ef72"
};
```

### Configuração:
- [x] Criar projeto no Firebase Console
- [x] Ativar **Authentication** → Email/Senha
- [x] Ativar **Authentication** → Google
- [x] Ativar **Firestore Database** → modo produção, `southamerica-east1`
- [x] Registrar **App Web** → config obtida
- [x] **Regras de segurança** → publicadas

---

## 💰 Stripe (FUTURO — Fase 2)

| Campo | Valor |
|-------|-------|
| **Site** | https://stripe.com/br |
| **Conta** | a criar |
| **Preço** | R$ 29,90/ano |
| **Taxa** | ~3,99% + R$ 0,49 por venda |

---

## 🟢 Google Play Store (FUTURO — Fase 4)

| Campo | Valor |
|-------|-------|
| **Console** | https://play.google.com/console |
| **Taxa** | $25 (única, vitalícia) |
| **Conta Google** | a definir |
| **Taxa por venda** | 15% (Google) |

---

## 🍎 Apple App Store (FUTURO — pós-validação)

| Campo | Valor |
|-------|-------|
| **Console** | https://appstoreconnect.apple.com |
| **Taxa** | $99/ano |
| **Taxa por venda** | 15% |
| **Dificuldade** | ALTA — precisa Mac, app nativo, revisão rigorosa |

---

## 📋 Checklist de contas a criar

| # | Conta | Status | Pra quê |
|---|-------|--------|---------|
| 1 | Firebase | ⏳ agora | Banco + login |
| 2 | Stripe | 🔜 depois | Cobrança iPhone |
| 3 | Google Play Console | 🔜 depois | Publicar Android |
| 4 | Apple Developer | 🔮 futuro | App Store |

---

> 💡 **Dica:** Use sempre a mesma conta Google (annagalloqa@gmail.com ou a que preferir) pra Firebase E Play Store — facilita a integração depois.
