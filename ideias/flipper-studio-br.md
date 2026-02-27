# FotoPro BR — Business Plan
## AI Product Photography App for Brazilian Marketplace Sellers

**Date:** February 2026
**Status:** Planning / Pre-MVP

---

## 1. Concept & Market Analysis

### The Problem
Millions of Brazilian small sellers on Mercado Livre, Shopee, OLX, Enjoei, and Amazon BR take terrible product photos with their phones. Poor lighting, cluttered backgrounds, wrong aspect ratios. Professional photography costs R$50-200 per product — unaffordable for micro-sellers listing dozens of items.

### The Solution
An iOS app that transforms a quick phone photo into marketplace-ready product images in seconds:
1. Snap a photo of your product
2. AI removes background, fixes lighting, color-corrects
3. Choose a template (white background, lifestyle mockup, seasonal theme)
4. Export at the exact dimensions each marketplace requires
5. Share directly to Mercado Livre, Shopee, etc.

### Brazilian E-Commerce Market
- **Brazil e-commerce revenue:** ~$50B USD (2025), growing 15-20% YoY
- **Mercado Livre:** 220M+ registered users, dominant marketplace in LATAM, ~12M active sellers across region
- **Shopee Brazil:** Massive growth since 2019, estimated 3M+ sellers in BR
- **Amazon BR:** Growing but smaller, ~100K+ sellers
- **OLX/Enjoei:** Millions of casual C2C sellers
- **Estimated addressable market:** 5-10M individuals/small businesses selling online in Brazil

### Pain Point Severity
- Mercado Livre actively penalizes listings with poor photos (lower search ranking)
- Shopee promotes listings with white backgrounds and multiple angles
- Studies show products with professional photos sell 2-3x faster
- Most BR sellers are mobile-first (no DSLR, no Photoshop skills)

### Competitor Analysis (Brazil)
| Competitor | Weakness |
|---|---|
| **Remove.bg** | Background removal only, no mockups, no marketplace templates, $0.23/image |
| **Canva** | Generic design tool, not optimized for product photography |
| **PhotoRoom** | Best direct competitor globally, but no BR localization, pricing in USD ($9.99/mo), no marketplace-specific templates for ML/Shopee |
| **Fundo Branco (misc apps)** | Low quality, ad-heavy, no AI enhancement |
| **Manual photography services** | R$50-200/product, slow turnaround |

### Competitive Advantage
- **BR-first:** Portuguese UI, BRL pricing, marketplace-specific templates (Mercado Livre dimensions, Shopee requirements)
- **Price point:** R$19.90-39.90/mo vs PhotoRoom's ~R$60/mo equivalent
- **All-in-one:** Background removal + enhancement + mockups + marketplace export in one flow
- **Lifestyle mockups:** AI-generated scenes (product on marble table, in kitchen, being worn, etc.)

---

## 2. Core Features (MVP)

### v1.0 — MVP (8 weeks)
1. **Smart Capture** — Camera with alignment guides for common product types (shoes, electronics, clothing)
2. **Background Removal** — One-tap clean white/transparent background
3. **AI Enhancement** — Auto lighting correction, shadow generation, color balance
4. **Marketplace Templates:**
   - Mercado Livre: 1200×1200px primary, up to 12 images
   - Shopee: 800×800px minimum, white background preferred
   - Amazon BR: 1600×1600px minimum (for zoom), pure white background required
   - OLX: 1080×1080px
   - Enjoei: 1080×1080px
5. **Lifestyle Mockups** — AI-generated scenes: product on table, in-use context, flat lay, seasonal backgrounds
6. **Batch Processing** — Process 5-20 photos at once
7. **Export/Share** — Save to camera roll, share sheet, direct copy for marketplace upload

### v1.1 — Post-Launch
- Before/after comparison slider (great for marketing)
- Brand watermark overlay
- Photo templates with text overlay (sale banners, "frete grátis")
- Integration with Mercado Livre API for direct listing creation
- Android version

