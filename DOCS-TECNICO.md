# Documento Técnico — Minha Tirze PWA

## Visão Geral

**Minha Tirze** é uma Progressive Web App (PWA) de arquivo único (single-file) para acompanhamento de tratamento com tirzepatida. Desenvolvida em HTML/CSS/JavaScript vanilla, sem frameworks ou dependências externas (exceto Google Fonts). Todos os dados são armazenados localmente no dispositivo do usuário via `localStorage`.

- **URL:** https://annagalloqa.github.io/minhatirze/
- **Repositório:** github.com/annagalloqa/minhatirze
- **Arquivo principal:** `index.html` (~2.300 linhas)
- **Plataformas:** iOS (Safari/PWA) e Android (Chrome/Brave)

---

## Arquitetura

```
index.html (arquivo único)
├── <head>
│   ├── Meta tags PWA (apple-mobile-web-app, manifest, theme-color)
│   ├── Google Fonts (DM Sans, DM Serif Display)
│   └── <style> (~500 linhas CSS)
├── <body>
│   ├── #onboardingScreen      — 3 slides de boas-vindas (só PWA)
│   ├── #installScreen         — Instruções de instalação (iOS/Android)
│   ├── #loginScreen           — Nome + PIN de 4 dígitos
│   ├── .header                — Barra superior fixa (dose + próx. dose)
│   ├── .bottom-nav            — 5 abas (Registrar, Sintomas, Histórico, Peso, Resumo)
│   ├── .page#page-registrar   — Registrar aplicação
│   ├── .page#page-sintomas    — Diário de sintomas
│   ├── .page#page-historico   — Histórico de aplicações
│   ├── .page#page-peso        — Meta + registro de peso + gráfico
│   ├── .page#page-resumo      — Estatísticas, IMC, proteína, conquistas, share
│   ├── #shareModal            — Modal de compartilhamento de progresso
│   └── .toast                 — Notificações temporárias
└── <script> (~1.800 linhas JavaScript)
```

---

## Stack Tecnológica

| Camada | Tecnologia |
|--------|-----------|
| Estrutura | HTML5 semântico |
| Estilo | CSS3 (variáveis, flexbox, animações, safe-area) |
| Lógica | JavaScript ES6+ vanilla |
| Armazenamento | `localStorage` (8 chaves) |
| Gráficos | Canvas 2D API (gráfico de peso + share card) |
| Fontes | Google Fonts: DM Sans (UI) + DM Serif Display (títulos) |
| PWA | Manifest JSON, Service Worker (implícito via GH Pages) |
| Deploy | GitHub Pages (push na branch `main`) |

---

## Armazenamento (localStorage)

| Chave | Conteúdo | Formato |
|-------|---------|---------|
| `tirzName` | Nome do usuário | string |
| `tirzPin` | PIN de 4 dígitos | string |
| `tirzApps` | Aplicações registradas | `[{id, datetime, dose, site, notes}]` |
| `tirzWeights` | Pesos registrados | `[{date, kg}]` |
| `tirzGoal` | Meta de peso | `{start, current, target, startDate, height}` |
| `tirzSymptoms` | Sintomas diários | `[{date, chips, notes}]` |
| `tirzNextDose` | Data da próxima dose | string ISO |
| `tirzAch` | Conquistas desbloqueadas | `['first_dose', ...]` |
| `tirzVial` | Concentração do frasco | number (5, 10 ou 15) |
| `tirzAppVersion` | Versão do app (cache bust) | string |

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

## Fluxo de Inicialização

```
window.onload
  ├── Cache bust (APP_VERSION)
  ├── checkInstallScreen()
  │     ├── isStandalone? → não mostra nada (PWA)
  │     └── !isStandalone? → mostra #installScreen
  ├── initOnboarding()
  │     ├── !isStandalone? → pula (navegador)
  │     ├── hasPin? → pula (já usou)
  │     └── !hasPin && isStandalone → mostra #onboardingScreen
  └── initLogin() ou onbFinish()
        ├── !hasPin → fluxo de criação (nome → PIN → confirmar)
        ├── hasPin → fluxo de entrada (digitar PIN)
        └── unlockApp() → initApp()
```

