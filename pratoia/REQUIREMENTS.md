# PratoIA — Documento de Requisitos Completo

> **Versão:** 1.0 | **Data:** 2026-03-03
> **Clone 1:1 do Cal AI (calai.app) para o mercado brasileiro**

---

## Sumário

1. [Visão Geral](#1-visão-geral)
2. [Stack Tecnológico](#2-stack-tecnológico)
3. [Fluxo do App (Tela a Tela)](#3-fluxo-do-app-tela-a-tela)
4. [Onboarding Detalhado](#4-onboarding-detalhado)
5. [Feature Matrix](#5-feature-matrix)
6. [Especificações UI/UX](#6-especificações-uiux)
7. [Integração com IA](#7-integração-com-ia)
8. [Modelos de Dados](#8-modelos-de-dados)
9. [API Endpoints](#9-api-endpoints)
10. [Estrutura Supabase](#10-estrutura-supabase)
11. [Gamificação & Engajamento](#11-gamificação--engajamento)
12. [Localização Brasil](#12-localização-brasil)
13. [Escopo MVP](#13-escopo-mvp)
14. [Requisitos Não-Funcionais](#14-requisitos-não-funcionais)

---

## 1. Visão Geral

**PratoIA** é um aplicativo de rastreamento calórico por IA, clone funcional 1:1 do Cal AI, adaptado para o mercado brasileiro. O usuário tira uma foto da refeição e a IA identifica os alimentos, estima porções e calcula macronutrientes automaticamente.

### Proposta de Valor
- Rastreamento calórico sem esforço via foto
- Base de dados alimentar brasileira (TACO/TBCA)
- Interface em português
- Preço acessível: R$14,90/mês (pós-MVP)

### Público-Alvo
- Pessoas em dieta (emagrecimento, ganho de massa, manutenção)
- Praticantes de musculação/fitness
- Pessoas com restrições alimentares
- Profissionais de saúde acompanhando pacientes
- Faixa etária: 18-45 anos
- Classe B/C brasileira

---

## 2. Stack Tecnológico

| Componente | Tecnologia |
|---|---|
| Frontend (Mobile) | Flutter (iOS + Android) |
| Backend API | Node.js (Express/Fastify) |
| Banco de Dados | Supabase (PostgreSQL) |
| Autenticação | Supabase Auth |
| Storage (fotos) | Supabase Storage |
| IA Primária | Google Gemini Flash 2.0 |
| IA Fallback | GPT-4.1 nano (OpenAI) |
| Base Alimentar | TACO/TBCA (Tabela Brasileira de Composição de Alimentos) |
| Push Notifications | Firebase Cloud Messaging (FCM) |
| Analytics | Mixpanel ou PostHog |
| Pagamentos | RevenueCat (Apple/Google) + Stripe (PIX futuramente) |
| CI/CD | GitHub Actions + Codemagic |
| Barcode API | Open Food Facts BR |

---

## 3. Fluxo do App (Tela a Tela)

### 3.1 Splash Screen
- Logo PratoIA centralizado com animação de fade-in
- Duração: 1.5s
- Verifica autenticação:
  - Logado → Home Dashboard
  - Primeiro acesso → Onboarding
  - Conta existente sem onboarding completo → Retomar onboarding

### 3.2 Onboarding (ver seção 4 para detalhes)
- Sequência de 10-12 telas com progresso visual
- Cada tela = 1 pergunta
- Transição: slide horizontal
- Botão "Voltar" em todas as telas (exceto primeira)

### 3.3 Paywall / Assinatura
- Apresentado após onboarding completo
- Mostra resultado personalizado ("Você atingirá seu objetivo em X semanas")
- Planos:
  - Semanal: R$9,90/semana
  - Mensal: R$14,90/mês (destaque "MAIS POPULAR")
  - Anual: R$99,90/ano (destaque "MELHOR VALOR")
- Trial gratuito de 3 dias (MVP: app gratuito, sem paywall)
- Botão "Continuar sem premium" discreto no topo
- Garantia de 7 dias

### 3.4 Home / Dashboard
Tela principal com layout vertical scrollable:

#### Header
- Saudação: "Olá, {nome}!" com avatar
- Data atual por extenso: "Terça, 3 de março"
- Ícone de configurações (engrenagem) no canto superior direito
- Streak counter (🔥 5 dias)

#### Resumo Diário (Card Principal)
- Círculo de progresso calórico grande (center)
  - Calorias consumidas / meta
  - Calorias restantes no centro
- Barra de progresso para cada macro abaixo:
  - Proteína (g) — cor azul
  - Carboidratos (g) — cor laranja
  - Gordura (g) — cor amarela
- Cada barra mostra: consumido / meta

#### Refeições do Dia
Lista vertical de cards por refeição:
- ☀️ **Café da Manhã** — horário sugerido: 06:00-10:00
- 🌤️ **Lanche da Manhã** — horário: 10:00-11:30
- ☀️ **Almoço** — horário: 11:30-14:00
- 🍎 **Lanche da Tarde** — horário: 14:00-17:00
- 🌙 **Jantar** — horário: 17:00-21:00
- 🌜 **Ceia** — horário: 21:00-23:00

Cada card mostra:
- Nome da refeição
- Total de calorias (se já logado)
- Lista de alimentos registrados (thumbnail + nome + calorias)
- Botão "+" para adicionar alimento

#### Água
- Card com copo d'água e progresso (ex: 5/8 copos)
- Botão "+" para adicionar copo
- Meta diária configurável

#### Exercício
- Card resumo de exercício do dia
- Calorias queimadas
- Botão "+" para adicionar exercício

### 3.5 Botão Central de Captura (FAB)
- Floating Action Button grande, verde, centralizado na bottom nav
- Ícone de câmera
- Ao tocar:
  1. Abre câmera nativa em fullscreen
  2. Overlay com guia de enquadramento ("Posicione o prato no centro")
  3. Botão de captura
  4. Opção de galeria (ícone no canto)
  5. Flash toggle

### 3.6 Câmera / Captura de Foto
- Câmera fullscreen com overlay
- Guidelines visuais para posicionar o prato
- Texto: "Aponte para sua refeição"
- Botões:
  - Capturar (centro, grande)
  - Galeria (canto inferior esquerdo)
  - Flash (canto superior direito)
  - Fechar/X (canto superior esquerdo)
- Após captura:
  - Preview da foto
  - Botão "Analisar" (confirmar)
  - Botão "Tirar outra" (refazer)

### 3.7 Análise IA — Loading
- Foto em background com blur
- Animação de loading (pulse ou shimmer)
- Textos rotativos:
  - "Identificando alimentos..."
  - "Estimando porções..."
  - "Calculando nutrientes..."
- Tempo alvo: < 3 segundos

### 3.8 Resultado da Análise IA
Tela de resultado pós-análise:

#### Header
- Foto da refeição (miniatura)
- Tipo de refeição detectado (ou seletor: Café/Almoço/Janta/Lanche)

#### Lista de Alimentos Identificados
Para cada alimento:
- Nome do alimento (ex: "Arroz branco")
- Porção estimada (ex: "150g — 1 colher de servir")
- Calorias
- Macros: P / C / G
- Ícone de editar (lápis) — permite ajustar porção
- Ícone de remover (X)
- Confiança da IA (indicador visual: verde/amarelo/vermelho)

#### Totais
- Total de calorias da refeição
- Total de cada macro
- Botão "Adicionar mais alimentos" (busca manual)
- Botão "Salvar refeição" (CTA principal, verde)

#### Ajuste de Porção (Modal)
- Slider para ajustar gramas
- Opções rápidas: "Metade", "Dobro", "Porção padrão"
- Atualiza calorias/macros em tempo real

### 3.9 Busca Manual de Alimentos
- Campo de busca com autocomplete
- Fonte: banco TACO/TBCA + alimentos customizados do usuário
- Filtros:
  - Favoritos
  - Recentes
  - Categorias (Frutas, Carnes, Grãos, Laticínios, etc.)
- Cada resultado mostra:
  - Nome do alimento
  - Calorias por 100g
  - Macro resumo
- Ao selecionar:
  - Tela de definir porção (gramas ou medida caseira)
  - Medidas caseiras brasileiras: colher de sopa, concha, escumadeira, xícara, unidade

### 3.10 Scanner de Código de Barras
- Câmera com overlay de scan area
- Lê código EAN-13 (padrão brasileiro)
- Busca em:
  1. Base interna PratoIA
  2. Open Food Facts API
  3. Se não encontrado: tela para cadastrar manualmente
- Resultado: card do produto com info nutricional
- Definir porção e salvar

### 3.11 Diário Alimentar / Meal Log
- Visualização dia a dia
- Swipe horizontal para navegar entre dias
- Calendário selecionável (topo)
- Para cada dia:
  - Lista de refeições com alimentos
  - Totais do dia
  - Gráfico de pizza (macros)
- Opção de copiar refeição de outro dia
- Opção de duplicar refeição

### 3.12 Estatísticas (Stats)
#### Visão Semanal
- Gráfico de barras: calorias por dia (7 dias)
- Linha de meta calórica
- Média semanal de calorias
- Média semanal de cada macro
- Aderência à meta (%)

#### Visão Mensal
- Calendário heat map (cores por aderência)
- Gráfico de linha: peso ao longo do tempo
- Gráfico de barras: macros médios por semana
- Tendências: "Você está consumindo 15% mais proteína esta semana"

#### Visão por Nutriente
- Drill-down em qualquer macro
- Top alimentos consumidos (ranking)
- Distribuição por refeição

### 3.13 Perfil & Configurações
#### Perfil
- Foto do usuário
- Nome
- Email
- Dados físicos (editáveis): peso atual, altura, idade
- Meta de peso
- Nível de atividade
- Objetivo (emagrecer/manter/ganhar)

#### Configurações
- **Metas nutricionais**: editar calorias e macros manualmente
- **Notificações**: ligar/desligar por tipo
- **Unidades**: kg/lb, cm/ft (padrão: métrico)
- **Tema**: Claro / Escuro / Automático
- **Idioma**: Português (BR) — fixo no MVP
- **Conta**: alterar email, senha
- **Assinatura**: gerenciar plano
- **Integrações**: Apple Health, Google Fit, Samsung Health
- **Dados**: exportar dados (CSV), excluir conta
- **LGPD**: política de privacidade, solicitar dados, consentimentos
- **Sobre**: versão, termos de uso
- **Feedback**: enviar feedback/sugestão

### 3.14 Configuração de Metas
- Tela acessível pelo perfil ou onboarding
- Meta calórica diária (calculada ou manual)
- Distribuição de macros:
  - Presets: Equilibrada, Low Carb, High Protein, Keto, Custom
  - Ou percentuais customizados
- Meta de água (ml/dia)
- Meta de peso (kg)
- Prazo estimado para atingir meta

### 3.15 Receitas / Recipe Builder
- Criar receita customizada:
  - Nome da receita
  - Ingredientes (busca + quantidade)
  - Número de porções
  - Foto (opcional)
  - Calorias/macros calculados automaticamente por porção
- Salvar receita
- Listar receitas salvas
- Usar receita como entrada rápida no log

### 3.16 Rastreamento de Água
- Widget no dashboard
- Tela dedicada com:
  - Meta diária (padrão: 2000ml, configurável)
  - Botões rápidos: +200ml, +300ml, +500ml
  - Quantidade customizada
  - Histórico do dia (lista de entradas)
  - Gráfico semanal

### 3.17 Registro de Exercícios
- Busca de exercícios (base pré-carregada)
- Categorias: Cardio, Musculação, Funcional, Esportes, Yoga, Outros
- Para cada exercício:
  - Duração (minutos)
  - Intensidade (leve, moderada, intensa)
  - Calorias queimadas (calculado ou manual)
- Integração com Apple Health / Google Fit para importar automaticamente

### 3.18 Integrações
- Apple Health (iOS): importar/exportar peso, exercícios, passos
- Google Fit (Android): importar/exportar peso, exercícios, passos
- Samsung Health: idem
- Smartwatches: via Health APIs
- Exportar dados em CSV

### 3.19 Bottom Navigation Bar
5 itens:
1. 🏠 **Início** — Dashboard
2. 📊 **Stats** — Estatísticas
3. 📸 **Câmera** (FAB central, elevado) — Captura de foto
4. 📖 **Diário** — Meal Log
5. 👤 **Perfil** — Configurações e perfil

---

## 4. Onboarding Detalhado

Fluxo sequencial de 12 etapas. Progress bar no topo. Cada etapa = 1 tela.

### Etapa 1: Boas-Vindas
- Logo PratoIA
- Título: "Bem-vindo ao PratoIA"
- Subtítulo: "O jeito mais fácil de controlar sua alimentação"
- Animação de uma foto sendo tirada de um prato → resultado com macros
- Botão: "Começar" (CTA verde)

### Etapa 2: Sexo Biológico
- Pergunta: "Qual seu sexo biológico?"
- Opções (cards visuais):
  - 👨 Masculino
  - 👩 Feminino
  - ⚧ Outro / Prefiro não dizer
- Usado para cálculo de TMB (Harris-Benedict / Mifflin-St Jeor)

### Etapa 3: Data de Nascimento
- Pergunta: "Qual sua data de nascimento?"
- Date picker (scroll wheels estilo iOS)
- Formato: DD/MM/AAAA
- Validação: idade mínima 13 anos

### Etapa 4: Altura
- Pergunta: "Qual sua altura?"
- Slider ou scroll picker
- Unidade: cm (padrão BR)
- Range: 100cm — 250cm
- Toggle para pés/polegadas

### Etapa 5: Peso Atual
- Pergunta: "Qual seu peso atual?"
- Scroll picker com decimais (0.1 kg)
- Unidade: kg (padrão BR)
- Range: 30kg — 300kg
- Toggle para libras

### Etapa 6: Objetivo
- Pergunta: "Qual seu objetivo?"
- Opções (cards visuais com ícones):
  - 📉 Perder peso
  - ⚖️ Manter peso
  - 📈 Ganhar massa muscular
- Animação de feedback ao selecionar

### Etapa 7: Peso Desejado (se objetivo ≠ manter)
- Pergunta: "Qual seu peso desejado?"
- Mesmo picker da etapa 5
- Validação:
  - Se perder peso: peso desejado < peso atual
  - Se ganhar: peso desejado > peso atual
- Mostra diferença: "Você quer [perder/ganhar] X kg"

### Etapa 8: Nível de Atividade Física
- Pergunta: "Qual seu nível de atividade?"
- Opções (cards com descrição):
  - 🛋️ **Sedentário** — Pouco ou nenhum exercício
  - 🚶 **Levemente ativo** — 1-3 dias/semana
  - 🏃 **Moderadamente ativo** — 3-5 dias/semana
  - 💪 **Muito ativo** — 6-7 dias/semana
  - 🏋️ **Extremamente ativo** — Atleta, 2x/dia
- Fator multiplicador para TDEE

### Etapa 9: Velocidade de Progresso
- Pergunta: "Qual velocidade você prefere?"
- Opções:
  - 🐢 **Devagar** — 0.25 kg/semana (mais sustentável)
  - 🐇 **Moderado** — 0.5 kg/semana (recomendado) ✨
  - 🚀 **Rápido** — 0.75-1 kg/semana (mais agressivo)
- Info: "Recomendamos a velocidade moderada para resultados sustentáveis"

### Etapa 10: Preferências Alimentares
- Pergunta: "Você segue alguma dieta específica?"
- Opções (multi-select, chips):
  - Sem restrições
  - Vegetariano
  - Vegano
  - Low Carb
  - Keto
  - Sem Glúten
  - Sem Lactose
  - Halal
  - Outro
- Afeta sugestões e filtros de busca

### Etapa 11: Restrições / Alergias
- Pergunta: "Tem alguma alergia alimentar?"
- Opções (multi-select, chips):
  - Nenhuma
  - Amendoim
  - Castanhas (tree nuts)
  - Leite
  - Ovos
  - Soja
  - Trigo/Glúten
  - Frutos do mar
  - Peixes
  - Outra (campo de texto)

### Etapa 12: Resultado Personalizado
- Título: "Seu plano está pronto! 🎉"
- Card com resumo:
  - Meta calórica diária: {X} kcal
  - Proteína: {X}g | Carboidrato: {X}g | Gordura: {X}g
  - Previsão: "Você atingirá {peso_desejado}kg em ~{X} semanas"
- Gráfico de projeção (linha descendente/ascendente)
- Botão: "Começar a usar o PratoIA" (CTA)

### Cálculos do Onboarding

#### TMB (Taxa Metabólica Basal) — Mifflin-St Jeor
```
Homens:  TMB = 10 × peso(kg) + 6.25 × altura(cm) - 5 × idade - 5 + 161
Mulheres: TMB = 10 × peso(kg) + 6.25 × altura(cm) - 5 × idade - 161
```

#### TDEE (Gasto Energético Total Diário)
```
TDEE = TMB × fator_atividade

Sedentário: 1.2
Levemente ativo: 1.375
Moderadamente ativo: 1.55
Muito ativo: 1.725
Extremamente ativo: 1.9
```

#### Meta Calórica
```
Perder peso (0.5kg/sem): TDEE - 500 kcal
Manter peso: TDEE
Ganhar massa (0.5kg/sem): TDEE + 300-500 kcal
```

#### Distribuição de Macros (Padrão Equilibrada)
```
Proteína: 30% das calorias → calorias × 0.30 / 4
Carboidrato: 40% das calorias → calorias × 0.40 / 4
Gordura: 30% das calorias → calorias × 0.30 / 9
```

---

## 5. Feature Matrix

| # | Feature | Cal AI | PratoIA | Prioridade | Notas |
|---|---------|--------|---------|------------|-------|
| 1 | Foto → análise calórica | IA com depth sensor | Gemini Flash 2.0 + fallback GPT-4.1 nano | **MVP** | Core feature |
| 2 | Identificação de alimentos | Multi-item detection | Idem via prompt engineering | **MVP** | |
| 3 | Estimativa de porção | Depth sensor + IA | IA visual (sem depth sensor) | **MVP** | Estimativa por referência visual |
| 4 | Calorias + Macros (P/C/G) | Sim | Sim | **MVP** | |
| 5 | Diário alimentar | Sim | Sim | **MVP** | |
| 6 | Dashboard com progresso | Sim | Sim | **MVP** | |
| 7 | Busca manual de alimentos | Base USDA/genérica | Base TACO/TBCA | **MVP** | Adaptação brasileira |
| 8 | Medidas caseiras | Cups, tablespoons | Colher de sopa, concha, xícara | **MVP** | Localizado para BR |
| 9 | Onboarding completo | 10-12 etapas | 12 etapas (ver seção 4) | **MVP** | |
| 10 | Cálculo automático de metas | TMB + TDEE | Idem (Mifflin-St Jeor) | **MVP** | |
| 11 | Registro de peso | Sim | Sim | **MVP** | |
| 12 | Gráfico semanal de calorias | Sim | Sim | **MVP** | |
| 13 | Scanner de código de barras | Sim | Open Food Facts + base própria | **MVP** | |
| 14 | Autenticação (email/social) | Sim | Supabase Auth (email, Google, Apple) | **MVP** | |
| 15 | Push notifications | Sim | FCM | **MVP** | Lembretes de refeição |
| 16 | Rastreamento de água | Sim | Sim | **MVP** | |
| 17 | Refeições favoritas/recentes | Sim | Sim | **MVP** | |
| 18 | Copiar refeição | Sim | Sim | **MVP** | |
| 19 | Dark mode | Sim | Sim | **MVP** | |
| 20 | Análise de foto da galeria | Sim | Sim | **MVP** | |
| 21 | Editar porção pós-análise | Sim | Slider + medidas caseiras | **MVP** | |
| 22 | Receitas customizadas | Sim | Sim | **v1.1** | |
| 23 | Registro de exercícios | Sim | Sim | **v1.1** | |
| 24 | Integração Apple Health | Sim | Sim | **v1.1** | |
| 25 | Integração Google Fit | Sim | Sim | **v1.1** | |
| 26 | Estatísticas mensais | Sim | Sim | **v1.1** | |
| 27 | Gráfico de peso ao longo do tempo | Sim | Sim | **v1.1** | |
| 28 | Micronutrientes (vitaminas, minerais) | Parcial | TACO tem dados completos | **v1.1** | Diferencial! |
| 29 | Paywall / Assinatura | Sim | RevenueCat | **v1.1** | MVP gratuito |
| 30 | Streaks e gamificação | Sim | Sim | **v1.1** | |
| 31 | Exportar dados CSV | Sim | Sim | **v1.1** | |
| 32 | Alimentos customizados (criar) | Sim | Sim | **v1.1** | |
| 33 | Análise de texto (descrever refeição) | Sim | Gemini prompt | **v2.0** | "Comi arroz, feijão e bife" |
| 34 | Widget home screen | Sim | Flutter widget | **v2.0** | |
| 35 | Apple Watch / Wear OS | Sim | Flutter companion | **v2.0** | |
| 36 | Plano alimentar IA | Não | Diferencial PratoIA | **v2.0** | Geração de cardápio |
| 37 | Social/Compartilhar | Parcial | Share card com resumo | **v2.0** | |
| 38 | Multi-idioma | Inglês | Português BR (+ EN futuro) | **v2.0** | |
| 39 | Integração com nutricionista | Não | Compartilhar diário | **v2.0** | Diferencial BR |
| 40 | Scanner de rótulo nutricional | Parcial | OCR de tabela nutricional | **v2.0** | |

---

## 6. Especificações UI/UX

### 6.1 Design Language
- **Estilo:** Clean, minimalista, moderno — similar ao Cal AI
- **Inspiração:** Cal AI, MyFitnessPal, Yazio
- **Cantos:** Arredondados (border-radius: 16px para cards, 12px para botões)
- **Sombras:** Sutis, elevation baixa (2-4dp)
- **Espaçamento:** Sistema de 8px grid

### 6.2 Paleta de Cores

#### Light Mode
| Elemento | Cor | Hex |
|----------|-----|-----|
| Primary (CTA, destaques) | Verde vibrante | `#2ECC71` |
| Primary Dark | Verde escuro | `#27AE60` |
| Secondary | Azul suave | `#3498DB` |
| Background | Branco off-white | `#F8F9FA` |
| Surface (cards) | Branco puro | `#FFFFFF` |
| Text Primary | Quase preto | `#1A1A2E` |
| Text Secondary | Cinza médio | `#6C757D` |
| Proteína | Azul | `#3498DB` |
| Carboidrato | Laranja | `#F39C12` |
| Gordura | Amarelo | `#F1C40F` |
| Calorias | Verde | `#2ECC71` |
| Erro | Vermelho | `#E74C3C` |
| Sucesso | Verde | `#27AE60` |
| Warning | Amarelo | `#F39C12` |

#### Dark Mode
| Elemento | Cor | Hex |
|----------|-----|-----|
| Background | Escuro profundo | `#0D1117` |
| Surface (cards) | Cinza escuro | `#161B22` |
| Text Primary | Branco | `#F0F6FC` |
| Text Secondary | Cinza claro | `#8B949E` |
| Primary | Verde (mesmo) | `#2ECC71` |
| Demais cores | Mesmas, com opacidade ajustada | — |

### 6.3 Tipografia
| Uso | Font | Peso | Tamanho |
|-----|------|------|---------|
| Display / Números grandes | Inter ou SF Pro | Bold (700) | 32-48px |
| Título de seção | Inter | SemiBold (600) | 20-24px |
| Subtítulo | Inter | Medium (500) | 16-18px |
| Body text | Inter | Regular (400) | 14-16px |
| Caption / Labels | Inter | Regular (400) | 12px |
| Números (calorias/macros) | Inter | Bold (700) | Variável |

### 6.4 Componentes

#### Cards
- Background: Surface color
- Border radius: 16px
- Padding: 16px
- Shadow: `0 2px 8px rgba(0,0,0,0.08)`

#### Botões
- **Primary CTA:** Background verde, texto branco, border-radius 12px, height 56px
- **Secondary:** Outline verde, background transparente
- **Text button:** Sem background, texto verde
- **Disabled:** Opacidade 40%

#### Inputs
- Border radius: 12px
- Border: 1px solid `#E0E0E0`
- Focus border: verde primary
- Height: 52px
- Padding horizontal: 16px

#### Progress Indicators
- **Circular (calorias):** Stroke width 12px, animação de preenchimento
- **Linear (macros):** Height 8px, border-radius 4px, animação de progresso
- **Onboarding:** Dots ou barra contínua no topo

### 6.5 Animações e Transições
| Contexto | Tipo | Duração | Curve |
|----------|------|---------|-------|
| Navegação entre telas | Slide horizontal | 300ms | easeInOut |
| Onboarding | Slide horizontal | 300ms | easeInOut |
| Cards aparecendo | Fade + slide up | 200ms | easeOut |
| Progresso calórico | Animação circular | 800ms | easeInOut |
| Barras de macro | Preenchimento animado | 600ms | easeOut |
| FAB press | Scale down 0.95 | 100ms | easeIn |
| Modal bottom sheet | Slide up | 250ms | decelerate |
| Loading (análise IA) | Shimmer/pulse | Loop | linear |
| Sucesso (salvar) | Check mark Lottie | 500ms | — |
| Splash logo | Fade in + scale | 500ms | easeOut |

### 6.6 Ícones
- Biblioteca: **Phosphor Icons** ou **Lucide** (line style)
- Consistência: todos line, stroke 1.5-2px
- Tamanho padrão: 24px (bottom nav: 28px)

### 6.7 Ilustrações
- Estilo: Flat/minimal com paleta de cores do app
- Uso: onboarding, estados vazios, erros
- Opção: Undraw.co ou ilustrações customizadas

---

## 7. Integração com IA

### 7.1 Fluxo Técnico de Análise de Foto

```
[Usuário tira foto]
        ↓
[Upload para Supabase Storage]
        ↓
[Frontend envia request para API]
  POST /api/v1/meals/analyze
  Body: { image_url, meal_type?, user_id }
        ↓
[Backend → Gemini Flash 2.0]
  - Envia imagem + prompt estruturado
  - Timeout: 10s
        ↓
  [Sucesso?]
    Sim → Parse resposta JSON → retornar
    Não → [Fallback: GPT-4.1 nano]
            - Mesmo prompt, mesma imagem
            - Timeout: 15s
            ↓
            [Sucesso?]
              Sim → Parse → retornar
              Não → Retornar erro ao usuário
        ↓
[Frontend recebe resultado]
  - Exibe alimentos identificados
  - Usuário edita/confirma
  - Salva no banco
```

### 7.2 Prompt Engineering

#### System Prompt (Gemini/GPT)
```
Você é um nutricionista especializado em análise visual de alimentos brasileiros. 
Analise a foto de refeição fornecida e identifique TODOS os alimentos visíveis.

Para cada alimento:
1. Identifique o nome em português brasileiro
2. Estime a porção em gramas baseado no tamanho visual relativo ao prato/recipiente
3. Calcule os valores nutricionais usando a tabela TACO/TBCA como referência
4. Atribua um nível de confiança (high/medium/low)

Regras:
- Use nomes comuns brasileiros (ex: "mandioca" não "cassava")
- Considere preparações típicas brasileiras (ex: arroz é geralmente branco cozido)
- Estime porções usando referências visuais (prato padrão = 26cm diâmetro)
- Priorize precisão sobre completude
- Se não conseguir identificar um item, liste como "Alimento não identificado" com confiança "low"
- Considere molhos, temperos e acompanhamentos visíveis
- Arroz e feijão juntos são muito comuns em pratos brasileiros

Responda SOMENTE com o JSON no formato especificado, sem texto adicional.
```

#### User Prompt
```
Analise esta foto de refeição. 
Tipo de refeição: {meal_type ou "detectar automaticamente"}
Contexto adicional: {user_notes se houver}
```

### 7.3 Formato de Resposta (JSON Schema)

```json
{
  "meal_analysis": {
    "meal_type": "almoco",
    "confidence": "high",
    "total_calories": 650,
    "total_protein": 35.2,
    "total_carbs": 78.5,
    "total_fat": 18.3,
    "total_fiber": 8.2,
    "items": [
      {
        "name": "Arroz branco cozido",
        "taco_id": "101",
        "portion_grams": 150,
        "portion_description": "1 colher de servir cheia",
        "confidence": "high",
        "calories": 193,
        "protein": 3.8,
        "carbs": 42.0,
        "fat": 0.5,
        "fiber": 0.6,
        "alternatives": [
          {
            "name": "Arroz integral cozido",
            "taco_id": "102",
            "calories": 172
          }
        ]
      },
      {
        "name": "Feijão carioca cozido",
        "taco_id": "201",
        "portion_grams": 120,
        "portion_description": "1 concha média",
        "confidence": "high",
        "calories": 94,
        "protein": 6.2,
        "carbs": 16.8,
        "fat": 0.6,
        "fiber": 5.8,
        "alternatives": []
      }
    ],
    "warnings": [],
    "suggestions": [
      "Considere adicionar uma fonte de vitamina C para melhorar a absorção do ferro do feijão"
    ]
  }
}
```

### 7.4 Error Handling

| Cenário | Ação | UX |
|---------|------|----|
| Foto escura/borrada | Retornar erro de qualidade | "A foto está escura. Tente novamente com melhor iluminação" |
| Nenhum alimento detectado | Retornar lista vazia | "Não conseguimos identificar alimentos. Tente tirar a foto mais de perto" |
| Confiança baixa (< 50%) | Flag visual amarelo/vermelho | Item marcado com "⚠️ Verificar" e sugestão de alternativas |
| Timeout Gemini | Fallback para GPT-4.1 nano | Loading continua, sem percepção do usuário |
| Timeout ambas IAs | Erro genérico | "Não foi possível analisar. Tente novamente ou adicione manualmente" |
| Alimento não-brasileiro | Buscar equivalente | Mapear nome internacional → TACO |
| Foto de não-alimento | Detectar e rejeitar | "Não identificamos alimentos nesta foto" |
| Rate limiting | Queue com retry | Loading com mensagem de espera |

### 7.5 Estimativa de Porção

#### Estratégia
Sem sensor de profundidade (LiDAR), usamos referências visuais:

1. **Prato como referência**: Prato padrão brasileiro = 26cm diâmetro
2. **Proporção visual**: Área do alimento relativa ao prato
3. **Referências comuns**:
   - Colher de sopa = ~15g (arroz), ~25g (feijão)
   - Concha = ~120g (feijão)
   - Escumadeira = ~100g (arroz)
   - Xícara = ~240ml
   - Copo = ~200ml
4. **Espessura**: Estimada pela aparência 3D (shadows, textura)
5. **Calibração**: Usuário pode informar tamanho do prato nas configurações

#### Prompt de Porção Adicional
```
Para estimar porções, considere:
- O prato padrão brasileiro tem ~26cm de diâmetro
- Use a proporção do alimento em relação ao prato
- Considere a altura/volume aparente do alimento
- Arredonde para a medida caseira mais próxima
- Na dúvida, estime para baixo (melhor subestimar)
```

### 7.6 Cache e Otimização
- Cache de análises idênticas (hash da imagem) por 24h
- Respostas comuns em cache local (arroz, feijão, salada)
- Compressão de imagem antes de enviar (máx 1MB, 1024px maior lado)
- Formato: JPEG quality 85%

---

## 8. Modelos de Dados

### 8.1 Entidades

#### User
```typescript
interface User {
  id: string;                    // UUID, PK
  email: string;                 // unique
  name: string;
  avatar_url: string | null;
  gender: 'male' | 'female' | 'other';
  birth_date: Date;
  height_cm: number;
  current_weight_kg: number;
  target_weight_kg: number | null;
  activity_level: 'sedentary' | 'light' | 'moderate' | 'active' | 'very_active';
  goal: 'lose' | 'maintain' | 'gain';
  goal_speed: 'slow' | 'moderate' | 'fast';
  dietary_preferences: string[];  // ['vegetarian', 'gluten_free', ...]
  allergies: string[];            // ['peanut', 'milk', ...]
  daily_calorie_goal: number;
  protein_goal_g: number;
  carbs_goal_g: number;
  fat_goal_g: number;
  water_goal_ml: number;         // default: 2000
  onboarding_completed: boolean;
  premium: boolean;
  premium_expires_at: Date | null;
  theme: 'light' | 'dark' | 'auto';
  notifications_enabled: boolean;
  created_at: Date;
  updated_at: Date;
}
```

#### MealLog
```typescript
interface MealLog {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  date: Date;                    // YYYY-MM-DD
  meal_type: 'breakfast' | 'morning_snack' | 'lunch' | 'afternoon_snack' | 'dinner' | 'supper';
  photo_url: string | null;
  ai_analysis_raw: object | null; // JSON bruto da IA
  total_calories: number;
  total_protein: number;
  total_carbs: number;
  total_fat: number;
  total_fiber: number;
  notes: string | null;
  created_at: Date;
  updated_at: Date;
}
```

#### MealItem
```typescript
interface MealItem {
  id: string;                    // UUID, PK
  meal_log_id: string;           // FK → meal_logs
  food_id: string | null;        // FK → foods (null se customizado)
  name: string;
  portion_grams: number;
  portion_description: string;   // "1 colher de servir"
  calories: number;
  protein: number;
  carbs: number;
  fat: number;
  fiber: number;
  source: 'ai' | 'manual' | 'barcode' | 'recipe' | 'favorite';
  ai_confidence: 'high' | 'medium' | 'low' | null;
  is_verified: boolean;          // usuário confirmou/editou
  created_at: Date;
}
```

#### Food (Base TACO)
```typescript
interface Food {
  id: string;                    // UUID, PK
  taco_id: string | null;        // ID original TACO
  barcode: string | null;        // EAN-13
  name: string;
  name_normalized: string;       // lowercase, sem acentos (busca)
  category: string;              // "Cereais", "Carnes", "Frutas", etc.
  portion_default_g: number;
  calories_per_100g: number;
  protein_per_100g: number;
  carbs_per_100g: number;
  fat_per_100g: number;
  fiber_per_100g: number;
  sodium_per_100g: number | null;
  // Micronutrientes TACO
  calcium_mg: number | null;
  iron_mg: number | null;
  vitamin_c_mg: number | null;
  vitamin_a_mcg: number | null;
  // ... demais micronutrientes TACO
  household_measures: HouseholdMeasure[];
  source: 'taco' | 'open_food_facts' | 'user' | 'admin';
  is_verified: boolean;
  created_at: Date;
}

interface HouseholdMeasure {
  name: string;       // "colher de sopa", "concha média", "fatia"
  grams: number;      // equivalência em gramas
}
```

#### WaterLog
```typescript
interface WaterLog {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  date: Date;
  amount_ml: number;
  created_at: Date;
}
```

#### WeightLog
```typescript
interface WeightLog {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  date: Date;
  weight_kg: number;
  notes: string | null;
  created_at: Date;
}
```

#### ExerciseLog
```typescript
interface ExerciseLog {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  date: Date;
  exercise_name: string;
  category: 'cardio' | 'strength' | 'flexibility' | 'sports' | 'other';
  duration_minutes: number;
  intensity: 'light' | 'moderate' | 'intense';
  calories_burned: number;
  source: 'manual' | 'health_kit' | 'google_fit';
  created_at: Date;
}
```

#### Recipe
```typescript
interface Recipe {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  name: string;
  servings: number;
  photo_url: string | null;
  calories_per_serving: number;
  protein_per_serving: number;
  carbs_per_serving: number;
  fat_per_serving: number;
  ingredients: RecipeIngredient[];
  created_at: Date;
  updated_at: Date;
}

interface RecipeIngredient {
  food_id: string;               // FK → foods
  food_name: string;
  portion_grams: number;
  portion_description: string;
}
```

#### UserStreak
```typescript
interface UserStreak {
  id: string;                    // UUID, PK
  user_id: string;               // FK → users
  current_streak: number;        // dias consecutivos
  longest_streak: number;
  last_log_date: Date;
  updated_at: Date;
}
```

#### Achievement
```typescript
interface Achievement {
  id: string;
  key: string;                   // 'first_meal', 'streak_7', 'streak_30', etc.
  name: string;
  description: string;
  icon: string;
  requirement_type: string;
  requirement_value: number;
}

interface UserAchievement {
  id: string;
  user_id: string;
  achievement_id: string;
  unlocked_at: Date;
}
```

---

## 9. API Endpoints

### Base URL: `https://api.pratoia.com.br/v1`

### Autenticação
Todos os endpoints (exceto auth) requerem header: `Authorization: Bearer {supabase_jwt}`

### Endpoints

#### Auth (via Supabase Auth — não precisa de API custom)
- `POST /auth/signup` — Criar conta
- `POST /auth/login` — Login
- `POST /auth/logout` — Logout
- `POST /auth/forgot-password` — Reset password
- `POST /auth/oauth/{provider}` — OAuth (Google, Apple)

#### Users
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/users/me` | Perfil do usuário logado |
| PUT | `/users/me` | Atualizar perfil |
| PUT | `/users/me/onboarding` | Salvar dados do onboarding |
| PUT | `/users/me/goals` | Atualizar metas nutricionais |
| DELETE | `/users/me` | Excluir conta (LGPD) |
| GET | `/users/me/export` | Exportar dados (LGPD) |

#### Meals (Refeições)
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/meals/analyze` | Enviar foto para análise IA |
| POST | `/meals` | Salvar refeição (com itens) |
| GET | `/meals?date={YYYY-MM-DD}` | Listar refeições do dia |
| GET | `/meals/{id}` | Detalhe de uma refeição |
| PUT | `/meals/{id}` | Editar refeição |
| DELETE | `/meals/{id}` | Excluir refeição |
| POST | `/meals/{id}/copy` | Copiar refeição para outro dia/tipo |

#### Meal Items
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/meals/{meal_id}/items` | Adicionar item à refeição |
| PUT | `/meals/{meal_id}/items/{item_id}` | Editar item |
| DELETE | `/meals/{meal_id}/items/{item_id}` | Remover item |

#### Foods (Alimentos)
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/foods/search?q={query}` | Buscar alimento (TACO + custom) |
| GET | `/foods/{id}` | Detalhe do alimento |
| GET | `/foods/barcode/{code}` | Buscar por código de barras |
| POST | `/foods` | Criar alimento customizado |
| GET | `/foods/categories` | Listar categorias |
| GET | `/foods/recent` | Alimentos recentes do usuário |
| GET | `/foods/favorites` | Favoritos do usuário |
| POST | `/foods/{id}/favorite` | Favoritar/desfavoritar |

#### Water
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/water` | Registrar água |
| GET | `/water?date={YYYY-MM-DD}` | Total do dia |
| DELETE | `/water/{id}` | Remover entrada |

#### Weight
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/weight` | Registrar peso |
| GET | `/weight?from={date}&to={date}` | Histórico de peso |
| DELETE | `/weight/{id}` | Remover entrada |

#### Exercise
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/exercises` | Registrar exercício |
| GET | `/exercises?date={YYYY-MM-DD}` | Exercícios do dia |
| DELETE | `/exercises/{id}` | Remover exercício |
| GET | `/exercises/catalog` | Catálogo de exercícios |

#### Recipes
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| POST | `/recipes` | Criar receita |
| GET | `/recipes` | Listar receitas do usuário |
| GET | `/recipes/{id}` | Detalhe da receita |
| PUT | `/recipes/{id}` | Editar receita |
| DELETE | `/recipes/{id}` | Excluir receita |

#### Stats
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| GET | `/stats/daily?date={YYYY-MM-DD}` | Resumo do dia |
| GET | `/stats/weekly?date={YYYY-MM-DD}` | Resumo semanal |
| GET | `/stats/monthly?month={YYYY-MM}` | Resumo mensal |
| GET | `/stats/streak` | Dados de streak |

#### Notifications
| Método | Endpoint | Descrição |
|--------|----------|-----------|
| PUT | `/notifications/preferences` | Configurar preferências |
| POST | `/notifications/token` | Registrar FCM token |

---

## 10. Estrutura Supabase

### 10.1 Tabelas SQL

```sql
-- ============================================
-- USERS
-- ============================================
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  auth_id UUID UNIQUE REFERENCES auth.users(id) ON DELETE CASCADE,
  email TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  avatar_url TEXT,
  gender TEXT CHECK (gender IN ('male', 'female', 'other')),
  birth_date DATE,
  height_cm NUMERIC(5,1),
  current_weight_kg NUMERIC(5,1),
  target_weight_kg NUMERIC(5,1),
  activity_level TEXT CHECK (activity_level IN ('sedentary', 'light', 'moderate', 'active', 'very_active')),
  goal TEXT CHECK (goal IN ('lose', 'maintain', 'gain')),
  goal_speed TEXT CHECK (goal_speed IN ('slow', 'moderate', 'fast')),
  dietary_preferences TEXT[] DEFAULT '{}',
  allergies TEXT[] DEFAULT '{}',
  daily_calorie_goal INTEGER DEFAULT 2000,
  protein_goal_g INTEGER DEFAULT 150,
  carbs_goal_g INTEGER DEFAULT 200,
  fat_goal_g INTEGER DEFAULT 67,
  water_goal_ml INTEGER DEFAULT 2000,
  onboarding_completed BOOLEAN DEFAULT FALSE,
  premium BOOLEAN DEFAULT FALSE,
  premium_expires_at TIMESTAMPTZ,
  theme TEXT DEFAULT 'auto' CHECK (theme IN ('light', 'dark', 'auto')),
  notifications_enabled BOOLEAN DEFAULT TRUE,
  fcm_token TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- ============================================
-- FOODS (Base TACO + customizados)
-- ============================================
CREATE TABLE foods (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  taco_id TEXT,
  barcode TEXT,
  name TEXT NOT NULL,
  name_normalized TEXT NOT NULL, -- para busca full-text
  category TEXT NOT NULL,
  portion_default_g NUMERIC(7,1) DEFAULT 100,
  calories_per_100g NUMERIC(7,1) NOT NULL,
  protein_per_100g NUMERIC(7,2) NOT NULL,
  carbs_per_100g NUMERIC(7,2) NOT NULL,
  fat_per_100g NUMERIC(7,2) NOT NULL,
  fiber_per_100g NUMERIC(7,2) DEFAULT 0,
  sodium_per_100g NUMERIC(7,2),
  calcium_mg NUMERIC(7,2),
  iron_mg NUMERIC(7,2),
  vitamin_c_mg NUMERIC(7,2),
  vitamin_a_mcg NUMERIC(7,2),
  potassium_mg NUMERIC(7,2),
  magnesium_mg NUMERIC(7,2),
  zinc_mg NUMERIC(7,2),
  household_measures JSONB DEFAULT '[]',
  source TEXT DEFAULT 'taco' CHECK (source IN ('taco', 'open_food_facts', 'user', 'admin')),
  created_by UUID REFERENCES users(id),
  is_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_foods_name_normalized ON foods USING gin(to_tsvector('portuguese', name_normalized));
CREATE INDEX idx_foods_barcode ON foods(barcode) WHERE barcode IS NOT NULL;
CREATE INDEX idx_foods_category ON foods(category);
CREATE INDEX idx_foods_taco_id ON foods(taco_id) WHERE taco_id IS NOT NULL;

-- ============================================
-- MEAL_LOGS
-- ============================================
CREATE TABLE meal_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  meal_type TEXT NOT NULL CHECK (meal_type IN (
    'breakfast', 'morning_snack', 'lunch', 'afternoon_snack', 'dinner', 'supper'
  )),
  photo_url TEXT,
  ai_analysis_raw JSONB,
  ai_model_used TEXT, -- 'gemini_flash_2' ou 'gpt_4_1_nano'
  total_calories NUMERIC(7,1) DEFAULT 0,
  total_protein NUMERIC(7,2) DEFAULT 0,
  total_carbs NUMERIC(7,2) DEFAULT 0,
  total_fat NUMERIC(7,2) DEFAULT 0,
  total_fiber NUMERIC(7,2) DEFAULT 0,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_meal_logs_user_date ON meal_logs(user_id, date);

-- ============================================
-- MEAL_ITEMS
-- ============================================
CREATE TABLE meal_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  meal_log_id UUID NOT NULL REFERENCES meal_logs(id) ON DELETE CASCADE,
  food_id UUID REFERENCES foods(id),
  name TEXT NOT NULL,
  portion_grams NUMERIC(7,1) NOT NULL,
  portion_description TEXT,
  calories NUMERIC(7,1) NOT NULL,
  protein NUMERIC(7,2) NOT NULL,
  carbs NUMERIC(7,2) NOT NULL,
  fat NUMERIC(7,2) NOT NULL,
  fiber NUMERIC(7,2) DEFAULT 0,
  source TEXT DEFAULT 'manual' CHECK (source IN ('ai', 'manual', 'barcode', 'recipe', 'favorite')),
  ai_confidence TEXT CHECK (ai_confidence IN ('high', 'medium', 'low')),
  is_verified BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_meal_items_meal_log ON meal_items(meal_log_id);

-- ============================================
-- WATER_LOGS
-- ============================================
CREATE TABLE water_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  amount_ml INTEGER NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_water_logs_user_date ON water_logs(user_id, date);

-- ============================================
-- WEIGHT_LOGS
-- ============================================
CREATE TABLE weight_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  weight_kg NUMERIC(5,1) NOT NULL,
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_weight_logs_user_date ON weight_logs(user_id, date);

-- ============================================
-- EXERCISE_LOGS
-- ============================================
CREATE TABLE exercise_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  date DATE NOT NULL,
  exercise_name TEXT NOT NULL,
  category TEXT CHECK (category IN ('cardio', 'strength', 'flexibility', 'sports', 'other')),
  duration_minutes INTEGER NOT NULL,
  intensity TEXT CHECK (intensity IN ('light', 'moderate', 'intense')),
  calories_burned INTEGER NOT NULL,
  source TEXT DEFAULT 'manual' CHECK (source IN ('manual', 'health_kit', 'google_fit')),
  created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_exercise_logs_user_date ON exercise_logs(user_id, date);

-- ============================================
-- RECIPES
-- ============================================
CREATE TABLE recipes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  name TEXT NOT NULL,
  servings INTEGER NOT NULL DEFAULT 1,
  photo_url TEXT,
  ingredients JSONB NOT NULL DEFAULT '[]',
  calories_per_serving NUMERIC(7,1),
  protein_per_serving NUMERIC(7,2),
  carbs_per_serving NUMERIC(7,2),
  fat_per_serving NUMERIC(7,2),
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_recipes_user ON recipes(user_id);

-- ============================================
-- USER_FAVORITES (alimentos favoritos)
-- ============================================
CREATE TABLE user_favorites (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  food_id UUID NOT NULL REFERENCES foods(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, food_id)
);

-- ============================================
-- USER_STREAKS
-- ============================================
CREATE TABLE user_streaks (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  current_streak INTEGER DEFAULT 0,
  longest_streak INTEGER DEFAULT 0,
  last_log_date DATE,
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- ============================================
-- ACHIEVEMENTS
-- ============================================
CREATE TABLE achievements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  key TEXT UNIQUE NOT NULL,
  name TEXT NOT NULL,
  description TEXT NOT NULL,
  icon TEXT NOT NULL,
  requirement_type TEXT NOT NULL, -- 'streak', 'meals_logged', 'days_logged', etc.
  requirement_value INTEGER NOT NULL,
  sort_order INTEGER DEFAULT 0
);

CREATE TABLE user_achievements (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  achievement_id UUID NOT NULL REFERENCES achievements(id),
  unlocked_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, achievement_id)
);

-- ============================================
-- ROW LEVEL SECURITY (RLS)
-- ============================================
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE meal_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE meal_items ENABLE ROW LEVEL SECURITY;
ALTER TABLE water_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE weight_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE exercise_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE recipes ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_favorites ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_streaks ENABLE ROW LEVEL SECURITY;
ALTER TABLE user_achievements ENABLE ROW LEVEL SECURITY;

-- Políticas RLS: cada usuário vê/edita apenas seus dados
CREATE POLICY "Users can view own data" ON users
  FOR SELECT USING (auth.uid() = auth_id);

CREATE POLICY "Users can update own data" ON users
  FOR UPDATE USING (auth.uid() = auth_id);

CREATE POLICY "Users own meal_logs" ON meal_logs
  FOR ALL USING (user_id = (SELECT id FROM users WHERE auth_id = auth.uid()));

CREATE POLICY "Users own meal_items" ON meal_items
  FOR ALL USING (meal_log_id IN (
    SELECT id FROM meal_logs WHERE user_id = (SELECT id FROM users WHERE auth_id = auth.uid())
  ));

-- Repetir padrão para demais tabelas...

-- Foods: todos podem ler, apenas criador pode editar user-created
CREATE POLICY "Anyone can read foods" ON foods
  FOR SELECT USING (true);

CREATE POLICY "Users can create foods" ON foods
  FOR INSERT WITH CHECK (source = 'user' AND created_by = (SELECT id FROM users WHERE auth_id = auth.uid()));

-- ============================================
-- FUNCTIONS (Supabase Edge Functions / DB Functions)
-- ============================================

-- Função para calcular totais de um meal_log
CREATE OR REPLACE FUNCTION update_meal_totals()
RETURNS TRIGGER AS $$
BEGIN
  UPDATE meal_logs SET
    total_calories = (SELECT COALESCE(SUM(calories), 0) FROM meal_items WHERE meal_log_id = COALESCE(NEW.meal_log_id, OLD.meal_log_id)),
    total_protein = (SELECT COALESCE(SUM(protein), 0) FROM meal_items WHERE meal_log_id = COALESCE(NEW.meal_log_id, OLD.meal_log_id)),
    total_carbs = (SELECT COALESCE(SUM(carbs), 0) FROM meal_items WHERE meal_log_id = COALESCE(NEW.meal_log_id, OLD.meal_log_id)),
    total_fat = (SELECT COALESCE(SUM(fat), 0) FROM meal_items WHERE meal_log_id = COALESCE(NEW.meal_log_id, OLD.meal_log_id)),
    total_fiber = (SELECT COALESCE(SUM(fiber), 0) FROM meal_items WHERE meal_log_id = COALESCE(NEW.meal_log_id, OLD.meal_log_id)),
    updated_at = NOW()
  WHERE id = COALESCE(NEW.meal_log_id, OLD.meal_log_id);
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_update_meal_totals
AFTER INSERT OR UPDATE OR DELETE ON meal_items
FOR EACH ROW EXECUTE FUNCTION update_meal_totals();

-- Função para atualizar streak
CREATE OR REPLACE FUNCTION update_user_streak(p_user_id UUID, p_date DATE)
RETURNS void AS $$
DECLARE
  v_last_date DATE;
  v_current INTEGER;
  v_longest INTEGER;
BEGIN
  SELECT last_log_date, current_streak, longest_streak
  INTO v_last_date, v_current, v_longest
  FROM user_streaks WHERE user_id = p_user_id;

  IF NOT FOUND THEN
    INSERT INTO user_streaks (user_id, current_streak, longest_streak, last_log_date)
    VALUES (p_user_id, 1, 1, p_date);
    RETURN;
  END IF;

  IF v_last_date = p_date THEN
    RETURN; -- já logou hoje
  ELSIF v_last_date = p_date - 1 THEN
    v_current := v_current + 1;
  ELSE
    v_current := 1;
  END IF;

  IF v_current > v_longest THEN
    v_longest := v_current;
  END IF;

  UPDATE user_streaks SET
    current_streak = v_current,
    longest_streak = v_longest,
    last_log_date = p_date,
    updated_at = NOW()
  WHERE user_id = p_user_id;
END;
$$ LANGUAGE plpgsql;
```

### 10.2 Supabase Storage Buckets

| Bucket | Uso | Acesso |
|--------|-----|--------|
| `meal-photos` | Fotos de refeições | Privado (user own) |
| `avatars` | Fotos de perfil | Público |
| `recipe-photos` | Fotos de receitas | Privado (user own) |

### 10.3 Supabase Edge Functions

| Função | Trigger | Descrição |
|--------|---------|-----------|
| `analyze-meal` | HTTP POST | Processa foto via Gemini/GPT |
| `calculate-goals` | HTTP POST | Recalcula metas após onboarding |
| `daily-stats` | HTTP GET | Retorna resumo diário compilado |
| `send-notification` | Cron (schedule) | Envia push notifications |
| `export-data` | HTTP GET | Gera CSV para LGPD |

---

## 11. Gamificação & Engajamento

### 11.1 Sistema de Streaks

#### Regras
- Streak incrementa quando o usuário registra pelo menos 1 refeição no dia
- Streak reseta se um dia inteiro passar sem registro
- Streak não reseta por dias futuros (apenas passados)
- Grace period: nenhum (rigoroso, como Cal AI)

#### Visual
- 🔥 Ícone de chama no header do dashboard
- Número ao lado: "🔥 7"
- Animação de celebração ao atingir marcos (7, 14, 30, 60, 90, 365 dias)

### 11.2 Conquistas (Achievements)

| Key | Nome | Descrição | Requisito |
|-----|------|-----------|-----------|
| `first_meal` | Primeira Refeição | Registrou sua primeira refeição | 1 refeição |
| `first_photo` | Fotógrafo | Usou a câmera pela primeira vez | 1 foto analisada |
| `streak_3` | Consistência | 3 dias seguidos registrando | 3 dias streak |
| `streak_7` | Semana Completa | 7 dias seguidos | 7 dias streak |
| `streak_14` | Duas Semanas | 14 dias seguidos | 14 dias streak |
| `streak_30` | Mês de Fogo | 30 dias seguidos | 30 dias streak |
| `streak_90` | Trimestre Perfeito | 90 dias seguidos | 90 dias streak |
| `streak_365` | Um Ano Inteiro | 365 dias seguidos | 365 dias streak |
| `meals_10` | Explorador | 10 refeições registradas | 10 total |
| `meals_50` | Dedicado | 50 refeições registradas | 50 total |
| `meals_100` | Centenário | 100 refeições registradas | 100 total |
| `meals_500` | Mestre Chef | 500 refeições registradas | 500 total |
| `weight_first` | Na Balança | Primeiro registro de peso | 1 registro |
| `weight_goal` | Objetivo Alcançado! | Atingiu o peso desejado | peso = meta |
| `water_perfect` | Hidratado | Bateu a meta de água | 1 dia 100% |
| `water_week` | Semana Hidratada | 7 dias seguidos batendo meta de água | 7 dias |
| `macros_balanced` | Equilibrado | Ficou dentro de ±5% em todos os macros | 1 dia |
| `recipe_first` | Chef | Criou primeira receita | 1 receita |
| `barcode_first` | Scanner | Usou o scanner pela primeira vez | 1 scan |
| `all_meals_day` | Dia Completo | Registrou todas as refeições do dia | 4+ refeições |

### 11.3 Estratégia de Notificações

#### Tipos de Notificação

| Tipo | Horário | Frequência | Texto (PT-BR) |
|------|---------|------------|----------------|
| Lembrete café da manhã | 08:00 | Diário | "☀️ Bom dia! Não esqueça de registrar seu café da manhã" |
| Lembrete almoço | 12:30 | Diário | "🍽️ Hora do almoço! Tire uma foto do seu prato" |
| Lembrete jantar | 19:30 | Diário | "🌙 Boa noite! Registre seu jantar para manter a sequência" |
| Streak em risco | 20:00 | Diário (se não logou) | "🔥 Sua sequência de {X} dias está em risco! Registre algo antes de dormir" |
| Streak celebração | Ao logar | No marco | "🎉 Parabéns! {X} dias seguidos! Você é incrível!" |
| Conquista desbloqueada | Ao desbloquear | Único | "🏆 Conquista desbloqueada: {nome}!" |
| Lembrete de água | 15:00 | Diário | "💧 Já bebeu água hoje? Você está em {X}% da sua meta" |
| Resumo semanal | Domingo 10:00 | Semanal | "📊 Seu resumo semanal está pronto! Veja como você foi" |
| Peso | Sábado 09:00 | Semanal | "⚖️ Que tal se pesar hoje? Acompanhar o peso ajuda a manter o foco" |
| Re-engajamento (inativo 3d) | 10:00 | Único | "Sentimos sua falta! 😢 Volte e retome o controle da sua alimentação" |
| Re-engajamento (inativo 7d) | 10:00 | Único | "Faz uma semana! Que tal recomeçar hoje? Um prato de cada vez 💪" |

#### Configurações do Usuário
- Toggle global: ligar/desligar todas
- Toggle por tipo: lembretes de refeição, água, peso, conquistas
- Horário customizável para lembretes de refeição
- Modo silencioso: sem notificações entre 22:00-07:00

---

## 12. Localização Brasil

### 12.1 Categorias de Alimentos (TACO)

| Categoria | Exemplos |
|-----------|----------|
| Cereais e derivados | Arroz, pão francês, macarrão, farinha de mandioca, cuscuz |
| Verduras, hortaliças e derivados | Alface, tomate, cenoura, brócolis, couve |
| Frutas e derivados | Banana, maçã, laranja, manga, açaí |
| Gorduras e óleos | Azeite, óleo de soja, manteiga, margarina |
| Pescados e frutos do mar | Tilápia, sardinha, camarão, atum |
| Carnes e derivados | Frango, carne bovina, carne suína, linguiça |
| Leite e derivados | Leite, queijo minas, iogurte, requeijão |
| Bebidas | Café, suco de laranja, refrigerante, cerveja |
| Ovos e derivados | Ovo cozido, ovo frito, omelete |
| Produtos açucarados | Açúcar, mel, chocolate, doce de leite |
| Miscelâneas | Catchup, mostarda, maionese, molho de tomate |
| Outros alimentos industrializados | Biscoitos, salgadinhos, embutidos |
| Alimentos preparados | Feijoada, estrogonofe, moqueca |
| Leguminosas e derivados | Feijão carioca, feijão preto, lentilha, grão-de-bico |
| Nozes e sementes | Castanha-do-pará, amendoim, chia |
| Tubérculos, raízes e derivados | Batata, mandioca, inhame, batata-doce |

### 12.2 Refeições Brasileiras

| Código | Nome | Horário Típico | Ícone |
|--------|------|----------------|-------|
| `breakfast` | Café da Manhã | 06:00-10:00 | ☀️ |
| `morning_snack` | Lanche da Manhã | 10:00-11:30 | 🥤 |
| `lunch` | Almoço | 11:30-14:00 | 🍽️ |
| `afternoon_snack` | Lanche da Tarde | 14:00-17:00 | 🍎 |
| `dinner` | Jantar | 17:00-21:00 | 🌙 |
| `supper` | Ceia | 21:00-23:00 | 🌜 |

### 12.3 Medidas Caseiras Brasileiras

| Medida | Gramas (referência) |
|--------|---------------------|
| Colher de chá | 5g |
| Colher de sobremesa | 10g |
| Colher de sopa | 15g |
| Colher de sopa cheia | 25g |
| Colher de servir | 45g |
| Concha pequena | 80g |
| Concha média | 120g |
| Concha grande | 160g |
| Escumadeira | 100g |
| Xícara de chá | 240ml / variável em g |
| Copo americano | 200ml |
| Copo de requeijão | 250ml |
| Fatia (pão de forma) | 25g |
| Fatia fina (queijo) | 15g |
| Fatia média (queijo) | 30g |
| Unidade pequena | Variável |
| Unidade média | Variável |
| Unidade grande | Variável |
| Pedaço pequeno | Variável |
| Pedaço médio | Variável |
| Pedaço grande | Variável |
| Ponta de faca | 3g |

### 12.4 Considerações Culturais

- **Prato feito (PF)**: Arroz + feijão + carne + salada é a refeição padrão — IA deve reconhecer esse padrão
- **Açaí**: Extremamente popular, tratar como categoria especial com toppings
- **Churrasco**: Reconhecer diferentes cortes (picanha, maminha, fraldinha, etc.)
- **Comida de boteco**: Pastel, coxinha, bolinho de bacalhau
- **Café**: Café coado com açúcar é muito comum — perguntar se tem açúcar
- **Suco natural**: Muito mais comum que em outros países
- **Tapioca**: Item regional nordestino popular nacionalmente
- **Pão francês**: Diferente de "french bread" internacional — item específico BR
- **Marmita/Quentinha**: Conceito importante — refeição em recipiente de transporte

### 12.5 Pagamento — PIX (Futuro, pós-MVP)

Para v1.1+ com assinatura:
- **RevenueCat** para Apple App Store e Google Play (padrão)
- **Stripe** com PIX para pagamento direto (alternativa web)
- Considerar: PIX recorrente quando disponível
- Planos:
  - Semanal: R$9,90
  - Mensal: R$14,90
  - Anual: R$99,90

### 12.6 LGPD (Lei Geral de Proteção de Dados)

#### Obrigatório
1. **Consentimento explícito**: Checkbox no cadastro para aceitar termos e política de privacidade
2. **Política de Privacidade**: Documento acessível no app, descrevendo:
   - Quais dados são coletados
   - Como são usados
   - Com quem são compartilhados
   - Tempo de retenção
3. **Direito de acesso**: Botão "Exportar meus dados" nas configurações → gera CSV/JSON
4. **Direito de exclusão**: Botão "Excluir minha conta" → apaga todos os dados em até 48h
5. **Direito de retificação**: Usuário pode editar todos seus dados pessoais
6. **Consentimento para notificações**: Solicitação explícita antes de enviar push
7. **Consentimento para analytics**: Opt-in/opt-out para tracking
8. **DPO (Data Protection Officer)**: Email de contato visível na política

#### Dados Coletados
| Dado | Finalidade | Base Legal |
|------|-----------|------------|
| Email, nome | Autenticação e identificação | Consentimento |
| Dados físicos (peso, altura, etc.) | Cálculo de metas nutricionais | Consentimento |
| Fotos de refeições | Análise nutricional por IA | Consentimento |
| Registros alimentares | Histórico e estatísticas | Consentimento |
| Dados de uso (analytics) | Melhoria do produto | Legítimo interesse (opt-out) |
| FCM token | Envio de notificações | Consentimento |

#### Implementação Técnica
- Supabase RLS garante isolamento de dados por usuário
- Fotos armazenadas em bucket privado com acesso por signed URL (expira em 1h)
- Endpoint de exclusão: soft delete (30 dias) → hard delete
- Log de consentimentos em tabela separada
- Não compartilhar dados pessoais com terceiros (IA recebe apenas imagem, não dados do user)

---

## 13. Escopo MVP

### 13.1 MVP — Must Have para Lançamento

| # | Feature | Critério de Aceite |
|---|---------|-------------------|
| 1 | Cadastro/Login (email + Google + Apple) | Usuário consegue criar conta e fazer login com email ou OAuth |
| 2 | Onboarding completo (12 etapas) | Todas as etapas funcionam, dados salvos, metas calculadas automaticamente |
| 3 | Dashboard com resumo diário | Exibe calorias/macros do dia com progresso visual, lista de refeições |
| 4 | Câmera → Análise IA | Tirar foto → enviar para Gemini → receber lista de alimentos com calorias/macros em < 5s |
| 5 | Análise de foto da galeria | Selecionar foto existente e analisar |
| 6 | Resultado da análise editável | Usuário pode editar nome, porção, remover itens; macros recalculam |
| 7 | Salvar refeição | Refeição salva no banco com todos os itens e aparece no diário |
| 8 | Diário alimentar | Visualizar refeições por dia, navegar entre dias |
| 9 | Busca manual de alimentos | Buscar na base TACO por nome, selecionar porção em gramas ou medida caseira |
| 10 | Scanner de código de barras | Escanear EAN-13, buscar em Open Food Facts, exibir info nutricional |
| 11 | Rastreamento de água | Adicionar/remover água, meta diária, progresso visual |
| 12 | Registro de peso | Registrar peso com data, visualizar última entrada |
| 13 | Perfil editável | Editar todos os dados pessoais e metas |
| 14 | Configurações básicas | Tema (claro/escuro/auto), notificações on/off |
| 15 | Estatísticas semanais | Gráfico de barras de calorias dos últimos 7 dias |
| 16 | Alimentos recentes e favoritos | Favoritar alimentos, seção de recentes na busca |
| 17 | Push notifications (lembretes) | Lembretes de refeição (café, almoço, jantar) via FCM |
| 18 | Copiar refeição | Duplicar refeição de outro dia/tipo |
| 19 | Dark mode | Tema escuro completo |
| 20 | LGPD básico | Política de privacidade, exportar dados, excluir conta |

### 13.2 v1.1 — Pós-Lançamento (2-4 semanas)

| # | Feature |
|---|---------|
| 1 | Receitas customizadas (criar, salvar, usar como entrada rápida) |
| 2 | Registro de exercícios (catálogo + manual) |
| 3 | Integração Apple Health (peso, exercícios, passos) |
| 4 | Integração Google Fit |
| 5 | Estatísticas mensais (calendar heatmap, tendências) |
| 6 | Gráfico de evolução de peso |
| 7 | Streaks e conquistas (gamificação) |
| 8 | Alimentos customizados (criar) |
| 9 | Micronutrientes (dados TACO completos) |
| 10 | Paywall / assinatura (RevenueCat) |
| 11 | Exportar dados em CSV |
| 12 | Notificações inteligentes (streak em risco, resumo semanal) |

### 13.3 v2.0 — Futuro

| # | Feature |
|---|---------|
| 1 | Análise por texto ("comi arroz, feijão e bife") |
| 2 | Widget home screen (iOS/Android) |
| 3 | Apple Watch / Wear OS |
| 4 | Plano alimentar gerado por IA |
| 5 | Compartilhamento social (card com resumo) |
| 6 | Multi-idioma (inglês) |
| 7 | Compartilhar diário com nutricionista |
| 8 | Scanner de rótulo nutricional (OCR) |
| 9 | PIX como método de pagamento |
| 10 | Comunidade / social features |
| 11 | Integração com restaurantes (cardápios) |
| 12 | Modo offline completo |

---

## 14. Requisitos Não-Funcionais

### 14.1 Performance

| Métrica | Alvo | Aceitável |
|---------|------|-----------|
| Tempo de análise de foto (IA) | < 3s | < 5s |
| Tempo de carregamento do app (cold start) | < 2s | < 3s |
| Tempo de busca de alimentos | < 500ms | < 1s |
| Tempo de scan de barcode | < 2s | < 3s |
| Tamanho do APK/IPA | < 30MB | < 50MB |
| Uso de RAM | < 150MB | < 250MB |
| Frames por segundo (UI) | 60fps | 30fps mínimo |
| Tamanho de upload de foto (comprimida) | < 500KB | < 1MB |

### 14.2 Disponibilidade e Escalabilidade

| Requisito | Especificação |
|-----------|---------------|
| Uptime | 99.5% |
| Usuários simultâneos (MVP) | 1.000 |
| Usuários simultâneos (v1.1) | 10.000 |
| Latência API (P95) | < 500ms (exceto análise IA) |
| Rate limit por usuário | 60 requests/min |
| Rate limit análise IA | 10 análises/min por usuário |
| Backup do banco | Diário (Supabase automático) |

### 14.3 Offline

#### MVP
- Cache local de dados do dia atual (SQLite via Drift/Hive)
- Busca de alimentos funciona offline (base TACO embarcada ~2MB)
- Registro manual de alimentos funciona offline (sync ao reconectar)
- Análise de foto requer internet (mostrar mensagem)

#### v2.0
- Queue de fotos para análise quando reconectar
- Cache de dias recentes (últimos 7 dias)
- Sync bidirecional com resolução de conflitos

### 14.4 Segurança

| Requisito | Implementação |
|-----------|---------------|
| Autenticação | Supabase Auth (JWT) |
| Autorização | RLS (Row Level Security) por user_id |
| HTTPS | Obrigatório em todas as comunicações |
| Armazenamento sensível | Flutter Secure Storage para tokens |
| Fotos | Signed URLs com expiração (1h) |
| API Keys (Gemini, GPT) | Apenas no backend, nunca no client |
| Input validation | Sanitização no backend (zod/joi) |
| SQL Injection | Prepared statements (Supabase default) |
| XSS | Não aplicável (mobile nativo) |
| Certificate pinning | Implementar para API própria |
| Dados pessoais | Criptografia em repouso (Supabase default) |
| Logs | Sem dados pessoais em logs |
| Senha | Mínimo 8 caracteres, Supabase managed |

### 14.5 Analytics e Tracking

#### Eventos a Rastrear

| Evento | Propriedades |
|--------|-------------|
| `app_opened` | source (push, organic, deeplink) |
| `onboarding_started` | - |
| `onboarding_step_completed` | step_number, step_name |
| `onboarding_completed` | total_time_seconds |
| `onboarding_abandoned` | last_step |
| `photo_taken` | source (camera, gallery) |
| `ai_analysis_started` | - |
| `ai_analysis_completed` | model_used, items_count, duration_ms |
| `ai_analysis_failed` | error_type, model_used |
| `meal_saved` | meal_type, items_count, source (ai, manual, mixed) |
| `meal_edited` | edit_type (portion, remove, add) |
| `food_searched` | query, results_count |
| `barcode_scanned` | found (boolean) |
| `water_logged` | amount_ml |
| `weight_logged` | - |
| `exercise_logged` | category, duration |
| `recipe_created` | ingredients_count |
| `streak_updated` | current_streak |
| `achievement_unlocked` | achievement_key |
| `settings_changed` | setting_name, new_value |
| `export_requested` | format |
| `account_deleted` | reason (se coletado) |
| `paywall_shown` | source |
| `subscription_started` | plan, price |
| `subscription_cancelled` | reason |
| `notification_received` | type |
| `notification_opened` | type |

#### Ferramenta
- **Mixpanel** (preferencial) ou **PostHog** (self-hosted)
- Respeitar opt-out LGPD
- Não enviar PII (dados pessoais identificáveis) para analytics
- User ID anonimizado (hash)

### 14.6 Testes

| Tipo | Cobertura Alvo | Ferramenta |
|------|----------------|-----------|
| Unit tests (Flutter) | 80%+ business logic | flutter_test |
| Unit tests (Node.js) | 80%+ | Jest |
| Widget tests (Flutter) | Telas principais | flutter_test |
| Integration tests | Fluxos críticos (onboarding, análise, log) | integration_test |
| E2E (opcional) | Smoke tests | Patrol ou Appium |
| API tests | Todos os endpoints | Supertest |
| Performance | Análise IA, busca | k6 ou Artillery |

### 14.7 CI/CD

| Etapa | Ferramenta | Trigger |
|-------|-----------|---------|
| Lint + Format | `flutter analyze`, ESLint | Push em qualquer branch |
| Testes | `flutter test`, `jest` | Push em qualquer branch |
| Build Android (APK/AAB) | Codemagic | Push em `main` ou tag |
| Build iOS (IPA) | Codemagic | Push em `main` ou tag |
| Deploy API | GitHub Actions → Railway/Render/Fly.io | Push em `main` |
| Deploy Supabase migrations | Supabase CLI | Push em `main` |
| Distribuição beta | Firebase App Distribution | Tag `beta-*` |
| Publicação stores | Manual via Codemagic | Tag `release-*` |

### 14.8 Monitoramento

| Componente | Ferramenta |
|-----------|-----------|
| Crashes (mobile) | Firebase Crashlytics |
| Errors (API) | Sentry |
| Uptime | UptimeRobot ou Better Uptime |
| Performance (API) | Sentry Performance |
| Logs | Supabase Logs + Logflare |
| Custos IA | Dashboard custom (tokens consumidos) |

---

## Apêndice A: Seed Data — Achievements

```sql
INSERT INTO achievements (key, name, description, icon, requirement_type, requirement_value, sort_order) VALUES
('first_meal', 'Primeira Refeição', 'Registrou sua primeira refeição', '🍽️', 'meals_logged', 1, 1),
('first_photo', 'Fotógrafo', 'Usou a câmera pela primeira vez', '📸', 'photos_taken', 1, 2),
('streak_3', 'Consistência', '3 dias seguidos registrando', '🔥', 'streak', 3, 3),
('streak_7', 'Semana Completa', '7 dias seguidos', '🔥', 'streak', 7, 4),
('streak_14', 'Duas Semanas', '14 dias seguidos', '🔥', 'streak', 14, 5),
('streak_30', 'Mês de Fogo', '30 dias seguidos', '🔥', 'streak', 30, 6),
('streak_90', 'Trimestre Perfeito', '90 dias seguidos', '🔥', 'streak', 90, 7),
('streak_365', 'Um Ano Inteiro', '365 dias seguidos', '🔥', 'streak', 365, 8),
('meals_10', 'Explorador', '10 refeições registradas', '🌟', 'meals_logged', 10, 9),
('meals_50', 'Dedicado', '50 refeições registradas', '⭐', 'meals_logged', 50, 10),
('meals_100', 'Centenário', '100 refeições registradas', '💫', 'meals_logged', 100, 11),
('meals_500', 'Mestre Chef', '500 refeições registradas', '👨‍🍳', 'meals_logged', 500, 12),
('weight_first', 'Na Balança', 'Primeiro registro de peso', '⚖️', 'weight_logged', 1, 13),
('weight_goal', 'Objetivo Alcançado!', 'Atingiu o peso desejado', '🎯', 'weight_goal', 1, 14),
('water_perfect', 'Hidratado', 'Bateu a meta de água', '💧', 'water_goal_met', 1, 15),
('water_week', 'Semana Hidratada', '7 dias seguidos batendo meta de água', '🌊', 'water_streak', 7, 16),
('macros_balanced', 'Equilibrado', 'Ficou dentro de ±5% em todos os macros', '⚖️', 'macros_balanced', 1, 17),
('recipe_first', 'Chef', 'Criou primeira receita', '📝', 'recipes_created', 1, 18),
('barcode_first', 'Scanner', 'Usou o scanner pela primeira vez', '📱', 'barcodes_scanned', 1, 19),
('all_meals_day', 'Dia Completo', 'Registrou todas as refeições do dia', '✅', 'full_day', 1, 20);
```

## Apêndice B: Categorias TACO Completas

A tabela TACO (Tabela Brasileira de Composição de Alimentos — UNICAMP) contém ~597 alimentos organizados em categorias. A base deve ser importada na íntegra como seed do banco `foods`.

Download: https://www.cfn.org.br/wp-content/uploads/2017/03/taco_4_edicao_ampliada_e_revisada.pdf

Formato de importação: converter PDF → CSV → SQL INSERT via script de migração.

## Apêndice C: Estrutura de Pastas Flutter

```
lib/
├── main.dart
├── app/
│   ├── app.dart
│   ├── routes.dart
│   └── theme/
│       ├── app_theme.dart
│       ├── colors.dart
│       └── typography.dart
├── core/
│   ├── constants/
│   ├── errors/
│   ├── network/
│   │   ├── api_client.dart
│   │   └── interceptors.dart
│   ├── storage/
│   │   ├── local_storage.dart
│   │   └── secure_storage.dart
│   └── utils/
│       ├── date_utils.dart
│       ├── nutrition_calculator.dart
│       └── validators.dart
├── data/
│   ├── models/
│   │   ├── user_model.dart
│   │   ├── meal_log_model.dart
│   │   ├── meal_item_model.dart
│   │   ├── food_model.dart
│   │   ├── water_log_model.dart
│   │   ├── weight_log_model.dart
│   │   ├── exercise_log_model.dart
│   │   ├── recipe_model.dart
│   │   └── achievement_model.dart
│   ├── repositories/
│   │   ├── auth_repository.dart
│   │   ├── user_repository.dart
│   │   ├── meal_repository.dart
│   │   ├── food_repository.dart
│   │   ├── water_repository.dart
│   │   ├── weight_repository.dart
│   │   ├── exercise_repository.dart
│   │   ├── recipe_repository.dart
│   │   └── stats_repository.dart
│   └── datasources/
│       ├── remote/
│       │   ├── supabase_datasource.dart
│       │   └── ai_datasource.dart
│       └── local/
│           └── local_db.dart
├── domain/
│   ├── entities/
│   ├── repositories/ (interfaces)
│   └── usecases/
├── presentation/
│   ├── providers/ (Riverpod)
│   │   ├── auth_provider.dart
│   │   ├── user_provider.dart
│   │   ├── meal_provider.dart
│   │   ├── food_search_provider.dart
│   │   ├── water_provider.dart
│   │   ├── stats_provider.dart
│   │   └── theme_provider.dart
│   ├── screens/
│   │   ├── splash/
│   │   ├── onboarding/
│   │   │   ├── onboarding_screen.dart
│   │   │   └── steps/
│   │   │       ├── welcome_step.dart
│   │   │       ├── gender_step.dart
│   │   │       ├── birth_date_step.dart
│   │   │       ├── height_step.dart
│   │   │       ├── weight_step.dart
│   │   │       ├── goal_step.dart
│   │   │       ├── target_weight_step.dart
│   │   │       ├── activity_step.dart
│   │   │       ├── speed_step.dart
│   │   │       ├── diet_step.dart
│   │   │       ├── allergies_step.dart
│   │   │       └── result_step.dart
│   │   ├── home/
│   │   ├── camera/
│   │   ├── analysis_result/
│   │   ├── meal_log/
│   │   ├── food_search/
│   │   ├── barcode_scanner/
│   │   ├── stats/
│   │   ├── profile/
│   │   ├── settings/
│   │   ├── water/
│   │   ├── weight/
│   │   ├── exercise/
│   │   ├── recipes/
│   │   └── paywall/
│   └── widgets/
│       ├── calorie_ring.dart
│       ├── macro_bar.dart
│       ├── meal_card.dart
│       ├── food_item_tile.dart
│       ├── water_tracker.dart
│       ├── streak_badge.dart
│       ├── achievement_card.dart
│       └── loading_overlay.dart
└── l10n/
    └── app_pt.arb
```

## Apêndice D: Estrutura Node.js Backend

```
src/
├── index.ts
├── config/
│   ├── env.ts
│   ├── supabase.ts
│   └── ai.ts
├── routes/
│   ├── auth.routes.ts
│   ├── users.routes.ts
│   ├── meals.routes.ts
│   ├── foods.routes.ts
│   ├── water.routes.ts
│   ├── weight.routes.ts
│   ├── exercises.routes.ts
│   ├── recipes.routes.ts
│   ├── stats.routes.ts
│   └── notifications.routes.ts
├── controllers/
│   ├── meals.controller.ts
│   ├── foods.controller.ts
│   └── ...
├── services/
│   ├── ai/
│   │   ├── gemini.service.ts
│   │   ├── openai.service.ts
│   │   ├── analyzer.service.ts  (orchestrator com fallback)
│   │   └── prompts.ts
│   ├── nutrition.service.ts
│   ├── streak.service.ts
│   ├── achievement.service.ts
│   └── notification.service.ts
├── middleware/
│   ├── auth.middleware.ts
│   ├── rate-limit.middleware.ts
│   └── validation.middleware.ts
├── validators/
│   ├── meal.validator.ts
│   └── ...
├── types/
│   └── index.ts
└── utils/
    ├── image.utils.ts (compress, resize)
    ├── taco.utils.ts
    └── calculator.utils.ts
```

---

> **Este documento é a fonte única de verdade para o desenvolvimento do PratoIA.**
> Qualquer alteração deve ser versionada e aprovada antes de implementação.
>
> **Última atualização:** 2026-03-03