### v2.0 — Growth
- Video product showcase generation (short clips from photos)
- AI product description generation (from photo)
- Analytics (which photo styles convert better)
- Multi-user / team accounts for larger sellers

---

## 3. Tech Architecture

### iOS App
- **Language:** Swift + SwiftUI
- **Minimum iOS:** 16.0
- **Local processing:** Apple Vision framework for initial segmentation (fast preview), Core Image for basic adjustments
- **Networking:** URLSession + async/await
- **State:** SwiftData for local history, UserDefaults for preferences
- **Payments:** StoreKit 2 for subscriptions

### Backend
- **Runtime:** Python (FastAPI) on Google Cloud Run
- **Why Python:** Best Google AI SDK support, image processing libraries (Pillow)
- **Autoscaling:** Cloud Run min 0, max 20 instances (scale to zero when idle)
- **Storage:** Google Cloud Storage (temp images, 24h TTL for free tier, 7 days for paid)
- **Auth:** Firebase Auth (Apple Sign-In, email)
- **Database:** Firestore (user accounts, usage tracking, subscription status)
- **CDN:** Cloud CDN for serving processed images back to app

### Image Processing Pipeline

```
User Photo (HEIC/JPEG, 2-12MP)
    ↓
Upload to Cloud Storage (temp bucket)
    ↓
Cloud Run receives request
    ↓
Step 1: Background Removal
  → Gemini 3.1 Flash Image (cheapest, fast)
  → Prompt: "Remove background, keep product only, transparent/white bg"
    ↓
Step 2: Enhancement + Lighting
  → Gemini 3.1 Flash Image
  → Prompt: "Professional product photo, studio lighting, soft shadows"
    ↓
Step 3: Lifestyle Mockup (if requested)
  → Gemini 3 Pro Image (higher quality for creative generation)
  → Prompt: "Place [product] on [marble counter/wooden table/lifestyle scene]"
    ↓
Step 4: Resize to marketplace specs
  → Pillow (server-side, no API cost)
    ↓
Return processed images to app
```

### Google Image Generation API Comparison

| Model | Price/Image (1K res) | Price/Image (2K res) | Quality | Speed | Best For |
|---|---|---|---|---|---|
| **Gemini 3.1 Flash Image** | **$0.045** | $0.101 | Good | Fast | Background removal, basic enhancement |
| **Gemini 3 Pro Image** | $0.134 | $0.134 | Excellent | Medium | Lifestyle mockups, creative generation |
| **Gemini 3.1 Pro Image** | $0.134 | $0.134 | Excellent | Medium | Same as 3 Pro, newer |
| **Gemini 2.5 Flash Image** | ~$0.039 | — | Good | Fast | Budget option |
| **Gemini 2.0 Flash Image Gen** | ~$0.039 | — | Decent | Fast | Cheapest but older quality |
| **Imagen 3 (Vertex)** | ~$0.04 | — | Very Good | Medium | Dedicated image model |
| **Imagen 4 (Vertex)** | TBD (newer) | — | Best | Medium | Premium tier |

**Flex/Batch pricing** (50% off): Available for non-real-time processing. Could be used for batch jobs.

#### Recommended Strategy
- **Default pipeline:** Gemini 3.1 Flash Image ($0.045/image) for bg removal + enhancement
- **Premium mockups:** Gemini 3 Pro Image ($0.134/image) for lifestyle scenes
- **Typical user flow:** 1 bg removal + 1 enhancement + 1 mockup = ~$0.22 per product processed
- **Budget flow (bg removal only):** $0.045 per product
- **Use Flex/Batch** when user does batch processing (async, 50% discount): $0.022-$0.067 per image