---

## Detecção de Plataforma

```javascript
const isAndroid = /Android/i.test(navigator.userAgent);
const isIOS = /iPhone|iPad|iPod/i.test(navigator.userAgent);
const isStandalone = navigator.standalone || matchMedia('(display-mode: standalone)');
```

Comportamentos específicos por plataforma:
- **iOS PWA:** Blob URL + `<a download>` para evento de calendário
- **Android:** Google Calendar link em vez de ICS
- **Share Card:** `navigator.share()` com fallback para download via Blob URL

---

## Funcionalidades Técnicas

### 1. Onboarding
- 3 slides com `scroll-snap-type: x mandatory`
- Swipe detection via `touchstart`/`touchend`
- Indicadores de slide (dots)
- Só aparece no PWA, primeira vez

### 2. PIN de Segurança
- 4 dígitos numéricos
- Fluxo: nome → criar PIN → confirmar PIN → entrar
- Redefinição de PIN preserva dados

### 3. Registro de Aplicação
- DateTime picker nativo
- Dose picker com +/− (0.5mg a 15mg)
- Dose chips de acesso rápido
- Conversor mg → UI (unidades na seringa)
- Mapa corporal SVG com 8 zonas clicáveis
- Agenda próxima dose (+7 dias) com integração calendário

### 4. Calendário (ICS / Google Calendar)
- Gera arquivo `.ics` (RFC 5545) com `VALARM`
- iOS: Blob URL + `<a download>` (abre app Calendário nativo)
- Android: Google Calendar URL

### 5. Diário de Sintomas
- 10 chips de sintomas + toggle
- Notas textuais
- Histórico com swipe-to-delete

### 6. Peso e Meta
- Meta: peso inicial, atual, alvo, data de início, altura
- Registro de peso com date picker + stepper
- Gráfico Canvas 2D com linha, área, dots e linha de meta
- Barra de progresso da meta
- Swipe-to-delete nos registros

### 7. Resumo (Dashboard)
- Cards de estatísticas (total apps, dose atual, kg perdidos)
- Progressão de dose (barra + chips)
- IMC + recomendação de proteína
- Sistema de conquistas (8 achievements)
- Share Card (Canvas → Blob URL → share nativo)
- Rodízio inteligente de zonas
- Exportação de dados (texto)
- Limpeza de dados (confirmação)

### 8. Share Card
- Canvas 600×800 com gradiente, métricas, frase motivacional
- Blob URL para compatibilidade com `download` attribute
- `navigator.share()` com `files` para compartilhamento nativo
- Modal overlay com botões: Compartilhar, Salvar, Fechar

### 9. Swipe-to-Delete
- Touch events (touchstart/move/end)
- Arrasta → revela ícone 🗑 → clica para deletar
- Fecha ao tocar fora
- Presente em: Histórico, Sintomas, Peso

### 10. Conquistas
- 8 conquistas: primeira dose, 4 semanas, 12 semanas, -5kg, -10kg, meta, 7 diários, rodízio completo
- Toast especial ao desbloquear (3.5s)
- Lista com ícones 🔒/🏆 no Resumo

### 11. PWA / Instalação
- Manifest JSON com ícone e cores
- `beforeinstallprompt` para instalação nativa Android
- Tela de instruções com abas iOS/Android
- Cache bust via `APP_VERSION` no localStorage

---

## Versionamento

Versão atual: `15` (APP_VERSION no localStorage)

O sistema de cache bust força reload quando a versão muda, garantindo que PWAs instaladas recebam atualizações.

---

## Deploy

```bash
cd /Users/annacarolina/Downloads/tirze-app
git add index.html
git commit -m "descrição"
GIT_ASKPASS=/tmp/git_askpass.sh git push origin main
```

GitHub Pages detecta o push na branch `main` e publica automaticamente em ~1-2 minutos.
