# PratoIA — Pesquisa de Mercado & Análise Técnica

> Última atualização: Março 2026

---

## Índice
1. [Cal AI — Deep Dive](#1-cal-ai--deep-dive)
2. [Vision AI Models para Food Recognition](#2-vision-ai-models-para-food-recognition)
3. [Mercado Brasileiro](#3-mercado-brasileiro)
4. [Arquitetura Técnica Recomendada](#4-arquitetura-técnica-recomendada)
5. [Modelos de Monetização](#5-modelos-de-monetização)
6. [Conclusões e Próximos Passos](#6-conclusões-e-próximos-passos)

---

## 1. Cal AI — Deep Dive

### Como Funciona
Cal AI é um calorie tracker com AI que funciona assim:
1. **Foto do prato** → usuário tira foto com câmera do celular
2. **Depth sensor** (LiDAR em iPhones Pro) → estima volume dos alimentos
3. **AI Vision** → identifica os alimentos na imagem
4. **Cálculo nutricional** → cruza com banco de dados (provavelmente USDA) para retornar calorias, proteína, carboidratos e gordura
5. **Logging automático** → registra no diário alimentar

### Features Principais
- 📸 Snap-to-track (foto → macros em segundos)
- 📏 Depth sensor para estimativa de volume (iPhone Pro c/ LiDAR)
- 🍳 Receitas customizadas e alimentos manuais
- 🏋️ Integração com fitness products (Apple Health, provavelmente Fitbit/Garmin)
- 📊 Dashboard de macros diários
- 🎯 Metas personalizadas (perda de peso, bulking, manutenção)

### Pricing (estimado, baseado em apps similares)
Cal AI usa modelo freemium com subscription:
- **Free tier:** Limitado (provavelmente ~3 scans/dia)
- **Premium:** ~$19.99/mês ou ~$99.99/ano (padrão do mercado US)
- **Lifetime:** Possivelmente oferecido em promoções (~$149-199)

> Nota: Apps de calorie tracking com AI no mercado US cobram entre $9.99-29.99/mês. Cal AI se posiciona no tier premium.

### O Que Usuários Amam (baseado em testimonials)
- ✅ Funciona de verdade — resultados reais de perda de peso ("lost 17 lbs")
- ✅ Simplicidade — tira foto e pronto
- ✅ Estético e visual
- ✅ Ajuda a ter consciência alimentar sem obsessão
- ✅ Bom para bulking também, não só dieta

### O Que Provavelmente Odeiam (padrão do mercado)
- ❌ Imprecisão com pratos misturados (stews, curries)
- ❌ Não reconhece comidas regionais/locais
- ❌ Preço alto para mercados fora dos EUA
- ❌ Depth sensor só funciona em iPhones Pro
- ❌ Precisa de internet para funcionar
- ❌ Porções estimadas nem sempre batem

### Revenue & Downloads (estimativas de mercado)
- Cal AI é relativamente novo mas cresce rápido no nicho "AI calorie tracker"
- Mercado global de health/fitness apps: ~$15B (2025)
- Calorie tracking apps especificamente: ~$1.5B
- Competidores diretos com AI: Lensa (Prisma Labs approach), SnapCalorie, Bite AI
- Estimativa: 100K-500K downloads, $1-5M ARR (app novo mas viral no TikTok)

### Fraquezas para o Mercado BR
- 🚫 Sem suporte a português
- 🚫 Banco de dados americano (USDA) — não conhece comida brasileira
- 🚫 Pricing em dólar — inacessível para maioria dos brasileiros
- 🚫 Sem PIX
- 🚫 Não reconhece: marmitex, açaí, coxinha, pão de queijo, feijoada, tapioca, etc.

---

## 2. Vision AI Models para Food Recognition

### Comparativo de Modelos

| Modelo | Input Cost (1M tokens) | Image Cost (~1K tokens) | Accuracy (food) | Latency | Portion Estimation |
|--------|----------------------|------------------------|-----------------|---------|-------------------|
| **GPT-4.1** | $2.00 | ~$0.002 | ⭐⭐⭐⭐⭐ | ~2s | Boa com prompt engineering |
| **GPT-4.1 mini** | $0.40 | ~$0.0004 | ⭐⭐⭐⭐ | ~1s | Boa |
| **GPT-4.1 nano** | $0.10 | ~$0.0001 | ⭐⭐⭐ | ~0.5s | Razoável |
| **Gemini 2.0 Flash** | $0.10* | ~$0.0001 | ⭐⭐⭐⭐ | ~0.8s | Boa |
| **Gemini 2.0 Pro** | $1.25* | ~$0.001 | ⭐⭐⭐⭐⭐ | ~2s | Muito boa |
| **Claude Sonnet 4** | $3.00 | ~$0.003 | ⭐⭐⭐⭐ | ~2s | Boa |
| **Claude Haiku** | $0.25 | ~$0.00025 | ⭐⭐⭐ | ~0.5s | Razoável |

*Preços Google AI Studio podem variar; free tier generoso disponível.

### Custo por Imagem (detalhado)
Para uma análise de foto de comida, estimamos:
- **Input:** ~800 tokens (imagem comprimida) + ~200 tokens (prompt com contexto)
- **Output:** ~300 tokens (JSON com alimentos, quantidades, macros)
- **Total por request:** ~1,300 tokens

#### Custo por usuário/mês (7 fotos/dia × 30 dias = 210 fotos)

| Modelo | Custo/foto | Custo/usuário/mês | 10K usuários/mês |
|--------|-----------|-------------------|------------------|
| **GPT-4.1 nano** | $0.0003 | $0.06 | $630 |
| **Gemini 2.0 Flash** | $0.0003 | $0.06 | $630 |
| **GPT-4.1 mini** | $0.001 | $0.21 | $2,100 |
| **Claude Haiku** | $0.0006 | $0.13 | $1,260 |
| **GPT-4.1** | $0.005 | $1.05 | $10,500 |

### 🏆 Recomendação: Gemini 2.0 Flash (primário) + GPT-4.1 nano (fallback)

**Por quê Gemini Flash:**
- Custo praticamente zero ($0.06/usuário/mês)
- Free tier do Google generoso para bootstrap
- Boa accuracy para food recognition
- Baixa latência (<1s)
- Suporta imagens nativamente
- Bom com comida latina/brasileira (treinado em dados diversos)

**GPT-4.1 nano como fallback:**
- Mesmo range de preço
- Boa alternativa se Gemini falhar
- OpenAI tem uptime excelente

### APIs Especializadas em Food Recognition

| API | Foco | Pricing | Vantagem | Desvantagem |
|-----|------|---------|----------|-------------|
| **Foodvisor** | App + API de food recognition | Enterprise pricing | Muito preciso, dados nutricionais inclusos | Caro, focado em mercado europeu |
| **LogMeal** | API de detecção de comida | Free trial 30 dias, depois enterprise | Deep learning especializado em comida | Sem dados BR, pricing opaco |
| **Nutritionix** | Database nutricional + NLP | $0.004/call (Track API) | Enorme database, NLP para texto | Focado em US, sem vision AI |
| **Passio AI** | SDK mobile para food recognition | Enterprise | SDK nativo, funciona offline | Muito caro para startups |
| **Clarifai** | General + food vision | $0.0012/operation | Modelos pré-treinados | Genérico, não especializado |

**Conclusão sobre APIs especializadas:**
- São caras e focadas em mercados desenvolvidos
- Nenhuma suporta comida brasileira nativamente
- Melhor usar LLMs multimodais (Gemini/GPT) com prompt engineering + nosso próprio banco BR
- Custo é 10-100x menor usando LLMs diretamente

### Prompt Engineering Strategy
```
Analyze this food photo. Identify each food item, estimate portion size in grams.
Return JSON: { "items": [{ "name": "...", "name_pt": "...", "grams": N, "confidence": 0.0-1.0 }] }
Context: This is likely Brazilian food. Common items include rice, beans, farofa, salad, meat.
Be specific about Brazilian dishes (e.g., "feijoada" not "bean stew").
```

Depois, cruzar os itens identificados com o banco TACO/TBCA local para dados nutricionais precisos.

---

## 3. Mercado Brasileiro

### Panorama do Mercado de Fitness Apps no Brasil

- 🇧🇷 Brasil: **215M habitantes**, ~170M smartphones
- 📱 Brasil é o **3º maior mercado de apps** do mundo
- 🏋️ Mercado fitness brasileiro: ~R$12B/ano (academias + digital)
- 📈 Health & fitness apps crescem ~20% ao ano no BR
- 💪 Brasil tem a **2ª maior quantidade de academias do mundo** (depois dos EUA)
- 🍽️ Mercado de diet/nutrition apps no BR: estimado R$500M-1B

### Competidores Existentes no Brasil

#### Tecnonutri 🇧🇷
- **O que é:** App brasileiro de nutrição e dieta
- **Pontos fortes:** Database brasileiro, em português, nutricionistas reais
- **Pontos fracos:** UI datada, sem AI, manual demais, experiência ruim
- **Pricing:** Freemium, planos ~R$29.90/mês
- **Market share:** Maior app BR nativo, mas perdendo relevância

#### FatSecret Brasil
- **O que é:** Versão BR do FatSecret global
- **Pontos fortes:** Grande database, barcode scanner, gratuito
- **Pontos fracos:** UX genérica, não é "Brazilian first", sem AI
- **Pricing:** Gratuito com ads, Premium ~R$34.90/ano
- **Market share:** Popular entre quem busca gratuito

#### MyFitnessPal (BR)
- **O que é:** Líder global, disponível em português
- **Pontos fortes:** Enorme database, integrações, comunidade
- **Pontos fracos:** Caro ($19.99/mês USD), pesado, database BR incompleto
- **Pricing:** Premium ~R$69.90/mês (absurdo para BR)
- **Market share:** Usado por público premium/fitness hardcore

#### Yazio
- **O que é:** App alemão popular no BR
- **Pontos fortes:** UI bonita, plano de refeições
- **Pontos fracos:** Sem AI vision, database BR limitado
- **Pricing:** ~R$39.90/mês

#### Nenhum oferece: 📸 AI food recognition para comida brasileira

### O Que Falta no Mercado Brasileiro
1. **AI Vision para comida BR** — ninguém faz isso
2. **Database brasileiro completo** — maioria usa USDA adaptado
3. **Preço acessível** — apps importados custam R$40-70/mês
4. **PIX nativo** — pagamento preferido dos brasileiros
5. **Comida de verdade** — marmitex, self-service por kg, comida de rua
6. **Offline capability** — muitas regiões com internet instável
7. **UX moderna em português** — Tecnonutri é datado, resto é traduzido

### 🗃️ Bancos de Dados de Alimentos Brasileiros

#### TACO (Tabela Brasileira de Composição de Alimentos)
- **Fonte:** UNICAMP/NEPA
- **Alimentos:** ~597 alimentos analisados
- **Dados:** Macro e micronutrientes por 100g
- **Formato:** PDF e planilhas (não tem API oficial)
- **Status:** Última edição 4ª (2011) — desatualizado mas ainda referência
- **Como usar:** Baixar tabela, converter para JSON/PostgreSQL, servir via API própria
- **Link:** https://www.cfn.org.br/wp-content/uploads/2017/03/taco_4_edicao_ampliada_e_revisada.pdf

#### TBCA (Tabela Brasileira de Composição de Alimentos) — USP
- **Fonte:** USP/BRASILFOODS
- **Alimentos:** ~4,000+ alimentos (muito mais completa que TACO)
- **Dados:** Composição química completa, inclui preparações
- **Formato:** Consulta web em tbca.net.br (sem API pública)
- **Status:** Atualizada continuamente (ativa em 2025)
- **Como usar:** Web scraping da tabela OU contatar USP para dados bulk
- **Vantagem:** Inclui pratos preparados (feijoada, arroz com feijão, etc.)

#### IBGE — POF (Pesquisa de Orçamentos Familiares)
- **O que tem:** Dados de consumo alimentar da população brasileira
- **Útil para:** Entender porções típicas brasileiras
- **Formato:** Microdados disponíveis no site do IBGE
- **Link:** https://www.ibge.gov.br/estatisticas/sociais/saude/24786-pesquisa-de-orcamentos-familiares-2.html

### Estratégia de Database
1. **Base:** TBCA (~4K alimentos) como fonte primária
2. **Complemento:** TACO para validação cruzada
3. **IBGE POF:** Para porções típicas brasileiras
4. **Crowdsourcing:** Usuários validam e adicionam alimentos
5. **AI-generated:** Para pratos compostos que não estão na tabela

### Tamanho da Oportunidade

| Métrica | Valor |
|---------|-------|
| Smartphones no Brasil | ~170M |
| Pessoas que fazem dieta regularmente | ~40M |
| Usuários de fitness apps | ~20M |
| TAM (calorie tracking) | ~R$2B/ano |
| SAM (público disposto a pagar) | ~R$400M/ano |
| SOM realista (ano 1-2) | R$2-10M/ano |
| Meta: 50K usuários pagantes × R$14.90/mês | R$8.9M ARR |

---

## 4. Arquitetura Técnica Recomendada

### Stack

```
┌─────────────────────────────────────────────┐
│                 FLUTTER APP                  │
│  (iOS + Android, câmera, offline cache)     │
├─────────────────────────────────────────────┤
│              SUPABASE                        │
│  ┌──────────┬──────────┬──────────────┐     │
│  │   Auth   │ Storage  │  PostgreSQL  │     │
│  │ (social  │ (food    │  (users,     │     │
│  │  login)  │  photos) │   meals,     │     │
│  │          │          │   foods)     │     │
│  └──────────┴──────────┴──────────────┘     │
├─────────────────────────────────────────────┤
│           NODE.JS API (Edge Functions)       │
│  ┌──────────────────────────────────────┐   │
│  │  Image Processing Pipeline            │   │
│  │  1. Receive photo                     │   │
│  │  2. Compress/resize (max 1024px)      │   │
│  │  3. Send to Gemini Flash              │   │
│  │  4. Parse food items                  │   │
│  │  5. Match with TACO/TBCA DB           │   │
│  │  6. Calculate macros                  │   │
│  │  7. Return result + cache             │   │
│  └──────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│         AI PROVIDERS (external)              │
│  ┌────────────┐  ┌────────────┐             │
│  │ Gemini     │  │ GPT-4.1    │             │
│  │ Flash 2.0  │  │ nano       │             │
│  │ (primary)  │  │ (fallback) │             │
│  └────────────┘  └────────────┘             │
└─────────────────────────────────────────────┘
```

### Image Processing Pipeline (detail)

```
User snaps photo
       │
       ▼
[Flutter] Compress to JPEG, max 1024x1024, quality 80%
       │
       ▼
[Flutter] Upload to Supabase Storage (meals/{user_id}/{date}/{uuid}.jpg)
       │
       ▼
[Edge Function] Receive image URL + user context
       │
       ▼
[Edge Function] Call Gemini Flash 2.0 Vision API
  - System prompt: Brazilian food expert
  - Input: image + structured prompt
  - Output: JSON { items: [{ name, name_pt, grams, confidence }] }
       │
       ▼
[Edge Function] Match items against TACO/TBCA database
  - Fuzzy matching on name_pt
  - Calculate macros based on grams × nutrients/100g
       │
       ▼
[Edge Function] Return to app:
  {
    "meal_id": "uuid",
    "items": [
      {
        "name_pt": "Arroz branco",
        "grams": 150,
        "calories": 193,
        "protein": 4.1,
        "carbs": 41.3,
        "fat": 0.5,
        "confidence": 0.92
      },
      ...
    ],
    "total": { "calories": 650, "protein": 35, "carbs": 80, "fat": 18 }
  }
       │
       ▼
[Flutter] Show results, user can adjust portions, save to daily_log
```

### Database Schema (PostgreSQL/Supabase)

```sql
-- Users
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT UNIQUE,
  name TEXT,
  created_at TIMESTAMPTZ DEFAULT now(),
  goal TEXT CHECK (goal IN ('lose', 'maintain', 'gain')),
  target_calories INT,
  target_protein INT,
  target_carbs INT,
  target_fat INT,
  height_cm INT,
  weight_kg DECIMAL(5,2),
  birth_date DATE,
  sex TEXT CHECK (sex IN ('M', 'F')),
  activity_level TEXT CHECK (activity_level IN ('sedentary', 'light', 'moderate', 'active', 'very_active')),
  subscription_tier TEXT DEFAULT 'free',
  subscription_expires_at TIMESTAMPTZ
);

-- Brazilian food database (imported from TACO/TBCA)
CREATE TABLE foods (
  id SERIAL PRIMARY KEY,
  name_pt TEXT NOT NULL,
  name_en TEXT,
  source TEXT CHECK (source IN ('taco', 'tbca', 'user', 'ai')),
  category TEXT, -- grãos, carnes, frutas, etc.
  per_100g JSONB NOT NULL, -- { calories, protein, carbs, fat, fiber, sodium, ... }
  common_portion_g INT, -- porção comum em gramas
  verified BOOLEAN DEFAULT false,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Meals (one per eating occasion)
CREATE TABLE meals (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  meal_type TEXT CHECK (meal_type IN ('breakfast', 'lunch', 'dinner', 'snack')),
  photo_url TEXT,
  ai_raw_response JSONB, -- resposta bruta do AI para debug
  logged_at TIMESTAMPTZ DEFAULT now(),
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Meal items (foods within a meal)
CREATE TABLE meal_items (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  meal_id UUID REFERENCES meals(id) ON DELETE CASCADE,
  food_id INT REFERENCES foods(id),
  custom_name TEXT, -- se food_id for null (comida não encontrada)
  grams DECIMAL(7,2) NOT NULL,
  calories DECIMAL(7,2),
  protein DECIMAL(7,2),
  carbs DECIMAL(7,2),
  fat DECIMAL(7,2),
  ai_confidence DECIMAL(3,2), -- 0.00-1.00
  manually_adjusted BOOLEAN DEFAULT false
);

-- Daily summary (materialized/cached)
CREATE TABLE daily_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  date DATE NOT NULL,
  total_calories DECIMAL(7,2) DEFAULT 0,
  total_protein DECIMAL(7,2) DEFAULT 0,
  total_carbs DECIMAL(7,2) DEFAULT 0,
  total_fat DECIMAL(7,2) DEFAULT 0,
  meal_count INT DEFAULT 0,
  water_ml INT DEFAULT 0,
  notes TEXT,
  UNIQUE(user_id, date)
);

-- User custom foods
CREATE TABLE user_foods (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  name_pt TEXT NOT NULL,
  per_100g JSONB NOT NULL,
  common_portion_g INT,
  created_at TIMESTAMPTZ DEFAULT now()
);

-- Indexes
CREATE INDEX idx_meals_user_date ON meals(user_id, logged_at);
CREATE INDEX idx_daily_logs_user_date ON daily_logs(user_id, date);
CREATE INDEX idx_foods_name ON foods USING gin(name_pt gin_trgm_ops);
```

### Storage Strategy
- **Photos:** Supabase Storage (S3-compatible)
  - Path: `meals/{user_id}/{YYYY-MM}/{uuid}.jpg`
  - Compress client-side: max 1024px, JPEG quality 80% (~100-200KB)
  - Auto-delete after 90 days (free tier), keep indefinitely (premium)
  - CDN via Supabase (built-in)
- **Cost estimate:** 10K users × 7 photos/day × 150KB × 30 days = ~315GB/month
  - Supabase: ~$25/month for storage at this scale

### Offline Capability
1. **SQLite local** (via Drift/sqflite in Flutter) — cache foods DB, recent meals
2. **Photo queue** — photos saved locally, synced when online
3. **Offline food search** — full TACO/TBCA cached locally (~2MB as JSON)
4. **Manual entry always works** — no AI needed offline
5. **Sync strategy:** Optimistic writes → queue → sync on reconnect

### Key Flutter Packages
```yaml
dependencies:
  supabase_flutter: ^2.0.0
  camera: ^0.11.0
  image_picker: ^1.0.0
  image: ^4.0.0          # compression
  sqflite: ^2.3.0         # offline cache
  connectivity_plus: ^6.0.0
  fl_chart: ^0.68.0       # nutrition charts
  google_fonts: ^6.0.0
  riverpod: ^2.5.0        # state management
  go_router: ^14.0.0
  purchases_flutter: ^7.0.0  # RevenueCat for IAP
```

---

## 5. Modelos de Monetização

### Como Competidores Monetizam

| App | Modelo | Preço | Sucesso |
|-----|--------|-------|---------|
| Cal AI | Subscription | ~$19.99/mês | Alto (mercado US) |
| MyFitnessPal | Freemium + Sub | $19.99/mês | Alto mas em declínio |
| Yazio | Subscription | €6.99/mês | Médio |
| FatSecret | Ads + Premium | $6.99/mês | Baixo |
| Tecnonutri | Freemium | R$29.90/mês | Médio-baixo |

### Modelo Recomendado para PratoIA 🇧🇷

#### Tier Gratuito (aquisição)
- 3 análises de foto por dia
- Diário alimentar completo (manual)
- Database TACO/TBCA
- Histórico de 7 dias

#### Tier Premium — R$14.90/mês ou R$99.90/ano
- ♾️ Análises de foto ilimitadas
- Histórico completo
- Relatórios semanais/mensais
- Exportar dados
- Sem ads
- Receitas com macros
- Metas personalizadas avançadas

#### Tier Anual com Desconto
- R$99.90/ano (R$8.33/mês) — ~45% desconto
- Trial de 7 dias grátis

### Pagamento no Brasil
- **PIX obrigatório** — 70%+ dos brasileiros preferem PIX
- **Cartão de crédito** — via Stripe BR ou RevenueCat (App Store/Play Store)
- **App Store/Play Store IAP** — obrigatório para iOS (30% taxa Apple)
- **Boleto** — ainda relevante para assinatura anual
- **Nota:** Usar RevenueCat para gerenciar subscriptions cross-platform

### Considerações Brasil
- ❌ **Subscription fatigue** é real — brasileiro tem Netflix, Spotify, gym, e resiste a mais uma assinatura
- ✅ **R$14.90/mês** é o sweet spot — preço de um lanche, não de um software
- ✅ **Ano com desconto agressivo** converte bem
- ✅ **Free tier generoso** é essencial para viral growth
- ✅ **PIX** reduz fricção de pagamento dramaticamente

### Unit Economics (projeção)

| Métrica | Valor |
|---------|-------|
| CAC (custo aquisição) | R$5-15 (orgânico/social) |
| LTV (12 meses) | R$120-150 |
| Custo AI/usuário/mês | R$0.30 (~$0.06) |
| Custo infra/usuário/mês | R$0.50 |
| Margem por usuário premium | ~95% |
| Breakeven | ~5K usuários premium |

---

## 6. Conclusões e Próximos Passos

### Por Que PratoIA Tem Chance

1. **Timing perfeito** — AI food recognition amadureceu em 2024-2025, custo despencou
2. **Zero competição** — nenhum app faz AI food tracking para comida brasileira
3. **Mercado enorme** — 40M+ brasileiros fazendo dieta, maioria sem ferramenta boa
4. **Custo operacional baixo** — $0.06/usuário/mês em AI é ridiculamente barato
5. **Diferenciação clara** — TACO/TBCA + comida BR = impossível de copiar rápido

### Riscos

1. **Cal AI ou MyFitnessPal adicionar suporte BR** — possível mas improvável a curto prazo
2. **Accuracy do AI** — food recognition ainda erra, especialmente pratos misturados
3. **Adoção** — convencer brasileiro a pagar por app de dieta
4. **Database** — TACO/TBCA não cobrem tudo, precisa de curadoria contínua

### MVP Recomendado (4-6 semanas)

**Semana 1-2:**
- [ ] Setup Flutter + Supabase
- [ ] Importar TACO/TBCA para PostgreSQL
- [ ] Tela de câmera + upload
- [ ] Integração Gemini Flash 2.0

**Semana 3-4:**
- [ ] Diário alimentar (daily log)
- [ ] Dashboard de macros
- [ ] Busca manual de alimentos
- [ ] Ajuste de porções pós-AI

**Semana 5-6:**
- [ ] Onboarding (metas, dados pessoais)
- [ ] Cálculo de TDEE
- [ ] Auth (Google/Apple sign-in via Supabase)
- [ ] Polish UI + testes

**Pós-MVP:**
- [ ] Subscription (RevenueCat + PIX)
- [ ] Offline mode
- [ ] Apple Health integration
- [ ] Barcode scanner
- [ ] Receitas
- [ ] Social/compartilhar

---

> **Bottom line:** O mercado está aberto. A tecnologia está barata. A oportunidade é real. Bora construir. 🚀