#### Vertex AI vs Google AI Studio (Gemini API)
- **Vertex AI:** Production-grade, SLA, VPC, IAM, audit logs. Higher base prices but necessary for production.
- **Google AI Studio (free tier):** 1,500 requests/day free for Flash models. Good for development/testing only.
- **Decision:** Use Vertex AI for production. The free tier has rate limits too low for real users.

---

## 4. Cost Analysis

### Per-Image API Cost Breakdown

| Scenario | API Calls | Cost per Product |
|---|---|---|
| Basic (bg removal only) | 1× Flash Image | $0.045 (R$0.27) |
| Standard (bg removal + enhance) | 2× Flash Image | $0.090 (R$0.54) |
| Premium (bg + enhance + mockup) | 2× Flash + 1× Pro | $0.224 (R$1.34) |
| Batch basic (10 products) | 10× Flash (Flex) | $0.225 (R$1.35) |
| Batch premium (10 products) | 20× Flash + 10× Pro (Flex) | $1.12 (R$6.72) |

*Exchange rate assumption: 1 USD = 6.00 BRL*

### Monthly Infrastructure Costs

| Item | Cost/Month | Notes |
|---|---|---|
| Cloud Run | $0-50 | Scale to zero; ~$0.00002/request |
| Cloud Storage | $5-20 | ~100GB with 24h-7d TTL |
| Firestore | $0-10 | Free tier covers early stage |
| Firebase Auth | $0 | Free up to 10K MAU |
| Cloud CDN | $5-15 | Egress costs |
| Monitoring/Logging | $0-5 | Free tier usually sufficient |
| **Total infra (excl. API)** | **$10-100** | Scales with usage |

### Fixed Costs

| Item | Cost | Frequency |
|---|---|---|
| Apple Developer Program | $99 | Annual |
| Domain + email | ~$15 | Annual |
| App Store review tools | $0 | — |
| **Total fixed** | **~$114/yr** | — |

### Unit Economics

**Assumptions:**
- Average user processes 30 products/month (standard flow)
- API cost per user/month: 30 × $0.09 = $2.70 (R$16.20)
- Infra cost per user (amortized at 1000 users): ~$0.10/month

| Plan | Price (BRL) | API Cost | Gross Margin |
|---|---|---|---|
| Free (5 images/mo) | R$0 | R$2.70 | -R$2.70 (acquisition cost) |
| Básico (30 images/mo) | R$19.90 | R$16.20 | **R$3.70 (18.6%)** |
| Pro (100 images/mo) | R$39.90 | R$54.00 | **-R$14.10 (negative!)** |
| Pro (100 images, Flash only) | R$39.90 | R$27.00 | **R$12.90 (32.3%)** |

⚠️ **Critical insight:** At Pro tier with premium mockups, margins go negative. Solutions:
1. Pro tier uses Flash Image only (no Pro Image for mockups) — margin becomes 32%
2. Charge mockups separately (R$0.50-1.00 each as credits)
3. Increase Pro pricing to R$59.90
4. Use Flex/Batch pricing (50% off) for batch processing — cuts API cost in half

**Revised pricing model (hybrid credits):**

| Plan | Price (BRL) | Included | Extra Credits |
|---|---|---|---|
| Grátis | R$0 | 5 basic edits/month | — |
| Básico | R$19.90/mo | 30 basic edits + 5 mockups | R$0.50/edit, R$1.50/mockup |
| Pro | R$49.90/mo | 100 basic edits + 20 mockups | R$0.40/edit, R$1.00/mockup |
| Negócio | R$99.90/mo | 300 basic edits + 50 mockups | R$0.30/edit, R$0.80/mockup |

**Revised unit economics (Pro tier):**
- 100 basic edits: 100 × $0.045 = $4.50 (R$27)
- 20 mockups: 20 × $0.134 = $2.68 (R$16.08)
- Total API cost: R$43.08
- Revenue: R$49.90
- **Gross margin: R$6.82 (13.7%)** — still thin but viable with scale

