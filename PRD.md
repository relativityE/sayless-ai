# SpeakSharp Product Requirements Document

**Version 6.18** | **Last Updated: August 14, 2025**

---

## 🔄 Recent Updates (v6.18)
*August 14, 2025*

- **New Transcription Engine:** Implemented a new, flexible `TranscriptionService` to handle speech-to-text processing. This service is designed with a two-phase approach:
    - **Phase 1 (Current):** Uses AssemblyAI for cloud-based transcription, enabling rapid development and testing.
    - **Phase 2 (Planned):** Will use Whisper.cpp for on-device, private transcription.
- **UI for Mode Switching:** Added a toggle in the session sidebar to allow users to switch between "local" and "cloud" transcription modes.
- **Documentation Overhaul:** Updated `README.md` and `System Architecture.md` to reflect the new transcription architecture, including updated diagrams, technology stack, and deployment instructions. Added a security alert for production keys.

---

## 🎯 Executive Summary

### Product Vision
SpeakSharp is a **privacy-first, real-time speech analysis tool** that empowers users to become more confident and articulate speakers. By providing instant, on-device feedback on filler word usage and speaking pace — without storing user audio — we enable practice that is both effective and secure.

### Business Value & Competitive Edge
Our competitive advantage is **speed + privacy**. Users experience an immediate "aha" moment in their first session, driving free-to-paid conversions.

### Go-to-Market Strategy
```
Pre-MVP    → Build audience via Reddit, SEO articles, email capture
Launch     → Google Ads, organic Reddit traffic, Product Hunt
Growth     → SEO expansion, retargeting ads, coach partnerships
```

### Primary Success Metrics
```
📊 Homepage → Signup Conversion:  15%+
💰 Free → Paid Conversion:       5%+
🔄 Returning Monthly Users:      40%+
✅ Session Completion Rate:      80%+
```

---

## 💰 Pricing Model

```
┌─────────────┬──────────────┬───────────────────────────────────────┐
│    TIER     │    PRICE     │               FEATURES                │
├─────────────┼──────────────┼───────────────────────────────────────┤
│    FREE     │     $0       │ • 2-min trial session                 │
│             │              │ • 10 mins/month logged in             │
│             │              │ • Last 3 sessions saved               │
│             │              │ • 5 custom words                      │
│             │              │ • Basic analytics                     │
├─────────────┼──────────────┼───────────────────────────────────────┤
│    PRO      │   $7.99      │ • Unlimited sessions                  │
│             │              │ • Unlimited custom words              │
│             │              │ • Full analytics history              │
│             │              │ • Improvement tracking                │
│             │              │ • PDF export                          │
│             │              │ • High-accuracy cloud transcription   │
│             │              │ • Download audio locally              │
└─────────────┴──────────────┴───────────────────────────────────────┘
```

---

## 🔒 Privacy Policy

**What we DON'T store:**
- ❌ Audio recordings (never saved on servers)

**What we DO store:**
- ✅ Filler word counts
- ✅ Session duration
- ✅ Speaking pace
- ✅ Timestamps

---

## 🛠️ Technology Stack

### Core Technologies
```
Frontend        → React + Vite
Styling         → Tailwind CSS + shadcn/ui  
Auth/Database   → Supabase
Speech API      → TranscriptionService (AssemblyAI / Whisper.cpp)
Payments        → Stripe
Monitoring      → Sentry
Analytics       → PostHog
Hosting         → Vercel
```

### Scalability Architecture
**Speech Processing:**
- **Phase 1 (Current):** AssemblyAI for cloud-based transcription.
- **Phase 2 (Planned):** Whisper.cpp for local, on-device transcription to ensure privacy.

**Scaling Strategy:**
- Client-heavy architecture minimizes server load
- Serverless functions auto-scale for premium features
- Managed services handle scaling automatically

---

## Test Approach

Our project employs a sophisticated, hybrid testing strategy to balance speed and accuracy. It consists of two parallel tracks: a primary suite for fast feedback and a specialized suite for features that are difficult to test in a simulated environment.

### 1. The Main Test Suite: **Vitest + JSDOM**

This is the primary testing stack for the majority of the application.

*   **Vite**: Acts as the core build tool. When you run the tests, Vitest uses Vite's engine to compile and process the React code and tests.
*   **Vitest**: Our main **test runner**. `pnpm test` executes all `*.test.jsx` files.
*   **JSDOM**: Vitest runs its tests in a **simulated browser environment** called JSDOM. It's fast but is not a real browser. This was identified as the source of instability for browser-native APIs like the ones used in the `TranscriptionService`.

### 2. The Special Case Test: **Playwright + Real Browser**

For features that are untestable in JSDOM, we migrate them to a separate, more robust environment.

*   **Playwright**: This is our secondary **test runner**, used for specific end-to-end or component-in-browser tests. It controls a **real, full-featured browser** (Chromium), avoiding JSDOM's limitations.
*   **Standalone Harness**: We create tiny, self-contained React applications (`playwright-tests/harness/`) to host the component or hook under test.
*   **Embedded Server**: The Playwright test itself builds the harness using a dedicated Vite config (`vite.harness.config.ts`) and serves it using a lightweight, built-in HTTP server. This makes the test completely self-contained and independent of the main dev server.

### Summary of Tools

| Tool          | Role                                           | When It's Used                                               |
| :------------ | :--------------------------------------------- | :----------------------------------------------------------- |
| **Vite**      | Core build engine.                             | Used by `pnpm run dev`, Vitest, and the Playwright harness build. |
| **Vitest**    | Main test runner for fast unit/integration tests. | `pnpm test`                                                  |
| **JSDOM**     | Simulated browser for Vitest.                  | The environment for all Vitest tests.                        |
| **Playwright**| Secondary, end-to-end test runner.             | For specific unstable features (`npx playwright test`).        |

