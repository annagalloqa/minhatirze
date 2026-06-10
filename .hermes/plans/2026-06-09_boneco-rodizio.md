# Plano: Boneco + Flancos + Rodízio Inteligente

**Data:** 09/06/2026  
**Arquivo:** `index.html` (single-file PWA)

---

## 1. 🐛 Corrigir: deselecionar zona do boneco

**Problema atual:** O código da função `selectZone` já foi atualizado com a lógica de toggle, mas o usuário reporta que não está funcionando no app. Possível causa: cache do navegador/PWA.

**Ação:**
- Forçar cache-bust adicionando `?v=N` ao final da URL em `updateSummary` ou adicionar meta tag no HTML
- Verificar se o push mais recente (commit `89932bc`) realmente contém a função atualizada → confirmado via `read_file`
- Adicionar `meta http-equiv="Cache-Control"` com `no-cache` (já existe, linha 7)
- **Ação extra:** Bump manual do cache hash no `localStorage` para forçar reload no PWA

**Arquivo:** `index.html` linha 1021-1053

---

## 2. 🎨 Silhueta mais realista

**Problema:** Boneco atual usa retângulos e elipses simples (cabeça=elipse, tronco=retângulo, braços=retângulos, pernas=retângulos).

**Proposta:** Redesenhar o SVG com `path` (curvas de Bézier):

```
Cabeça: oval mais natural
Pescoço: transição suave com trapézio
Tronco: forma de ampulheta com curvas laterais
Braços: afastados do corpo, com curva no cotovelo
Pernas: ligeiramente afastadas, com curva na coxa
```

**Dimensões:** Manter o viewBox `0 0 200 340` para não quebrar o layout
**Cores:** Mesmo esquema: `#E8DDD8` preenchimento, `#C4A99A` contorno

**Arquivo:** `index.html` linhas 406-473 (SVG do boneco)

---

## 3. ➕ Novas zonas: Flanco Esquerdo e Flanco Direito

**O quê:** Adicionar duas zonas clicáveis nos flancos (laterais da cintura/"pneuzinhos")

**Posição no SVG:**
- Flanco esquerdo: x≈58, y≈100, w≈14, h≈40 (lado esquerdo do tronco)
- Flanco direito: x≈128, y≈100, w≈14, h≈40 (lado direito do tronco)

**IDs:** `zone-flanco-l` e `zone-flanco-r`  
**Labels:** "Flanco esquerdo" e "Flanco direito"  
**Texto:** "FLANCO ESQ" e "FLANCO DIR" (fonte 6px, empilhado em 2 linhas)

**CSS:** Mesmas classes `.zone` e `.zone-fill` já existentes  
**Total de zonas:** 6 → 8

**Arquivo:** `index.html` SVG + `saveApplication` (zone reset)

---

## 4. 🧠 Rodízio inteligente no Resumo

**Problema:** A seção "🗺 Rodízio de Aplicações" existe no HTML mas nunca foi populada — é um card vazio.

**Proposta:** Transformar em um guia visual de rodízio:

### Layout proposto:
```
🗺 Rodízio de Aplicações
┌─────────────────────────────────┐
│ Última: Coxa direita (09/06)    │
│ Anterior: Abdômen esq (02/06)  │
│                                 │
│ 💡 Sugestão para próxima dose: │
│ ┌───────────────────────────┐   │
│ │   🟢 Flanco direito      │   │
│ │   (não usada há 30 dias) │   │
│ └───────────────────────────┘   │
│                                 │
│ Distribuição (últimas 6):       │
│ Abdômen ████████░░ 40%          │
│ Coxas   ██████░░░░ 30%          │
│ Braços  ████░░░░░░ 20%          │
│ Flancos ██░░░░░░░░ 10%          │
└─────────────────────────────────┘
```

### Lógica:
1. Pega as últimas 6 aplicações do `localStorage`
2. Conta frequência por tipo de zona (abdômen, coxa, braço, flanco)
3. Sugere a zona **menos usada** recentemente
4. Se empate, sugere a que está há mais tempo sem uso
5. Mostra barras de distribuição

### Cores por zona:
- Abdômen: `#C4717A` (rose)
- Coxa: `#7A9E8E` (sage)  
- Braço: `#C9A96E` (gold)
- Flanco: `#B8A0C4` (lavender)

**Arquivo:** `index.html` — nova função `renderRotation()` + chamada no `updateSummary()`

---

## Ordem de execução

| # | Tarefa | Complexidade |
|---|---|---|
| 1 | Cache-bust forçar reload | Baixa |
| 2 | Silhueta realista (SVG paths) | Média |
| 3 | Adicionar flancos E/D | Baixa |
| 4 | Rodízio inteligente no Resumo | Média |

---

## Validação

- [ ] Tocar na mesma zona 2x → deseleciona (label volta a "Toque em uma área")
- [ ] Trocar de zona → anterior limpa sem manchas
- [ ] Boneco com curvas suaves, proporções humanas
- [ ] 8 zonas clicáveis (6 originais + 2 flancos)
- [ ] Resumo mostra últimas aplicações + sugestão + barras
- [ ] iPhone + Android: ambos funcionam