**At scale (10K users, negotiated API pricing):** Google offers volume discounts at scale. Expect 20-40% reduction in per-token costs.

---

## 5. Monetization Strategy

### Primary: Freemium Subscription
- Free tier acts as acquisition funnel (5 images/month, watermarked)
- Subscription removes watermark, unlocks volume + mockups
- Annual discount: 2 months free (R$199/yr for Básico vs R$238.80 monthly)

### Secondary: Credit Packs (pay-as-you-go)
- For occasional sellers who don't want subscriptions
- R$9.90 for 10 basic edits
- R$29.90 for 30 basic edits + 5 mockups
- Higher margin than subscription (pricing includes premium)

### Tertiary: B2B / Agency Plan
- For photography studios, e-commerce agencies, dropshippers
- R$299.90/mo for 1000 edits + 200 mockups + API access
- White-label option at R$499.90/mo

### Apple's 30% Cut
All App Store subscription revenue loses 30% to Apple (15% after year 1 for small developers under $1M revenue).

**Adjusted margins (first year, 30% Apple cut):**
| Plan | Revenue after Apple | API Cost | Net Margin |
|---|---|---|---|
| Básico R$19.90 | R$13.93 | R$16.20 | **-R$2.27** ❌ |
| Pro R$49.90 | R$34.93 | R$43.08 | **-R$8.15** ❌ |

⚠️ **Apple's cut makes subscription-only model unprofitable at these prices!**

### Solutions:
1. **Web-based subscription** (bypass App Store for subscriptions, offer via website) — allowed by Apple's recent policy changes
2. **Raise prices:** Básico R$29.90, Pro R$69.90
3. **Optimize API costs:** Use Flash Image exclusively ($0.045/image), no Pro Image in pipeline
4. **Use on-device processing** for bg removal (Apple Vision / Core ML) — zero API cost for basic edits

### Revised Final Pricing (profitable)

| Plan | Price (BRL) | After Apple 30% | API Cost (Flash only) | Net Margin |
|---|---|---|---|---|
| Grátis | R$0 | — | R$1.35 (5 imgs) | -R$1.35 |
| Básico (50 edits) | R$29.90 | R$20.93 | R$13.50 | **R$7.43 (24.8%)** |
| Pro (150 edits) | R$59.90 | R$41.93 | R$40.50 | **R$1.43 (2.4%)** |
| Pro (150, on-device bg + cloud enhance) | R$59.90 | R$41.93 | R$20.25 | **R$21.68 (36.2%)** ✅ |

**Key decision: Use on-device Core ML for background removal, cloud API only for enhancement + mockups.**

This cuts API calls in half and makes the model profitable.

---

## 6. Optimized Tech Strategy

### Hybrid Processing (On-Device + Cloud)

```
On-Device (FREE, instant):
  - Background removal (Apple Vision / Core ML)
  - Basic cropping and resizing
  - Color correction (Core Image)
  - Marketplace template fitting

Cloud API (PAID, 2-5 seconds):
  - AI enhancement (lighting, shadows, professional look)
  - Lifestyle mockup generation
  - Advanced background replacement (custom scenes)
```

**Revised cost per product:**
- Basic edit (on-device only): **$0.00** ← game changer
- Enhanced (on-device bg + cloud enhance): **$0.045** (R$0.27)
- Premium mockup (cloud): **$0.045-0.134** (R$0.27-0.80)

**Revised unit economics with hybrid approach:**

| Plan | Price | After Apple | Cloud Cost | Net Margin |
|---|---|---|---|---|
| Grátis (10 basic edits) | R$0 | — | R$0 | R$0 (on-device!) |
| Básico (50 basic + 20 enhanced) | R$29.90 | R$20.93 | R$5.40 | **R$15.53 (51.9%)** ✅ |
| Pro (unlimited basic + 80 enhanced + 20 mockups) | R$59.90 | R$41.93 | R$13.28 | **R$28.65 (47.8%)** ✅ |