This hybrid approach allows us to maintain a fast and efficient development cycle for most of the codebase while ensuring that complex, browser-specific features are tested with the accuracy and stability of a real browser environment.

---

## 🗓️ Development Roadmap

### PHASE 1 — MVP FOUNDATION (Weeks 1-3) - 90% Complete
- **Must Have:**
    - `✅` **[M]** Implement core backend services (Supabase Auth & DB).
    - `✅` **[M]** Implement `TranscriptionService` with AssemblyAI provider.
    - `✅` **[M]** Integrate `TranscriptionService` into the main session page.
    - `✅` **[M]** Implement Stripe payment flow for Pro tier.
    - `✅` **[M]** Set up Sentry for error monitoring.
    - `✅` **[M]** Set up PostHog for product analytics.
- **Should Have:**
    - `✅` **[S]** Develop a responsive UI with a professional light theme.
    - `[ ]` **[S]** Comprehensive QA and performance tuning.
- **Could Have:**
    - `[ ]` **[C]** A/B testing setup with PostHog.
- **Won't Have (for this phase):**
    - `[ ]` **[W]** On-device transcription (moved to Phase 2).

### PHASE 2 — PRIVACY & POLISH (Months 1-3) - 0% Complete
- **Must Have:**
    - `[ ]` **[M]** Integrate Whisper.cpp into `TranscriptionService` for on-device transcription.
    - `[ ]` **[M]** Implement automatic fallback from local to cloud STT based on performance.
    - `[ ]` **[M]** Build a comprehensive analytics dashboard for users.
- **Should Have:**
    - `[ ]` **[S]** Implement weekly summary emails.
    - `[ ]` **[S]** Add in-app prompts to encourage users to upgrade.
    - `[ ]` **[S]** Conduct thorough cross-browser testing and bug fixing.
- **Could Have:**
    - `[ ]` **[C]** A/B test different UI elements and user flows.
    - `[ ]` **[C]** Optimize funnels based on PostHog data.

### PHASE 3 — SCALE & EXPANSION (Months 6-12) - 0% Complete
- **Must Have:**
    - `[ ]` **[M]** Implement team accounts and billing.
- **Should Have:**
    - `[ ]` **[S]** Add support for more languages.
    - `[ ]` **[S]** Develop AI-powered suggestions for improving speech.
- **Could Have:**
    - `[ ]` **[C]** Full offline mode for the application.
    - `[ ]` **[C]** Case studies and advanced content marketing.

---

## 📊 Financial Projections

### Assumptions
- **Free → Paid Conversion:** 5%
- **Stripe Fee:** 3% of revenue
- **Ad Spend:** $350/month average
- **Tool + Infrastructure Costs:** $141/month baseline

### Monthly Growth Projection
```
┌─────┬──────┬───────┬─────────┬───────────┬──────────┬────────────┬──────────────┬────────────┬──────────┐
│ Mth │ MAU  │ Paid  │ Revenue │ Infra     │ Ad Spend │ Stripe     │ Total Costs  │ Net Profit │ Profit % │
│     │      │ Users │         │ Costs     │          │ Fees       │              │            │          │
├─────┼──────┼───────┼─────────┼───────────┼──────────┼────────────┼──────────────┼────────────┼──────────┤
│  1  │ 250  │  13   │ $103.87 │   $141    │   $350   │   $3.12    │   $494.12    │  -$390.25  │  -375%   │
│  3  │1,200 │  60   │ $479.40 │   $141    │   $350   │   $14.38   │   $505.38    │   -$25.98  │   -5%    │
│  6  │3,000 │ 150   │$1,198.50│   $161    │   $350   │   $35.96   │   $546.96    │   $651.54  │   54%    │
└─────┴──────┴───────┴─────────┴───────────┴──────────┴────────────┴──────────────┴────────────┴──────────┘
```

### Key Business Metrics

**📈 LTV (Lifetime Value)**
```
Formula: ARPU × Average Customer Lifespan
Calculation: $7.99/month × 12 months = $95.88
```

**💸 CAC (Customer Acquisition Cost)**
```
Formula: Total Marketing Spend ÷ New Paying Customers
Calculation: $350 ÷ 35 customers = $10.00
```

**🎯 LTV:CAC Ratio**
```
Current Ratio: $95.88 ÷ $10.00 = 9.5:1
Target: 3:1+ (Highly favorable ✅)
```

---

## 🚀 Go-to-Market Assets

### Reddit Engagement Strategy

**Post #1 - Educational Approach**
- **Target:** r/PublicSpeaking, r/PresentationSkills
- **Title:** "How I cut my filler words in half before my big presentation"
- **Strategy:** Share personal story with soft product mention

**Post #2 - Beta Recruitment**
- **Target:** r/Toastmasters, r/CareerSuccess  
- **Title:** "Beta testers wanted: Real-time filler word counter for speech practice"
- **Strategy:** Direct community engagement for early adopters

### SEO Content Strategy

**Pillar Article: "How to Stop Saying 'Um'"**
- **Target Keyword:** "how to stop saying um"
- **Length:** ~2,500 words
- **Structure:** Psychology → Techniques → Tools → Action steps

---

## ✅ Success Criteria

```
🎯 Achieve 500 MAUs within 3 months post-launch
💰 Reach 5% free-to-paid conversion rate
📱 Maintain <40% mobile bounce rate  
💵 Achieve profitability within 12 months
```

---

*Ready to help users speak with confidence while keeping their privacy protected.* 🎤🔒

---