# PratoIA — Complete Requirements Document

> **Version:** 1.0 | **Date:** 2026-03-03
> **1:1 Clone of Cal AI (calai.app) for the Brazilian market**

---

## Table of Contents

1. [Overview](#1-overview)
2. [Technology Stack](#2-technology-stack)
3. [App Flow (Screen by Screen)](#3-app-flow-screen-by-screen)
4. [Detailed Onboarding](#4-detailed-onboarding)
5. [Feature Matrix](#5-feature-matrix)
6. [UI/UX Specifications](#6-uiux-specifications)
7. [AI Integration](#7-ai-integration)
8. [Data Models](#8-data-models)
9. [API Endpoints](#9-api-endpoints)
10. [Supabase Structure](#10-supabase-structure)
11. [Gamification & Engagement](#11-gamification--engagement)
12. [Brazil Localization](#12-brazil-localization)
13. [MVP Scope](#13-mvp-scope)
14. [Non-Functional Requirements](#14-non-functional-requirements)

---

## 1. Overview

**PratoIA** is an AI-powered calorie tracking app, a functional 1:1 clone of Cal AI, adapted for the Brazilian market. The user takes a photo of their meal and the AI identifies the foods, estimates portions, and calculates macronutrients automatically.

### Value Proposition
- Effortless calorie tracking via photo
- Brazilian food database (TACO/TBCA)
- Interface in Portuguese
- Affordable price: R$14.90/month (post-MVP)

### Target Audience
- People on diets (weight loss, muscle gain, maintenance)
- Gym-goers / fitness enthusiasts
- People with dietary restrictions
- Health professionals monitoring patients
- Age range: 18-45 years
- Brazilian middle class (Class B/C)

---

## 2. Technology Stack

| Component | Technology |
|---|---|
| Frontend (Mobile) | Flutter (iOS + Android) |
| Backend API | Node.js (Express/Fastify) |
| Database | Supabase (PostgreSQL) |
| Authentication | Supabase Auth |
| Storage (photos) | Supabase Storage |
| Primary AI | Google Gemini Flash 2.0 |
| Fallback AI | GPT-4.1 nano (OpenAI) |
| Food Database | TACO/TBCA (Brazilian Food Composition Table) |
| Push Notifications | Supabase Edge Functions + OneSignal (or native APNs/FCM directly) |
| Analytics | Mixpanel or PostHog |
| Payments | RevenueCat (Apple/Google) + Stripe (PIX in the future) |
| CI/CD | GitHub Actions + Codemagic |
| Barcode API | Open Food Facts BR |

---

## 3. App Flow (Screen by Screen)

### 3.1 Splash Screen
- Centered PratoIA logo with fade-in animation
- Duration: 1.5s
- Checks authentication:
  - Logged in → Home Dashboard
  - First access → Onboarding
  - Existing account without completed onboarding → Resume onboarding

### 3.2 Onboarding (see section 4 for details)
- Sequence of 10-12 screens with visual progress
- Each screen = 1 question
- Transition: horizontal slide
- "Back" button on all screens (except the first)

### 3.3 Paywall / Subscription
- Presented after onboarding is complete
- Shows personalized result ("Você atingirá seu objetivo em X semanas" / "You'll reach your goal in X weeks")
- Plans:
  - Weekly: R$9.90/week
  - Monthly: R$14.90/month (highlighted as "MAIS POPULAR" / "MOST POPULAR")
  - Annual: R$99.90/year (highlighted as "MELHOR VALOR" / "BEST VALUE")
- Free 3-day trial (MVP: free app, no paywall)
- Discreet "Continuar sem premium" ("Continue without premium") button at the top
- 7-day guarantee

### 3.4 Home / Dashboard
Main screen with vertical scrollable layout:

#### Header
- Greeting: "Olá, {nome}!" ("Hello, {name}!") with avatar
- Current date spelled out: "Terça, 3 de março" ("Tuesday, March 3rd")
- Settings icon (gear) in the top right corner
- Streak counter (🔥 5 dias / 5 days)

#### Daily Summary (Main Card)
- Large calorie progress circle (center)
  - Calories consumed / goal
  - Remaining calories in the center
- Progress bar for each macro below:
  - Protein (g) — blue color
  - Carbohydrates (g) — orange color
  - Fat (g) — yellow color
- Each bar shows: consumed / goal

#### Meals of the Day
Vertical list of cards per meal:
- ☀️ **Café da Manhã** (Breakfast) — suggested time: 06:00-10:00
- 🌤️ **Lanche da Manhã** (Morning Snack) — time: 10:00-11:30
- ☀️ **Almoço** (Lunch) — time: 11:30-14:00
- 🍎 **Lanche da Tarde** (Afternoon Snack) — time: 14:00-17:00
- 🌙 **Jantar** (Dinner) — time: 17:00-21:00
- 🌜 **Ceia** (Late-Night Snack) — time: 21:00-23:00

Each card shows:
- Meal name
- Total calories (if already logged)
- List of logged foods (thumbnail + name + calories)
- "+" button to add food

#### Water
- Card with water glass and progress (e.g., 5/8 glasses)
- "+" button to add a glass
- Configurable daily goal

#### Exercise
- Exercise summary card for the day
- Calories burned
- "+" button to add exercise

### 3.5 Central Capture Button (FAB)
- Large, black Floating Action Button, centered on the bottom nav
- Camera icon
- On tap:
  1. Opens native camera in fullscreen
  2. Overlay with framing guide ("Posicione o prato no centro" / "Position the plate in the center")
  3. Capture button
  4. Gallery option (icon in the corner)
  5. Flash toggle

### 3.6 Camera / Photo Capture
- Fullscreen camera with overlay
- Visual guidelines to position the plate
- Text: "Aponte para sua refeição" ("Point at your meal")
- Buttons:
  - Capture (center, large)
  - Gallery (bottom left corner)
  - Flash (top right corner)
  - Close/X (top left corner)
- After capture:
  - Photo preview
  - "Analisar" ("Analyze") button (confirm)
  - "Tirar outra" ("Take another") button (redo)

### 3.7 AI Analysis — Loading
- Photo in background with blur
- Loading animation (pulse or shimmer)
- Rotating text:
  - "Identificando alimentos..." ("Identifying foods...")
  - "Estimando porções..." ("Estimating portions...")
  - "Calculando nutrientes..." ("Calculating nutrients...")
- Target time: < 3 seconds

### 3.8 AI Analysis Result
Post-analysis result screen:

#### Header
- Meal photo (thumbnail)
- Detected meal type (or selector: Café/Almoço/Janta/Lanche — Breakfast/Lunch/Dinner/Snack)

#### Identified Food List
For each food item:
- Food name (e.g., "Arroz branco" / white rice)
- Estimated portion (e.g., "150g — 1 colher de servir" / "150g — 1 serving spoon")
- Calories
- Macros: P / C / F
- Edit icon (pencil) — allows portion adjustment
- Remove icon (X)
- AI confidence (visual indicator: grayscale bar or percentage)

#### Totals
- Total meal calories
- Total for each macro
- "Adicionar mais alimentos" ("Add more foods") button (manual search)
- "Salvar refeição" ("Save meal") button (primary CTA, black)

#### Portion Adjustment (Modal)
- Slider to adjust grams
- Quick options: "Metade" ("Half"), "Dobro" ("Double"), "Porção padrão" ("Standard portion")
- Updates calories/macros in real time

### 3.9 Manual Food Search
- Search field with autocomplete
- Source: TACO/TBCA database + user's custom foods
- Filters:
  - Favorites
  - Recent
  - Categories (Fruits, Meats, Grains, Dairy, etc.)
- Each result shows:
  - Food name
  - Calories per 100g
  - Macro summary
- On selection:
  - Portion definition screen (grams or household measure)
  - Brazilian household measures: colher de sopa (tablespoon), concha (ladle), escumadeira (slotted spoon), xícara (cup), unidade (unit)

### 3.10 Barcode Scanner
- Camera with scan area overlay
- Reads EAN-13 code (Brazilian standard)
- Searches in:
  1. PratoIA internal database
  2. Open Food Facts API
  3. If not found: screen to manually register
- Result: product card with nutritional info
- Set portion and save

### 3.11 Food Diary / Meal Log
- Day-by-day view
- Horizontal swipe to navigate between days
- Selectable calendar (top)
- For each day:
  - List of meals with foods
  - Daily totals
  - Pie chart (macros)
- Option to copy meal from another day
- Option to duplicate meal

### 3.12 Statistics (Stats)
#### Weekly View
- Bar chart: calories per day (7 days)
- Calorie goal line
- Weekly average calories
- Weekly average for each macro
- Goal adherence (%)

#### Monthly View
- Calendar heat map (colors by adherence)
- Line chart: weight over time
- Bar chart: average macros per week
- Trends: "Você está consumindo 15% mais proteína esta semana" ("You're consuming 15% more protein this week")

#### Per-Nutrient View
- Drill-down into any macro
- Top consumed foods (ranking)
- Distribution by meal

### 3.13 Profile & Settings
#### Profile
- User photo
- Name
- Email
- Physical data (editable): current weight, height, age
- Weight goal
- Activity level
- Objective (lose/maintain/gain)

#### Settings
- **Nutritional goals**: manually edit calories and macros
- **Notifications**: toggle on/off by type
- **Units**: kg/lb, cm/ft (default: metric)
- **Theme**: Light / Dark / Auto
- **Language**: Português (BR) — fixed in MVP
- **Account**: change email, password
- **Subscription**: manage plan
- **Integrations**: Apple Health, Google Fit, Samsung Health
- **Data**: export data (CSV), delete account
- **LGPD** (Brazilian Data Protection Law): privacy policy, request data, consents
- **About**: version, terms of use
- **Feedback**: send feedback/suggestion

### 3.14 Goal Configuration
- Screen accessible from profile or onboarding
- Daily calorie goal (calculated or manual)
- Macro distribution:
  - Presets: Balanced, Low Carb, High Protein, Keto, Custom
  - Or custom percentages
- Water goal (ml/day)
- Weight goal (kg)
- Estimated timeline to reach goal

### 3.15 Recipes / Recipe Builder
- Create custom recipe:
  - Recipe name
  - Ingredients (search + quantity)
  - Number of servings
  - Photo (optional)
  - Calories/macros automatically calculated per serving
- Save recipe
- List saved recipes
- Use recipe as quick entry in the log

### 3.16 Water Tracking
- Widget on dashboard
- Dedicated screen with:
  - Daily goal (default: 2000ml, configurable)
  - Quick buttons: +200ml, +300ml, +500ml
  - Custom amount
  - Day's history (list of entries)
  - Weekly chart

### 3.17 Exercise Logging
- Exercise search (pre-loaded database)
- Categories: Cardio, Strength Training, Functional, Sports, Yoga, Other
- For each exercise:
  - Duration (minutes)
  - Intensity (light, moderate, intense)
  - Calories burned (calculated or manual)
- Integration with Apple Health / Google Fit for automatic import

### 3.18 Integrations
- Apple Health (iOS): import/export weight, exercises, steps
- Google Fit (Android): import/export weight, exercises, steps
- Samsung Health: same
- Smartwatches: via Health APIs
- Export data as CSV

### 3.19 Bottom Navigation Bar
5 items:
1. 🏠 **Início** (Home) — Dashboard
2. 📊 **Stats** — Statistics
3. 📸 **Câmera** (Camera) (central FAB, elevated) — Photo capture
4. 📖 **Diário** (Diary) — Meal Log
5. 👤 **Perfil** (Profile) — Settings and profile

---

## 4. Detailed Onboarding

Sequential flow of 12 steps. Progress bar at the top. Each step = 1 screen.

### Step 1: Welcome
- PratoIA logo
- Title: "Bem-vindo ao PratoIA" ("Welcome to PratoIA")
- Subtitle: "O jeito mais fácil de controlar sua alimentação" ("The easiest way to control your diet")
- Animation of a photo being taken of a plate → result with macros
- Button: "Começar" ("Start") (black CTA)

### Step 2: Biological Sex
- Question: "Qual seu sexo biológico?" ("What is your biological sex?")
- Options (visual cards):
  - 👨 Masculino (Male)
  - 👩 Feminino (Female)
  - ⚧ Outro / Prefiro não dizer (Other / Prefer not to say)
- Used for BMR calculation (Harris-Benedict / Mifflin-St Jeor)

### Step 3: Date of Birth
- Question: "Qual sua data de nascimento?" ("What is your date of birth?")
- Date picker (iOS-style scroll wheels)
- Format: DD/MM/YYYY
- Validation: minimum age 13 years

### Step 4: Height
- Question: "Qual sua altura?" ("What is your height?")
- Slider or scroll picker
- Unit: cm (Brazilian default)
- Range: 100cm — 250cm
- Toggle for feet/inches

### Step 5: Current Weight
- Question: "Qual seu peso atual?" ("What is your current weight?")
- Scroll picker with decimals (0.1 kg)
- Unit: kg (Brazilian default)
- Range: 30kg — 300kg
- Toggle for pounds

### Step 6: Goal
- Question: "Qual seu objetivo?" ("What is your goal?")
- Options (visual cards with icons):
  - 📉 Perder peso (Lose weight)
  - ⚖️ Manter peso (Maintain weight)
  - 📈 Ganhar massa muscular (Gain muscle mass)
- Feedback animation on selection

### Step 7: Target Weight (if goal ≠ maintain)
- Question: "Qual seu peso desejado?" ("What is your target weight?")
- Same picker as step 5
- Validation:
  - If losing weight: target weight < current weight
  - If gaining: target weight > current weight
- Shows difference: "Você quer [perder/ganhar] X kg" ("You want to [lose/gain] X kg")

### Step 8: Physical Activity Level
- Question: "Qual seu nível de atividade?" ("What is your activity level?")
- Options (cards with description):
  - 🛋️ **Sedentário** (Sedentary) — Little or no exercise
  - 🚶 **Levemente ativo** (Lightly active) — 1-3 days/week
  - 🏃 **Moderadamente ativo** (Moderately active) — 3-5 days/week
  - 💪 **Muito ativo** (Very active) — 6-7 days/week
  - 🏋️ **Extremamente ativo** (Extremely active) — Athlete, 2x/day
- Multiplier factor for TDEE

### Step 9: Progress Speed
- Question: "Qual velocidade você prefere?" ("What speed do you prefer?")
- Options:
  - 🐢 **Devagar** (Slow) — 0.25 kg/week (more sustainable)
  - 🐇 **Moderado** (Moderate) — 0.5 kg/week (recommended) ✨
  - 🚀 **Rápido** (Fast) — 0.75-1 kg/week (more aggressive)
- Info: "Recomendamos a velocidade moderada para resultados sustentáveis" ("We recommend moderate speed for sustainable results")

### Step 10: Dietary Preferences
- Question: "Você segue alguma dieta específica?" ("Do you follow any specific diet?")
- Options (multi-select, chips):
  - Sem restrições (No restrictions)
  - Vegetariano (Vegetarian)
  - Vegano (Vegan)
  - Low Carb
  - Keto
  - Sem Glúten (Gluten-free)
  - Sem Lactose (Lactose-free)
  - Halal
  - Outro (Other)
- Affects suggestions and search filters

### Step 11: Restrictions / Allergies
- Question: "Tem alguma alergia alimentar?" ("Do you have any food allergies?")
- Options (multi-select, chips):
  - Nenhuma (None)
  - Amendoim (Peanut)
  - Castanhas (Tree nuts)
  - Leite (Milk)
  - Ovos (Eggs)
  - Soja (Soy)
  - Trigo/Glúten (Wheat/Gluten)
  - Frutos do mar (Seafood)
  - Peixes (Fish)
  - Outra (Other) (text field)

### Step 12: Personalized Result
- Title: "Seu plano está pronto! 🎉" ("Your plan is ready! 🎉")
- Summary card:
  - Daily calorie goal: {X} kcal
  - Protein: {X}g | Carbohydrate: {X}g | Fat: {X}g
  - Forecast: "Você atingirá {peso_desejado}kg em ~{X} semanas" ("You'll reach {target_weight}kg in ~{X} weeks")
- Projection chart (descending/ascending line)
- Button: "Começar a usar o PratoIA" ("Start using PratoIA") (CTA)

### Onboarding Calculations

#### BMR (Basal Metabolic Rate) — Mifflin-St Jeor
```
Men:   BMR = 10 × weight(kg) + 6.25 × height(cm) - 5 × age - 5 + 161
Women: BMR = 10 × weight(kg) + 6.25 × height(cm) - 5 × age - 161
```

#### TDEE (Total Daily Energy Expenditure)
```
TDEE = BMR × activity_factor

Sedentary: 1.2
Lightly active: 1.375
Moderately active: 1.55
Very active: 1.725
Extremely active: 1.9
```

#### Calorie Goal
```
Lose weight (0.5kg/week): TDEE - 500 kcal
Maintain weight: TDEE
Gain muscle (0.5kg/week): TDEE + 300-500 kcal
```

#### Macro Distribution (Balanced Default)
```
Protein: 30% of calories → calories × 0.30 / 4
Carbohydrate: 40% of calories → calories × 0.40 / 4
Fat: 30% of calories → calories × 0.30 / 9
```

---

## 5. Feature Matrix

| # | Feature | Cal AI | PratoIA | Priority | Notes |
|---|---------|--------|---------|----------|-------|
| 1 | Photo → calorie analysis | AI with depth sensor | Gemini Flash 2.0 + fallback GPT-4.1 nano | **MVP** | Core feature |
| 2 | Food identification | Multi-item detection | Same via prompt engineering | **MVP** | |
| 3 | Portion estimation | Depth sensor + AI | Visual AI (no depth sensor) | **MVP** | Estimation by visual reference |
| 4 | Calories + Macros (P/C/F) | Yes | Yes | **MVP** | |
| 5 | Food diary | Yes | Yes | **MVP** | |
| 6 | Dashboard with progress | Yes | Yes | **MVP** | |
| 7 | Manual food search | USDA/generic database | TACO/TBCA database | **MVP** | Brazilian adaptation |
| 8 | Household measures | Cups, tablespoons | Colher de sopa (tablespoon), concha (ladle), xícara (cup) | **MVP** | Localized for Brazil |
| 9 | Complete onboarding | 10-12 steps | 12 steps (see section 4) | **MVP** | |
| 10 | Automatic goal calculation | BMR + TDEE | Same (Mifflin-St Jeor) | **MVP** | |
| 11 | Weight logging | Yes | Yes | **MVP** | |
| 12 | Weekly calorie chart | Yes | Yes | **MVP** | |
| 13 | Barcode scanner | Yes | Open Food Facts + own database | **MVP** | |
| 14 | Authentication (email/social) | Yes | Supabase Auth (email, Google, Apple) | **MVP** | |
| 15 | Push notifications | Yes | FCM | **MVP** | Meal reminders |
| 16 | Water tracking | Yes | Yes | **MVP** | |
| 17 | Favorite/recent meals | Yes | Yes | **MVP** | |
| 18 | Copy meal | Yes | Yes | **MVP** | |
| 19 | Dark mode | Yes | Yes | **MVP** | |
| 20 | Gallery photo analysis | Yes | Yes | **MVP** | |
| 21 | Edit portion post-analysis | Yes | Slider + household measures | **MVP** | |
| 22 | Custom recipes | Yes | Yes | **v1.1** | |
| 23 | Exercise logging | Yes | Yes | **v1.1** | |
| 24 | Apple Health integration | Yes | Yes | **v1.1** | |
| 25 | Google Fit integration | Yes | Yes | **v1.1** | |
| 26 | Monthly statistics | Yes | Yes | **v1.1** | |
| 27 | Weight chart over time | Yes | Yes | **v1.1** | |
| 28 | Micronutrients (vitamins, minerals) | Partial | TACO has complete data | **v1.1** | Differentiator! |
| 29 | Paywall / Subscription | Yes | RevenueCat | **v1.1** | MVP is free |
| 30 | Streaks and gamification | Yes | Yes | **v1.1** | |
| 31 | Export data CSV | Yes | Yes | **v1.1** | |
| 32 | Custom foods (create) | Yes | Yes | **v1.1** | |
| 33 | Text analysis (describe meal) | Yes | Gemini prompt | **v2.0** | "Comi arroz, feijão e bife" ("I ate rice, beans, and steak") |
| 34 | Home screen widget | Yes | Flutter widget | **v2.0** | |
| 35 | Apple Watch / Wear OS | Yes | Flutter companion | **v2.0** | |
| 36 | AI meal plan | No | PratoIA differentiator | **v2.0** | Menu generation |
| 37 | Social/Share | Partial | Share card with summary | **v2.0** | |
| 38 | Multi-language | English | Portuguese BR (+ EN future) | **v2.0** | |
| 39 | Nutritionist integration | No | Share diary | **v2.0** | Brazilian differentiator |
| 40 | Nutrition label scanner | Partial | OCR of nutrition table | **v2.0** | |

---

## 6. UI/UX Specifications

### 6.1 Design Language
- **Style:** Clean, minimalist, modern — similar to Cal AI
- **Inspiration:** Cal AI, MyFitnessPal, Yazio
- **Corners:** Rounded (border-radius: 16px for cards, 12px for buttons)
- **Shadows:** Subtle, low elevation (2-4dp)
- **Spacing:** 8px grid system

### 6.2 Color Palette (Cal AI Style — Monochrome Minimal)

#### Light Mode (Primary)
| Element | Color | Hex |
|---------|-------|-----|
| Primary (CTA, highlights) | Black | `#000000` |
| Primary Soft | Dark gray | `#1A1A1A` |
| Background | Pure white | `#FFFFFF` |
| Surface (cards) | Light gray | `#F5F5F5` |
| Text Primary | Black | `#000000` |
| Text Secondary | Medium gray | `#6B7280` |
| Border / Dividers | Light gray | `#E5E7EB` |
| Protein | Neutral dark | `#374151` |
| Carbohydrate | Medium gray | `#6B7280` |
| Fat | Light gray | `#9CA3AF` |
| Calories | Black | `#000000` |
| Error | Red (only functional color) | `#EF4444` |
| Success | Dark gray | `#1A1A1A` |
| Warning | Medium gray | `#6B7280` |

> **Design philosophy:** Monochrome black/white like Cal AI. NO vibrant colors. The only color allowed is functional red for errors/destructive actions. Macro indicators use grayscale tones to differentiate. Clean, premium, minimal.

#### Dark Mode
| Element | Color | Hex |
|---------|-------|-----|
| Primary (CTA, highlights) | White | `#FFFFFF` |
| Background | True black | `#000000` |
| Surface (cards) | Near black | `#111111` |
| Text Primary | White | `#F9FAFB` |
| Text Secondary | Light gray | `#9CA3AF` |
| Border / Dividers | Dark gray | `#1F2937` |
| Other colors | Inverted grayscale | — |

### 6.3 Typography
| Usage | Font | Weight | Size |
|-------|------|--------|------|
| Display / Large numbers | Inter or SF Pro | Bold (700) | 32-48px |
| Section title | Inter | SemiBold (600) | 20-24px |
| Subtitle | Inter | Medium (500) | 16-18px |
| Body text | Inter | Regular (400) | 14-16px |
| Caption / Labels | Inter | Regular (400) | 12px |
| Numbers (calories/macros) | Inter | Bold (700) | Variable |

### 6.4 Components

#### Cards
- Background: Surface color
- Border radius: 16px
- Padding: 16px
- Shadow: `0 2px 8px rgba(0,0,0,0.08)`

#### Buttons
- **Primary CTA:** Black background, white text, border-radius 12px, height 56px
- **Secondary:** Black outline, transparent background
- **Text button:** No background, black text
- **Disabled:** 40% opacity

#### Inputs
- Border radius: 12px
- Border: 1px solid `#E0E0E0`
- Focus border: primary black
- Height: 52px
- Horizontal padding: 16px

#### Progress Indicators
- **Circular (calories):** Stroke width 12px, fill animation
- **Linear (macros):** Height 8px, border-radius 4px, progress animation
- **Onboarding:** Dots or continuous bar at the top

### 6.5 Animations and Transitions
| Context | Type | Duration | Curve |
|---------|------|----------|-------|
| Screen navigation | Horizontal slide | 300ms | easeInOut |
| Onboarding | Horizontal slide | 300ms | easeInOut |
| Cards appearing | Fade + slide up | 200ms | easeOut |
| Calorie progress | Circular animation | 800ms | easeInOut |
| Macro bars | Animated fill | 600ms | easeOut |
| FAB press | Scale down 0.95 | 100ms | easeIn |
| Modal bottom sheet | Slide up | 250ms | decelerate |
| Loading (AI analysis) | Shimmer/pulse | Loop | linear |
| Success (save) | Check mark Lottie | 500ms | — |
| Splash logo | Fade in + scale | 500ms | easeOut |

### 6.6 Icons
- Library: **Phosphor Icons** or **Lucide** (line style)
- Consistency: all line, stroke 1.5-2px
- Default size: 24px (bottom nav: 28px)

### 6.7 Illustrations
- Style: Flat/minimal monochrome (black/white/gray only)
- Usage: onboarding, empty states, errors
- Option: Undraw.co or custom illustrations

---

## 7. AI Integration

### 7.1 Photo Analysis Technical Flow

```
[User takes photo]
        ↓
[Upload to Supabase Storage]
        ↓
[Frontend sends request to API]
  POST /api/v1/meals/analyze
  Body: { image_url, meal_type?, user_id }
        ↓
[Backend → Gemini Flash 2.0]
  - Sends image + structured prompt
  - Timeout: 10s
        ↓
  [Success?]
    Yes → Parse JSON response → return
    No  → [Fallback: GPT-4.1 nano]
            - Same prompt, same image
            - Timeout: 15s
            ↓
            [Success?]
              Yes → Parse → return
              No  → Return error to user
        ↓
[Frontend receives result]
  - Displays identified foods
  - User edits/confirms
  - Saves to database
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

*(Translation for reference — the actual prompt sent to the AI is in Portuguese as shown above:*
*"You are a nutritionist specialized in visual analysis of Brazilian foods. Analyze the provided meal photo and identify ALL visible foods. For each food: 1. Identify the name in Brazilian Portuguese 2. Estimate the portion in grams based on visual size relative to the plate/container 3. Calculate nutritional values using the TACO/TBCA table as reference 4. Assign a confidence level (high/medium/low). Rules: Use common Brazilian names, consider typical Brazilian preparations, estimate portions using visual references (standard plate = 26cm diameter), prioritize accuracy over completeness, list unidentified items as 'Unidentified food' with 'low' confidence, consider visible sauces/seasonings/sides, rice and beans together are very common in Brazilian dishes. Respond ONLY with JSON in the specified format, no additional text.")*

#### User Prompt
```
Analise esta foto de refeição. 
Tipo de refeição: {meal_type ou "detectar automaticamente"}
Contexto adicional: {user_notes se houver}
```

*(Translation: "Analyze this meal photo. Meal type: {meal_type or 'detect automatically'}. Additional context: {user_notes if any}")*

### 7.3 Response Format (JSON Schema)

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

*(Note: Food names and suggestions remain in Portuguese as they are part of the AI response targeting Brazilian users. Example items: "Arroz branco cozido" = cooked white rice, "Feijão carioca cozido" = cooked carioca beans (pinto beans), "colher de servir cheia" = full serving spoon, "concha média" = medium ladle.)*

### 7.4 Error Handling

| Scenario | Action | UX |
|----------|--------|----|
| Dark/blurry photo | Return quality error | "A foto está escura. Tente novamente com melhor iluminação" ("The photo is dark. Try again with better lighting") |
| No food detected | Return empty list | "Não conseguimos identificar alimentos. Tente tirar a foto mais de perto" ("We couldn't identify foods. Try taking the photo closer") |
| Low confidence (< 50%) | Yellow/red visual flag | Item marked with "⚠️ Verificar" ("⚠️ Verify") and alternative suggestions |
| Gemini timeout | Fallback to GPT-4.1 nano | Loading continues, seamless to user |
| Both AIs timeout | Generic error | "Não foi possível analisar. Tente novamente ou adicione manualmente" ("Unable to analyze. Try again or add manually") |
| Non-Brazilian food | Find equivalent | Map international name → TACO |
| Non-food photo | Detect and reject | "Não identificamos alimentos nesta foto" ("We didn't identify food in this photo") |
| Rate limiting | Queue with retry | Loading with wait message |

### 7.5 Portion Estimation

#### Strategy
Without a depth sensor (LiDAR), we use visual references:

1. **Plate as reference**: Standard Brazilian plate = 26cm diameter
2. **Visual proportion**: Food area relative to plate
3. **Common references**:
   - Colher de sopa (tablespoon) = ~15g (rice), ~25g (beans)
   - Concha (ladle) = ~120g (beans)
   - Escumadeira (slotted spoon) = ~100g (rice)
   - Xícara (cup) = ~240ml
   - Copo (glass) = ~200ml
4. **Thickness**: Estimated by 3D appearance (shadows, texture)
5. **Calibration**: User can set plate size in settings

#### Additional Portion Prompt
```
Para estimar porções, considere:
- O prato padrão brasileiro tem ~26cm de diâmetro
- Use a proporção do alimento em relação ao prato
- Considere a altura/volume aparente do alimento
- Arredonde para a medida caseira mais próxima
- Na dúvida, estime para baixo (melhor subestimar)
```

*(Translation: "To estimate portions, consider: The standard Brazilian plate is ~26cm in diameter. Use the food's proportion relative to the plate. Consider the apparent height/volume of the food. Round to the nearest household measure. When in doubt, estimate lower (better to underestimate).")*

### 7.6 Cache and Optimization
- Cache identical analyses (image hash) for 24h
- Common responses in local cache (rice, beans, salad)
- Image compression before sending (max 1MB, 1024px longest side)
- Format: JPEG quality 85%

---

## 8. Data Models

### 8.1 Entities

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
  ai_analysis_raw: object | null; // Raw AI JSON
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
  food_id: string | null;        // FK → foods (null if custom)
  name: string;
  portion_grams: number;
  portion_description: string;   // "1 colher de servir" (1 serving spoon)
  calories: number;
  protein: number;
  carbs: number;
  fat: number;
  fiber: number;
  source: 'ai' | 'manual' | 'barcode' | 'recipe' | 'favorite';
  ai_confidence: 'high' | 'medium' | 'low' | null;
  is_verified: boolean;          // user confirmed/edited
  created_at: Date;
}
```

#### Food (TACO Database)
```typescript
interface Food {
  id: string;                    // UUID, PK
  taco_id: string | null;        // Original TACO ID
  barcode: string | null;        // EAN-13
  name: string;
  name_normalized: string;       // lowercase, no accents (search)
  category: string;              // "Cereais", "Carnes", "Frutas", etc.
  portion_default_g: number;
  calories_per_100g: number;
  protein_per_100g: number;
  carbs_per_100g: number;
  fat_per_100g: number;
  fiber_per_100g: number;
  sodium_per_100g: number | null;
  // TACO micronutrients
  calcium_mg: number | null;
  iron_mg: number | null;
  vitamin_c_mg: number | null;
  vitamin_a_mcg: number | null;
  // ... remaining TACO micronutrients
  household_measures: HouseholdMeasure[];
  source: 'taco' | 'open_food_facts' | 'user' | 'admin';
  is_verified: boolean;
  created_at: Date;
}

interface HouseholdMeasure {
  name: string;       // "colher de sopa" (tablespoon), "concha média" (medium ladle), "fatia" (slice)
  grams: number;      // gram equivalence
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
  current_streak: number;        // consecutive days
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

### Authentication
All endpoints (except auth) require header: `Authorization: Bearer {supabase_jwt}`

### Endpoints

#### Auth (via Supabase Auth — no custom API needed)
- `POST /auth/signup` — Create account
- `POST /auth/login` — Login
- `POST /auth/logout` — Logout
- `POST /auth/forgot-password` — Reset password
- `POST /auth/oauth/{provider}` — OAuth (Google, Apple)

#### Users
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users/me` | Logged-in user profile |
| PUT | `/users/me` | Update profile |
| PUT | `/users/me/onboarding` | Save onboarding data |
| PUT | `/users/me/goals` | Update nutritional goals |
| DELETE | `/users/me` | Delete account (LGPD) |
| GET | `/users/me/export` | Export data (LGPD) |

#### Meals
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/meals/analyze` | Send photo for AI analysis |
| POST | `/meals` | Save meal (with items) |
| GET | `/meals?date={YYYY-MM-DD}` | List meals for the day |
| GET | `/meals/{id}` | Meal detail |
| PUT | `/meals/{id}` | Edit meal |
| DELETE | `/meals/{id}` | Delete meal |
| POST | `/meals/{id}/copy` | Copy meal to another day/type |

#### Meal Items
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/meals/{meal_id}/items` | Add item to meal |
| PUT | `/meals/{meal_id}/items/{item_id}` | Edit item |
| DELETE | `/meals/{meal_id}/items/{item_id}` | Remove item |

#### Foods
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/foods/search?q={query}` | Search food (TACO + custom) |
| GET | `/foods/{id}` | Food detail |
| GET | `/foods/barcode/{code}` | Search by barcode |
| POST | `/foods` | Create custom food |
| GET | `/foods/categories` | List categories |
| GET | `/foods/recent` | User's recent foods |
| GET | `/foods/favorites` | User's favorites |
| POST | `/foods/{id}/favorite` | Favorite/unfavorite |

#### Water
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/water` | Log water |
| GET | `/water?date={YYYY-MM-DD}` | Day's total |
| DELETE | `/water/{id}` | Remove entry |

#### Weight
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/weight` | Log weight |
| GET | `/weight?from={date}&to={date}` | Weight history |
| DELETE | `/weight/{id}` | Remove entry |

#### Exercise
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/exercises` | Log exercise |
| GET | `/exercises?date={YYYY-MM-DD}` | Day's exercises |
| DELETE | `/exercises/{id}` | Remove exercise |
| GET | `/exercises/catalog` | Exercise catalog |

#### Recipes
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/recipes` | Create recipe |
| GET | `/recipes` | List user's recipes |
| GET | `/recipes/{id}` | Recipe detail |
| PUT | `/recipes/{id}` | Edit recipe |
| DELETE | `/recipes/{id}` | Delete recipe |

#### Stats
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/stats/daily?date={YYYY-MM-DD}` | Daily summary |
| GET | `/stats/weekly?date={YYYY-MM-DD}` | Weekly summary |
| GET | `/stats/monthly?month={YYYY-MM}` | Monthly summary |
| GET | `/stats/streak` | Streak data |

#### Notifications
| Method | Endpoint | Description |
|--------|----------|-------------|
| PUT | `/notifications/preferences` | Configure preferences |
| POST | `/notifications/token` | Register FCM token |

---

## 10. Supabase Structure

### 10.1 SQL Tables

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
-- FOODS (TACO database + custom)
-- ============================================
CREATE TABLE foods (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  taco_id TEXT,
  barcode TEXT,
  name TEXT NOT NULL,
  name_normalized TEXT NOT NULL, -- for full-text search
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
  ai_model_used TEXT, -- 'gemini_flash_2' or 'gpt_4_1_nano'
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
-- USER_FAVORITES (favorite foods)
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

-- RLS Policies: each user can only see/edit their own data
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

-- Repeat pattern for remaining tables...

-- Foods: everyone can read, only creator can edit user-created
CREATE POLICY "Anyone can read foods" ON foods
  FOR SELECT USING (true);

CREATE POLICY "Users can create foods" ON foods
  FOR INSERT WITH CHECK (source = 'user' AND created_by = (SELECT id FROM users WHERE auth_id = auth.uid()));

-- ============================================
-- FUNCTIONS (Supabase Edge Functions / DB Functions)
-- ============================================

-- Function to calculate meal_log totals
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

-- Function to update streak
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
    RETURN; -- already logged today
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

| Bucket | Usage | Access |
|--------|-------|--------|
| `meal-photos` | Meal photos | Private (user own) |
| `avatars` | Profile photos | Public |
| `recipe-photos` | Recipe photos | Private (user own) |

### 10.3 Supabase Edge Functions

| Function | Trigger | Description |
|----------|---------|-------------|
| `analyze-meal` | HTTP POST | Processes photo via Gemini/GPT |
| `calculate-goals` | HTTP POST | Recalculates goals after onboarding |
| `daily-stats` | HTTP GET | Returns compiled daily summary |
| `send-notification` | Cron (schedule) | Sends push notifications |
| `export-data` | HTTP GET | Generates CSV for LGPD compliance |

---

## 11. Gamification & Engagement

### 11.1 Streak System

#### Rules
- Streak increments when the user logs at least 1 meal in the day
- Streak resets if an entire day passes without a log
- Streak does not reset for future days (only past)
- Grace period: none (strict, like Cal AI)

#### Visual
- 🔥 Flame icon in the dashboard header
- Number beside it: "🔥 7"
- Celebration animation when reaching milestones (7, 14, 30, 60, 90, 365 days)

### 11.2 Achievements

| Key | Name | Description | Requirement |
|-----|------|-------------|-------------|
| `first_meal` | Primeira Refeição (First Meal) | Logged your first meal | 1 meal |
| `first_photo` | Fotógrafo (Photographer) | Used the camera for the first time | 1 photo analyzed |
| `streak_3` | Consistência (Consistency) | 3 consecutive days logging | 3-day streak |
| `streak_7` | Semana Completa (Complete Week) | 7 consecutive days | 7-day streak |
| `streak_14` | Duas Semanas (Two Weeks) | 14 consecutive days | 14-day streak |
| `streak_30` | Mês de Fogo (Month on Fire) | 30 consecutive days | 30-day streak |
| `streak_90` | Trimestre Perfeito (Perfect Quarter) | 90 consecutive days | 90-day streak |
| `streak_365` | Um Ano Inteiro (A Full Year) | 365 consecutive days | 365-day streak |
| `meals_10` | Explorador (Explorer) | 10 meals logged | 10 total |
| `meals_50` | Dedicado (Dedicated) | 50 meals logged | 50 total |
| `meals_100` | Centenário (Centurion) | 100 meals logged | 100 total |
| `meals_500` | Mestre Chef (Master Chef) | 500 meals logged | 500 total |
| `weight_first` | Na Balança (On the Scale) | First weight log | 1 log |
| `weight_goal` | Objetivo Alcançado! (Goal Achieved!) | Reached target weight | weight = goal |
| `water_perfect` | Hidratado (Hydrated) | Hit water goal | 1 day at 100% |
| `water_week` | Semana Hidratada (Hydrated Week) | 7 consecutive days hitting water goal | 7 days |
| `macros_balanced` | Equilibrado (Balanced) | Stayed within ±5% on all macros | 1 day |
| `recipe_first` | Chef | Created first recipe | 1 recipe |
| `barcode_first` | Scanner | Used the scanner for the first time | 1 scan |
| `all_meals_day` | Dia Completo (Complete Day) | Logged all meals of the day | 4+ meals |

### 11.3 Notification Strategy

#### Notification Types

| Type | Time | Frequency | Text (PT-BR with translation) |
|------|------|-----------|-------------------------------|
| Breakfast reminder | 08:00 | Daily | "☀️ Bom dia! Não esqueça de registrar seu café da manhã" ("Good morning! Don't forget to log your breakfast") |
| Lunch reminder | 12:30 | Daily | "🍽️ Hora do almoço! Tire uma foto do seu prato" ("Lunchtime! Take a photo of your plate") |
| Dinner reminder | 19:30 | Daily | "🌙 Boa noite! Registre seu jantar para manter a sequência" ("Good evening! Log your dinner to keep your streak") |
| Streak at risk | 20:00 | Daily (if not logged) | "🔥 Sua sequência de {X} dias está em risco! Registre algo antes de dormir" ("Your {X}-day streak is at risk! Log something before bed") |
| Streak celebration | On log | At milestone | "🎉 Parabéns! {X} dias seguidos! Você é incrível!" ("Congrats! {X} consecutive days! You're amazing!") |
| Achievement unlocked | On unlock | One-time | "🏆 Conquista desbloqueada: {nome}!" ("Achievement unlocked: {name}!") |
| Water reminder | 15:00 | Daily | "💧 Já bebeu água hoje? Você está em {X}% da sua meta" ("Have you had water today? You're at {X}% of your goal") |
| Weekly summary | Sunday 10:00 | Weekly | "📊 Seu resumo semanal está pronto! Veja como você foi" ("Your weekly summary is ready! See how you did") |
| Weight | Saturday 09:00 | Weekly | "⚖️ Que tal se pesar hoje? Acompanhar o peso ajuda a manter o foco" ("How about weighing yourself today? Tracking weight helps maintain focus") |
| Re-engagement (inactive 3d) | 10:00 | One-time | "Sentimos sua falta! 😢 Volte e retome o controle da sua alimentação" ("We miss you! Come back and take control of your diet") |
| Re-engagement (inactive 7d) | 10:00 | One-time | "Faz uma semana! Que tal recomeçar hoje? Um prato de cada vez 💪" ("It's been a week! How about starting again today? One plate at a time 💪") |

#### User Settings
- Global toggle: turn all on/off
- Per-type toggle: meal reminders, water, weight, achievements
- Customizable times for meal reminders
- Silent mode: no notifications between 22:00-07:00

---

## 12. Brazil Localization

### 12.1 Food Categories (TACO)

| Category | Examples |
|----------|----------|
| Cereais e derivados (Cereals and derivatives) | Arroz (rice), pão francês (French bread roll), macarrão (pasta), farinha de mandioca (cassava flour), cuscuz (couscous) |
| Verduras, hortaliças e derivados (Vegetables and derivatives) | Alface (lettuce), tomate (tomato), cenoura (carrot), brócolis (broccoli), couve (collard greens) |
| Frutas e derivados (Fruits and derivatives) | Banana, maçã (apple), laranja (orange), manga (mango), açaí |
| Gorduras e óleos (Fats and oils) | Azeite (olive oil), óleo de soja (soy oil), manteiga (butter), margarina (margarine) |
| Pescados e frutos do mar (Fish and seafood) | Tilápia, sardinha (sardine), camarão (shrimp), atum (tuna) |
| Carnes e derivados (Meats and derivatives) | Frango (chicken), carne bovina (beef), carne suína (pork), linguiça (sausage) |
| Leite e derivados (Dairy) | Leite (milk), queijo minas (minas cheese), iogurte (yogurt), requeijão (cream cheese spread) |
| Bebidas (Beverages) | Café (coffee), suco de laranja (orange juice), refrigerante (soda), cerveja (beer) |
| Ovos e derivados (Eggs and derivatives) | Ovo cozido (boiled egg), ovo frito (fried egg), omelete (omelette) |
| Produtos açucarados (Sugary products) | Açúcar (sugar), mel (honey), chocolate, doce de leite (milk caramel) |
| Miscelâneas (Miscellaneous) | Catchup (ketchup), mostarda (mustard), maionese (mayonnaise), molho de tomate (tomato sauce) |
| Outros alimentos industrializados (Other processed foods) | Biscoitos (crackers/cookies), salgadinhos (snacks), embutidos (deli meats) |
| Alimentos preparados (Prepared foods) | Feijoada (black bean stew with pork), estrogonofe (stroganoff, Brazilian style), moqueca (Bahian fish/shrimp stew) |
| Leguminosas e derivados (Legumes and derivatives) | Feijão carioca (pinto beans), feijão preto (black beans), lentilha (lentils), grão-de-bico (chickpeas) |
| Nozes e sementes (Nuts and seeds) | Castanha-do-pará (Brazil nut), amendoim (peanut), chia |
| Tubérculos, raízes e derivados (Tubers, roots and derivatives) | Batata (potato), mandioca (cassava), inhame (yam), batata-doce (sweet potato) |

### 12.2 Brazilian Meals

| Code | Name | Typical Time | Icon |
|------|------|--------------|------|
| `breakfast` | Café da Manhã (Breakfast) | 06:00-10:00 | ☀️ |
| `morning_snack` | Lanche da Manhã (Morning Snack) | 10:00-11:30 | 🥤 |
| `lunch` | Almoço (Lunch) | 11:30-14:00 | 🍽️ |
| `afternoon_snack` | Lanche da Tarde (Afternoon Snack) | 14:00-17:00 | 🍎 |
| `dinner` | Jantar (Dinner) | 17:00-21:00 | 🌙 |
| `supper` | Ceia (Late-Night Snack) | 21:00-23:00 | 🌜 |

### 12.3 Brazilian Household Measures

| Measure | Grams (reference) |
|---------|-------------------|
| Colher de chá (teaspoon) | 5g |
| Colher de sobremesa (dessert spoon) | 10g |
| Colher de sopa (tablespoon) | 15g |
| Colher de sopa cheia (heaping tablespoon) | 25g |
| Colher de servir (serving spoon) | 45g |
| Concha pequena (small ladle) | 80g |
| Concha média (medium ladle) | 120g |
| Concha grande (large ladle) | 160g |
| Escumadeira (slotted spoon) | 100g |
| Xícara de chá (teacup) | 240ml / variable in g |
| Copo americano (American glass — standard Brazilian drinking glass) | 200ml |
| Copo de requeijão (requeijão jar glass) | 250ml |
| Fatia - pão de forma (slice — sliced bread) | 25g |
| Fatia fina - queijo (thin slice — cheese) | 15g |
| Fatia média - queijo (medium slice — cheese) | 30g |
| Unidade pequena (small unit) | Variable |
| Unidade média (medium unit) | Variable |
| Unidade grande (large unit) | Variable |
| Pedaço pequeno (small piece) | Variable |
| Pedaço médio (medium piece) | Variable |
| Pedaço grande (large piece) | Variable |
| Ponta de faca (knife tip) | 3g |

### 12.4 Cultural Considerations

- **Prato feito (PF)** (set meal plate): Arroz (rice) + feijão (beans) + carne (meat) + salada (salad) is the standard meal — AI must recognize this pattern
- **Açaí**: Extremely popular, treat as a special category with toppings
- **Churrasco** (Brazilian barbecue): Recognize different cuts — picanha (top sirloin cap), maminha (tri-tip), fraldinha (flank steak), etc.
- **Comida de boteco** (bar food): Pastel (fried pastry), coxinha (chicken croquette), bolinho de bacalhau (codfish fritters)
- **Café** (coffee): Filtered coffee with sugar is very common — ask whether it has sugar
- **Suco natural** (fresh juice): Much more common than in other countries
- **Tapioca**: Regional Northeastern item popular nationwide
- **Pão francês** (French bread roll): Different from international "French bread" — Brazil-specific item
- **Marmita/Quentinha** (packed lunch): Important concept — meal in a transport container

### 12.5 Payment — PIX (Future, post-MVP)

For v1.1+ with subscription:
- **RevenueCat** for Apple App Store and Google Play (standard)
- **Stripe** with PIX for direct payment (web alternative)
- Consider: recurring PIX when available
- Plans:
  - Weekly: R$9.90
  - Monthly: R$14.90
  - Annual: R$99.90

### 12.6 LGPD (Brazilian General Data Protection Law)

#### Mandatory
1. **Explicit consent**: Checkbox at registration to accept terms and privacy policy
2. **Privacy Policy**: Document accessible in the app, describing:
   - What data is collected
   - How it is used
   - With whom it is shared
   - Retention period
3. **Right of access**: "Exportar meus dados" ("Export my data") button in settings → generates CSV/JSON
4. **Right of deletion**: "Excluir minha conta" ("Delete my account") button → deletes all data within 48h
5. **Right of rectification**: User can edit all personal data
6. **Consent for notifications**: Explicit request before sending push
7. **Consent for analytics**: Opt-in/opt-out for tracking
8. **DPO (Data Protection Officer)**: Contact email visible in policy

#### Data Collected
| Data | Purpose | Legal Basis |
|------|---------|-------------|
| Email, name | Authentication and identification | Consent |
| Physical data (weight, height, etc.) | Nutritional goal calculation | Consent |
| Meal photos | AI nutritional analysis | Consent |
| Food logs | History and statistics | Consent |
| Usage data (analytics) | Product improvement | Legitimate interest (opt-out) |
| FCM token | Sending notifications | Consent |

#### Technical Implementation
- Supabase RLS ensures data isolation per user
- Photos stored in private bucket with signed URL access (expires in 1h)
- Deletion endpoint: soft delete (30 days) → hard delete
- Consent log in separate table
- Do not share personal data with third parties (AI receives only image, not user data)

---

## 13. MVP Scope

### 13.1 MVP — Must Have for Launch

| # | Feature | Acceptance Criteria |
|---|---------|---------------------|
| 1 | Registration/Login (email + Google + Apple) | User can create account and log in with email or OAuth |
| 2 | Complete onboarding (12 steps) | All steps work, data saved, goals automatically calculated |
| 3 | Dashboard with daily summary | Shows daily calories/macros with visual progress, meal list |
| 4 | Camera → AI Analysis | Take photo → send to Gemini → receive food list with calories/macros in < 5s |
| 5 | Gallery photo analysis | Select existing photo and analyze |
| 6 | Editable analysis result | User can edit name, portion, remove items; macros recalculate |
| 7 | Save meal | Meal saved to database with all items and appears in diary |
| 8 | Food diary | View meals by day, navigate between days |
| 9 | Manual food search | Search TACO database by name, select portion in grams or household measure |
| 10 | Barcode scanner | Scan EAN-13, search Open Food Facts, display nutritional info |
| 11 | Water tracking | Add/remove water, daily goal, visual progress |
| 12 | Weight logging | Log weight with date, view latest entry |
| 13 | Editable profile | Edit all personal data and goals |
| 14 | Basic settings | Theme (light/dark/auto), notifications on/off |
| 15 | Weekly statistics | Bar chart of calories for the last 7 days |
| 16 | Recent and favorite foods | Favorite foods, recent section in search |
| 17 | Push notifications (reminders) | Meal reminders (breakfast, lunch, dinner) via FCM |
| 18 | Copy meal | Duplicate meal from another day/type |
| 19 | Dark mode | Complete dark theme |
| 20 | Basic LGPD compliance | Privacy policy, export data, delete account |

### 13.2 v1.1 — Post-Launch (2-4 weeks)

| # | Feature |
|---|---------|
| 1 | Custom recipes (create, save, use as quick entry) |
| 2 | Exercise logging (catalog + manual) |
| 3 | Apple Health integration (weight, exercises, steps) |
| 4 | Google Fit integration |
| 5 | Monthly statistics (calendar heatmap, trends) |
| 6 | Weight evolution chart |
| 7 | Streaks and achievements (gamification) |
| 8 | Custom foods (create) |
| 9 | Micronutrients (complete TACO data) |
| 10 | Paywall / subscription (RevenueCat) |
| 11 | Export data as CSV |
| 12 | Smart notifications (streak at risk, weekly summary) |

### 13.3 v2.0 — Future

| # | Feature |
|---|---------|
| 1 | Text analysis ("comi arroz, feijão e bife" / "I ate rice, beans, and steak") |
| 2 | Home screen widget (iOS/Android) |
| 3 | Apple Watch / Wear OS |
| 4 | AI-generated meal plan |
| 5 | Social sharing (summary card) |
| 6 | Multi-language (English) |
| 7 | Share diary with nutritionist |
| 8 | Nutrition label scanner (OCR) |
| 9 | PIX as payment method |
| 10 | Community / social features |
| 11 | Restaurant integration (menus) |
| 12 | Full offline mode |

---

## 14. Non-Functional Requirements

### 14.1 Performance

| Metric | Target | Acceptable |
|--------|--------|------------|
| Photo analysis time (AI) | < 3s | < 5s |
| App load time (cold start) | < 2s | < 3s |
| Food search time | < 500ms | < 1s |
| Barcode scan time | < 2s | < 3s |
| APK/IPA size | < 30MB | < 50MB |
| RAM usage | < 150MB | < 250MB |
| Frames per second (UI) | 60fps | 30fps minimum |
| Photo upload size (compressed) | < 500KB | < 1MB |

### 14.2 Availability and Scalability

| Requirement | Specification |
|-------------|---------------|
| Uptime | 99.5% |
| Concurrent users (MVP) | 1,000 |
| Concurrent users (v1.1) | 10,000 |
| API latency (P95) | < 500ms (except AI analysis) |
| Rate limit per user | 60 requests/min |
| AI analysis rate limit | 10 analyses/min per user |
| Database backup | Daily (Supabase automatic) |

### 14.3 Offline

#### MVP
- Local cache of current day's data (SQLite via Drift/Hive)
- Food search works offline (embedded TACO database ~2MB)
- Manual food logging works offline (syncs on reconnect)
- Photo analysis requires internet (show message)

#### v2.0
- Queue photos for analysis when reconnecting
- Cache recent days (last 7 days)
- Bidirectional sync with conflict resolution

### 14.4 Security

| Requirement | Implementation |
|-------------|----------------|
| Authentication | Supabase Auth (JWT) |
| Authorization | RLS (Row Level Security) by user_id |
| HTTPS | Required for all communications |
| Sensitive storage | Flutter Secure Storage for tokens |
| Photos | Signed URLs with expiration (1h) |
| API Keys (Gemini, GPT) | Backend only, never on client |
| Input validation | Backend sanitization (zod/joi) |
| SQL Injection | Prepared statements (Supabase default) |
| XSS | Not applicable (native mobile) |
| Certificate pinning | Implement for own API |
| Personal data | Encryption at rest (Supabase default) |
| Logs | No personal data in logs |
| Password | Minimum 8 characters, Supabase managed |

### 14.5 Analytics and Tracking

#### Events to Track

| Event | Properties |
|-------|------------|
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
| `account_deleted` | reason (if collected) |
| `paywall_shown` | source |
| `subscription_started` | plan, price |
| `subscription_cancelled` | reason |
| `notification_received` | type |
| `notification_opened` | type |

#### Tool
- **Mixpanel** (preferred) or **PostHog** (self-hosted)
- Respect LGPD opt-out
- Do not send PII (personally identifiable information) to analytics
- Anonymized user ID (hash)

### 14.6 Testing

| Type | Target Coverage | Tool |
|------|-----------------|------|
| Unit tests (Flutter) | 80%+ business logic | flutter_test |
| Unit tests (Node.js) | 80%+ | Jest |
| Widget tests (Flutter) | Main screens | flutter_test |
| Integration tests | Critical flows (onboarding, analysis, log) | integration_test |
| E2E (optional) | Smoke tests | Patrol or Appium |
| API tests | All endpoints | Supertest |
| Performance | AI analysis, search | k6 or Artillery |

### 14.7 CI/CD

| Stage | Tool | Trigger |
|-------|------|---------|
| Lint + Format | `flutter analyze`, ESLint | Push to any branch |
| Tests | `flutter test`, `jest` | Push to any branch |
| Build Android (APK/AAB) | Codemagic | Push to `main` or tag |
| Build iOS (IPA) | Codemagic | Push to `main` or tag |
| Deploy API | GitHub Actions → Railway/Render/Fly.io | Push to `main` |
| Deploy Supabase migrations | Supabase CLI | Push to `main` |
| Beta distribution | Codemagic / TestFlight / Google Play Internal | Tag `beta-*` |
| Store publication | Manual via Codemagic | Tag `release-*` |

### 14.8 Monitoring

| Component | Tool |
|-----------|------|
| Crashes (mobile) | Sentry |
| Errors (API) | Sentry |
| Uptime | UptimeRobot or Better Uptime |
| Performance (API) | Sentry Performance |
| Logs | Supabase Logs + Logflare |
| AI costs | Custom dashboard (tokens consumed) |

---

## Appendix A: Seed Data — Achievements

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

*(Note: Achievement names and descriptions are kept in Portuguese as they are user-facing strings in a Brazilian app.)*

## Appendix B: Complete TACO Categories

The TACO table (Tabela Brasileira de Composição de Alimentos — Brazilian Food Composition Table, by UNICAMP) contains ~597 foods organized in categories. The entire database should be imported as seed data for the `foods` table.

Download: https://www.cfn.org.br/wp-content/uploads/2017/03/taco_4_edicao_ampliada_e_revisada.pdf

Import format: convert PDF → CSV → SQL INSERT via migration script.

## Appendix C: Flutter Folder Structure

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

## Appendix D: Node.js Backend Structure

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
│   │   ├── analyzer.service.ts  (orchestrator with fallback)
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

> **This document is the single source of truth for PratoIA development.**
> Any changes must be versioned and approved before implementation.
>
> **Last updated:** 2026-03-03