**This is the model.** On-device processing for the basics, cloud API for premium features.

---

## 7. Go-to-Market Strategy (Brazil)

### Phase 1: Pre-Launch (Weeks 1-4 of marketing)
1. **Instagram/TikTok content machine:**
   - Before/after reels showing terrible → professional product photos
   - "Tire uma foto ruim → FotoPro transforma" format
   - Target: 3 reels/week, BR e-commerce hashtags
   - Budget: R$0 (organic first)
2. **Waitlist landing page** (fotopro.app)
   - Email capture with "10 edições grátis no lançamento"
   - Goal: 1,000 emails before launch

### Phase 2: Soft Launch (Weeks 5-8)
1. **Beta with 50-100 real sellers**
   - Recruit from Mercado Livre seller communities (Facebook groups with 100K+ members)
   - Groups: "Vendedores do Mercado Livre", "Shopee Sellers Brasil", "Dropshipping Brasil"
   - Free Pro access during beta in exchange for feedback + testimonials
2. **YouTube tutorials in Portuguese**
   - "Como tirar fotos profissionais para Mercado Livre com IA"
   - "5 dicas para vender mais no Shopee — fotos que vendem"
   - SEO-optimized, evergreen content
3. **Partnerships:**
   - Contact Mercado Livre seller education programs
   - Approach Shopee University BR for content collaboration

### Phase 3: Public Launch (Week 9+)
1. **App Store Optimization (ASO)**
   - Keywords: "foto produto", "fundo branco", "mercado livre fotos", "foto profissional"
   - Portuguese description, BR-optimized screenshots
2. **Paid ads (small budget)**
   - Instagram/Facebook ads: R$500-1000/month targeting e-commerce sellers
   - Google Ads on "como tirar foto de produto" keywords
3. **Referral program**
   - "Convide um amigo, ganhe 10 edições grátis"
   - Viral loop: seller shares processed photo → friend sees quality → downloads app
4. **Influencer partnerships**
   - Micro-influencers in BR e-commerce niche (10K-50K followers)
   - Cost: R$200-500 per sponsored post or affiliate deal

### Phase 4: Growth (Month 3+)
- Android version (doubles TAM)
- Mercado Livre API integration (edit listing directly)
- B2B outreach to e-commerce agencies
- Expand to other LATAM markets (Argentina, Mexico, Colombia — all Mercado Libre)

---

## 8. Timeline & Milestones

### Week 1-2: Foundation
- [ ] Set up Xcode project (SwiftUI, iOS 16+)
- [ ] Firebase project setup (Auth, Firestore)
- [ ] Google Cloud project + Vertex AI API enabled
- [ ] Basic camera capture screen
- [ ] Cloud Run backend scaffold (FastAPI)

### Week 3-4: Core Pipeline
- [ ] On-device background removal (Apple Vision framework)
- [ ] Cloud API integration (Gemini 3.1 Flash Image)
- [ ] Image enhancement pipeline (prompt engineering)
- [ ] Basic result display + save to camera roll

### Week 5-6: Templates & UX
- [ ] Marketplace template system (ML, Shopee, Amazon, OLX dimensions)
- [ ] Lifestyle mockup generation (Gemini 3 Pro Image)
- [ ] Batch processing queue
- [ ] Polish UI/UX (onboarding, loading states, error handling)

### Week 7-8: Monetization & Polish
- [ ] StoreKit 2 subscription integration
- [ ] Usage tracking + limits (Firestore)
- [ ] Free tier watermark
- [ ] App Store listing, screenshots, preview video
- [ ] TestFlight beta distribution

### Week 9: Beta
- [ ] Recruit 50 beta sellers from Facebook groups
- [ ] Collect feedback, iterate
- [ ] Fix bugs, optimize prompts based on real product types

### Week 10: Launch
- [ ] App Store submission
- [ ] Marketing push (Instagram/TikTok reels, YouTube tutorial)
- [ ] Monitor crash reports, API costs, user behavior

### Post-Launch Monthly Goals
| Month | Target MAU | Target MRR (BRL) | Key Focus |
|---|---|---|---|
| 1 | 500 | R$1,000 | Product-market fit, retention |
| 2 | 2,000 | R$5,000 | Conversion optimization |
| 3 | 5,000 | R$15,000 | Android launch, paid ads |
| 6 | 20,000 | R$60,000 | Mercado Livre API integration |
| 12 | 50,000 | R$200,000 | LATAM expansion |

---

## 9. Risk Analysis

| Risk | Impact | Mitigation |
|---|---|---|
| Google API price increase | High | Abstract API layer, easy to swap to alternatives (Stability AI, DALL-E) |
| PhotoRoom enters BR market aggressively | Medium | First-mover in BR localization, marketplace templates, lower pricing |
| Apple rejects app | Low | Standard app, follows guidelines, no controversial content |
| Low conversion free→paid | High | Optimize free tier (enough value to hook, limits that drive upgrade) |
| API latency for BR users | Medium | Cloud Run in southamerica-east1 (São Paulo) |
| Image quality inconsistency | Medium | Extensive prompt engineering, quality validation layer |

---

## 10. Summary & Decision Points

### Why This Works
1. **Massive market:** 5-10M online sellers in Brazil, most mobile-first
2. **Clear pain point:** Bad photos = fewer sales, and everyone knows it
3. **No BR-focused competitor:** PhotoRoom is the closest but not localized
4. **Affordable tech:** Gemini Flash Image at $0.045/image makes unit economics work
5. **Hybrid on-device + cloud:** Keeps costs low, improves speed, enables generous free tier

### Key Decisions to Make
1. **App name:** FotoPro? FotoVende? SnapPro? (check BR trademark + App Store availability)
2. **Solo dev or team?** MVP is feasible solo in 10 weeks for experienced iOS dev
3. **Pricing validation:** Survey 50 BR sellers on willingness to pay R$29.90/mo
4. **Imagen 4 vs Gemini Flash Image:** When Imagen 4 pricing is available, compare quality/cost. Gemini Flash Image is the safe bet for MVP.

### Startup Cost to Launch
| Item | Cost |
|---|---|
| Apple Developer | R$600 ($99) |
| Domain | R$80 |
| Cloud infra (first 3 months) | R$300 |
| Marketing (first month) | R$1,000 |
| **Total to launch** | **~R$2,000 (~$330 USD)** |

*Excludes developer time. If hiring, estimate R$15-25K for a freelance iOS dev for the MVP.*

---

## Appendix: API Pricing Reference (Feb 2026)

### Vertex AI — Image Generation Models

| Model | Input Cost | Output Image Cost (1K) | Output Image Cost (2K) | Output Image Cost (4K) | Batch Discount |
|---|---|---|---|---|---|
| Gemini 3.1 Flash Image | $0.50/1M tokens | $0.045 | $0.101 | $0.15 | 50% off |
| Gemini 3 Pro Image | $2/1M tokens | $0.134 | $0.134 | $0.24 | 50% off |
| Gemini 3.1 Pro Image | $2/1M tokens | $0.134 | $0.134 | $0.24 | 50% off |
| Gemini 2.5 Flash Image | $0.30/1M tokens | ~$0.039 | — | — | 50% off |
| Gemini 2.0 Flash Image Gen | $0.15/1M tokens | ~$0.039 | — | — | — |
| Imagen 3 | See Imagen pricing | ~$0.04 | — | — | — |
| Imagen 4 | See Imagen pricing | TBD | — | — | — |

**Recommendation for MVP:** Gemini 3.1 Flash Image (best price/quality ratio at $0.045/image for 1K resolution, sufficient for marketplace photos).
