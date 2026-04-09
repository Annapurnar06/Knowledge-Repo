# Borrower Journeys: Priority Segments

## Overview

This document details end-to-end borrower journeys for the five priority segments identified in the [roadmap](./roadmap.md). Each journey maps: discovery, onboarding, underwriting, disbursement, servicing, and graduation -- with the specific ULI data sources, AI agent interactions, and servicing models that apply.

---

## Journey 1: Ramesh -- Dairy Farmer (Phase 1 Priority)

### Profile

| Attribute | Value |
|---|---|
| Name archetype | Ramesh Patel |
| Location | Anand, Gujarat |
| Age | 38 |
| Herd | 4 buffaloes (2 milking) |
| Daily milk output | ~20 liters |
| Cooperative | Member of Amul-affiliated village dairy cooperative |
| Current credit access | KCC (INR 50K, rarely renewed); moneylender at 36% for emergencies |
| Income pattern | Fortnightly cooperative payments; flush season (Oct-Feb) 40% higher than lean (Apr-Jun) |
| Language | Gujarati (some Hindi) |
| Phone | Android smartphone, primarily WhatsApp |
| Credit need | INR 25K for cattle feed during lean season; INR 1.5L for new buffalo purchase |

### Journey

```
Discovery ──> Onboarding ──> Underwriting ──> Disbursement ──> Servicing ──> Graduation
  (Day 0)      (Day 0-1)     (Day 1-2)       (Day 2-3)      (Ongoing)    (Cycle 3+)
```

#### Stage 1: Discovery (Day 0)

**Trigger:** Ramesh's cooperative secretary mentions the new "phone se loan" option at the fortnightly milk collection payment.

**Channel:** WhatsApp message from cooperative (trusted sender) with a missed call number.

**AI interaction:** Ramesh gives a missed call. The system calls back in Gujarati via IVR:
> "Namaste Ramesh bhai, hu tame dairy loan mate madad kari shaku. Tamari cooperative membership number kaho."

**What happens technically:**
- Voice AI initiates in Gujarati dialect appropriate for Anand region
- Cooperative membership ID captured via voice
- KYC Agent pre-fetches Aadhaar data via ULI if consent token exists from cooperative enrollment

#### Stage 2: Onboarding (Day 0-1)

**Borrower experience:** 8-minute voice conversation + WhatsApp photo of Aadhaar.

**AI Agent: KYC/Onboarding Evidence Builder**
- Captures Aadhaar number via voice (or WhatsApp image OCR)
- Initiates ULI consent flow: Aadhaar verification + PAN linkage + cooperative membership verification
- Cross-checks: Aadhaar address vs cooperative village records
- Pulls AA bank statement (last 6 months) with borrower's voice consent

**ULI data sources activated:**
1. Aadhaar e-KYC (identity verification)
2. PAN verification (tax identity linkage)
3. Cooperative membership registry (livelihood verification)
4. AA bank statement (6 months financial history)
5. KCC records check (existing agriculture credit)

**Verification logic:**
- Aadhaar village address matches cooperative village → +ve signal
- KCC exists and was serviced → strong +ve signal
- Bank account shows fortnightly credits matching cooperative payment schedule → strong +ve signal
- No moneylender pattern in bank debits → neutral (absence of data, not absence of borrowing)

#### Stage 3: Underwriting (Day 1-2)

**AI Agent: Underwriting Workbench Copilot**

**Data synthesis:**
| Source | Signal | Weight |
|---|---|---|
| Milk pouring records (via cooperative API) | 20 liters/day average, consistent 11 of 12 months | High |
| Cooperative payment history | 24 fortnightly payments received on time, avg INR 8,500 | High |
| AA bank statement | Average balance INR 12K, no bounced payments | Medium |
| KCC history | INR 50K sanctioned, 90% utilized, repaid on time | Medium |
| Satellite (if available) | Green fodder visible around homestead | Low (supplementary) |

**Credit decision:**
- **Eligible amount:** INR 30K (working capital) at 16% p.a.
- **Product structure:** Cooperative-Cycle-Linked Repayment
  - 24 fortnightly instalments
  - Flush season (Oct-Feb): INR 1,800/fortnight
  - Normal (Mar, Jul-Sep): INR 1,200/fortnight
  - Lean season (Apr-Jun): INR 700/fortnight
- **Total repayment:** INR 32,400 over 12 months

**AI Agent: KFS/Documentation QA Agent**
- Generates Key Fact Statement in Gujarati
- Voice reads KFS terms to Ramesh (regulatory requirement: borrower must understand terms)
- Captures voice consent ("haa, mane samjay chhe" = "yes, I understand")

#### Stage 4: Disbursement (Day 2-3)

**AI Agent: Disbursement Guardrail Agent**
- Verifies: KFS consent recorded, all ULI checks passed, NBFC partner approval received
- Triggers UPI disbursement to Ramesh's bank account linked to Aadhaar
- Sends WhatsApp confirmation in Gujarati with loan summary

**Borrower experience:** Money in bank account within 48 hours of first call. WhatsApp message confirms amount, repayment schedule, and next payment date.

#### Stage 5: Servicing (Ongoing -- 12 months)

**Servicing Model: Cooperative-Cycle-Linked Repayment**

**How it works:**
- Every fortnight, cooperative pays Ramesh for milk. Payment amount is known to the system (cooperative API).
- System calculates repayment amount based on season:
  - If flush season: deduct INR 1,800 from cooperative payment before crediting Ramesh
  - If lean season: deduct only INR 700
- Ramesh receives net amount. Deduction is transparent -- shown on WhatsApp receipt.

**AI Agent: Collections Strategy Agent**
- Monitors milk pouring data daily. If Ramesh's pouring drops >30% for 5+ days:
  - Trigger: potential herd health issue or family emergency
  - Action: proactive WhatsApp message in Gujarati asking if everything is okay
  - If herd issue confirmed: offer 2-week moratorium + connect to veterinary helpline
  - If family emergency: restructure next 3 payments to lower amount
- Monitors weather data. If extreme heat event in Anand:
  - Preemptive adjustment: reduce next 2 fortnightly payments by 20%
  - Rationale: heat stress reduces milk output across region

**AI Agent: Compliance Monitor**
- Tracks that all deductions match the agreed schedule
- Ensures total interest charged matches KFS disclosure
- Flags any deviation from RBI guidelines on loan servicing

#### Stage 6: Graduation (After successful cycle)

**After 1 successful loan (12 months):**
- Ramesh's "Dairy Credit Score" improves
- Eligible for INR 75K (animal purchase or infrastructure)
- Same cooperative-cycle-linked repayment but 18-month tenure

**After 2 successful loans (24 months):**
- Eligible for INR 1.5L (new buffalo purchase)
- Can access insurance product (cattle insurance bundled)
- Invited to "Credit Health" subscription for ongoing monitoring

**After 3 successful loans (36 months):**
- Individual unsecured eligibility up to INR 2L
- Can become "credit champion" -- referral incentive for bringing other cooperative members

---

## Journey 2: Lakshmi -- Street Vendor / Micro-Merchant (Phase 1-2)

### Profile

| Attribute | Value |
|---|---|
| Name archetype | Lakshmi Devi |
| Location | Surat, Gujarat |
| Age | 32 |
| Business | Vegetable cart near textile market |
| Daily revenue | INR 2,000-4,000 (varies by day/season) |
| Current credit access | Moneylender at 5% per month (60% annualized) |
| Income pattern | Daily, with weekly cycle (Saturday peak, Monday low) |
| Language | Hindi |
| Phone | Basic Android, uses WhatsApp and Paytm for QR payments |
| Credit need | INR 15K for inventory bulk purchase; INR 5K emergency buffer |

### Journey

#### Stage 1: Discovery

**Trigger:** Paytm/QR payment notification includes a "Get working capital" banner. Or: field agent at wholesale mandi distributes flyers with missed call number.

**Channel:** Missed call → callback in Hindi

#### Stage 2: Onboarding (Same day)

**AI Agent: KYC/Onboarding Evidence Builder**
- Captures Aadhaar via voice
- ULI consent: Aadhaar + AA bank statement + UPI transaction history
- Key innovation: **UPI Financial Identity Building**
  - Even with no credit history, 6 months of UPI QR collections create a verifiable income profile
  - System analyzes: daily inflow pattern, peak days, average transaction size, customer frequency

**ULI data sources:**
1. Aadhaar e-KYC
2. AA bank statement (if available; many vendors bank irregularly)
3. UPI/QR transaction history (primary income signal)
4. PM SVANidhi registration (if applicable -- street vendor welfare scheme)

#### Stage 3: Underwriting

**AI Agent: Underwriting Workbench Copilot**

**Key innovation -- Cash Flow Object underwriting:**

| Signal | What It Shows | Scoring Impact |
|---|---|---|
| UPI QR collections: avg 15 transactions/day | Active business, regular customers | Strong positive |
| Daily inflow: INR 2,000-4,000 | Sufficient for micro-loan servicing | Positive |
| Weekly pattern: Sat peak, Mon trough | Predictable cycle for repayment scheduling | Positive |
| Festival months (Oct-Nov): 50% higher | Seasonal upside for acceleration | Positive |
| Bank balance: rarely >INR 5K | No savings buffer -- loan provides resilience | Neutral (not negative) |

**Credit decision:**
- **Eligible amount:** INR 15K at 18% p.a.
- **Product structure:** EDI (Dynamic)
  - Daily deduction from QR inflows
  - Amount: 8-12% of daily inflow (not fixed amount)
  - If daily inflow <INR 500: no deduction (automatic moratorium)
  - If daily inflow >INR 5,000: accelerated payment
- **Expected repayment period:** 90-120 days

#### Stage 4: Disbursement

Same-day UPI disbursement. Lakshmi sees INR 15,000 in her account within hours.

#### Stage 5: Servicing -- EDI Dynamic Model

**Daily flow:**
1. Lakshmi collects payments via QR throughout the day
2. At end of day (9 PM settlement), system calculates: total inflow x 10% = repayment
3. If inflow was INR 3,000: deduction = INR 300
4. If inflow was INR 800: deduction = INR 80
5. If inflow was INR 400 (below threshold): no deduction, carry forward

**AI Agent: Collections Strategy Agent**
- Tracks daily inflow trend. If 5 consecutive days below INR 1,000:
  - Check: is this Lakshmi-specific or market-wide? (compare with other vendors in area)
  - If market-wide (rain, festival closure): auto-extend tenure, no borrower action needed
  - If Lakshmi-specific: WhatsApp message checking if she needs support
- Tracks cumulative repayment. If ahead of schedule: offer top-up loan or reduced rate on next loan

#### Stage 6: Graduation

- After 1st EDI loan: eligible for INR 25K
- After 3rd loan: eligible for INR 50K + can access savings product
- After 12 months of continuous QR activity: "vendor credit score" established -- can approach formal bank loans

---

## Journey 3: Meena -- SHG Woman (Phase 2-3)

### Profile

| Attribute | Value |
|---|---|
| Name archetype | Meena Kumari |
| Location | Raichur, Karnataka |
| Age | 28 |
| SHG | Member of 12-woman SHG for 3 years |
| Current credit access | SHG group loan (INR 2L shared among 12 women = INR ~17K each) |
| Income | Seasonal agricultural labor + tailoring (INR 3-8K/month) |
| Language | Kannada |
| Phone | Feature phone (husband has smartphone) |
| Credit need | INR 25K for sewing machine + fabric to start tailoring business |

### Journey

#### Stage 1: Discovery

**Trigger:** SHG group meeting. NGO facilitator introduces the "individual loan" option for SHG members with good group repayment history.

**Channel:** IVR call in Kannada (feature phone compatible)

#### Stage 2: Onboarding

**AI Agent: KYC/Onboarding Evidence Builder**
- Aadhaar via voice (feature phone IVR -- no WhatsApp needed)
- SHG membership verification via NRLM/state SHG database
- Group repayment history: 36 months of on-time group loan repayment
- AA bank statement from SHG bank account (shows Meena's contribution pattern)

**Key innovation -- SHG Credit Score:**
| Component | Data Source | What It Shows |
|---|---|---|
| Group attendance | SHG register (digitized) | Commitment signal |
| Savings regularity | SHG bank account via AA | Discipline indicator |
| Group loan repayment | SHG repayment records | Creditworthiness |
| Peer assessment | Group leader input (voice) | Social collateral |
| Individual income evidence | Tailoring orders via UPI (if any) | Capacity signal |

#### Stage 3: Underwriting

**AI Agent: Underwriting Workbench Copilot**

**Graduation Staircase logic:**
- Meena has 36 months SHG history → qualifies for Level 2 (individual micro-loan)
- Level 1: Group loan only (INR 2-5K share) -- already completed
- **Level 2: Individual micro-loan (INR 10-25K)** ← Meena is here
- Level 3: Individual unsecured (INR 25K-2L)
- Level 4: MSME loan (INR 1-5L)

**Credit decision:**
- **Eligible amount:** INR 25K at 14% p.a. (lower rate due to SHG track record)
- **Product structure:** Monthly EMI with seasonal flex
  - Harvest months (Oct-Dec, Mar-Apr): INR 3,000/month
  - Off-season: INR 1,500/month
- **Tenure:** 12 months

#### Stage 4: Disbursement

UPI to Meena's individual bank account (not SHG account). WhatsApp message to husband's phone with loan summary in Kannada.

#### Stage 5: Servicing

**AI Agent: Collections Strategy Agent**
- Monthly check via IVR: "Meena, nimma tailoring kelasa hege nadeyuttide?" (How is your tailoring work going?)
- Tracks UPI inflows to Meena's account (tailoring payments)
- If income from tailoring growing: proactive offer to accelerate repayment and qualify for Level 3 faster
- If income stalled: connect to SHG NGO for business mentoring

**Community reinforcement:** Monthly SMS to SHG group leader with Meena's repayment status (with Meena's consent). Social accountability without punitive pressure.

#### Stage 6: Graduation

- **After Level 2 success:** Eligible for INR 75K (Level 3) to expand tailoring unit
- **After Level 3:** INR 2L for hiring 2 workers, buying industrial machine
- **After Level 4:** Full MSME loan eligibility -- Meena is now a formal micro-entrepreneur

**Data generated for system learning:** Every SHG woman's graduation path produces outcome data that trains the model on which factors predict successful graduation from group-guarantee to individual credit.

---

## Journey 4: Arjun -- Gig Worker (Phase 2-3)

### Profile

| Attribute | Value |
|---|---|
| Name archetype | Arjun Kumar |
| Location | Pune, Maharashtra |
| Age | 24 |
| Platform | Swiggy delivery partner (8 months) |
| Daily earnings | INR 500-1,200 (varies with incentives and hours) |
| Current credit access | Zero formal credit; family borrows from relatives |
| Income pattern | Daily payouts; weekends and rain days = peak earnings |
| Language | Hindi + Marathi |
| Phone | Android smartphone |
| Credit need | INR 10K for bike repair; INR 20K for second-hand bike upgrade |

### Journey

#### Stage 1: Discovery

**Trigger:** In-app notification from Swiggy partner app: "Get instant bike repair loan." Or: banner at Swiggy hub during weekly payout.

**Channel:** WhatsApp link → voice AI conversation

#### Stage 2: Onboarding

**AI Agent: KYC/Onboarding Evidence Builder**
- Aadhaar + PAN via WhatsApp photo
- Swiggy partner ID verification (platform API)
- ULI consent: AA bank statement + UPI transaction history
- Platform earnings data (via Swiggy partner API with consent)

**Key data source: Platform Payout History**
| Signal | What System Sees |
|---|---|
| 8 months on Swiggy | Stable platform engagement |
| Avg 25 deliveries/day | High activity level |
| Weekly payout: INR 5,000-8,000 | Consistent income |
| Rating: 4.6/5 | Platform trust signal |
| Incentive hit rate: 70% | Reliable performance |

#### Stage 3: Underwriting

**AI Agent: Underwriting Workbench Copilot**

**Payout-Linked Product Design:**
- **Eligible amount:** INR 15K at 20% p.a.
- **Product structure:** Payout-Linked Repayment
  - Auto-deduct 12% of each Swiggy payout
  - High-incentive weeks (>INR 8K): deduction = INR 960
  - Low weeks (<INR 4K): deduction = INR 480
  - Zero-activity weeks: no deduction (automatic pause)
- **Expected repayment period:** 16-20 weeks

#### Stage 4: Disbursement

Instant UPI disbursement. Arjun sees INR 15,000 within 2 hours.

#### Stage 5: Servicing

**Payout-Linked Flow:**
1. Swiggy processes weekly payout to Arjun
2. Before payout hits Arjun's account, 12% is routed to loan repayment
3. Arjun receives net payout + WhatsApp receipt showing: gross payout, loan deduction, net received, remaining balance

**AI Agent: Collections Strategy Agent**
- If Arjun switches platforms (starts doing Zomato too): system detects new UPI inflows, adjusts total income picture
- If 2+ weeks of zero activity: WhatsApp check-in. If bike broke down: offer emergency top-up loan for repair
- If Arjun's delivery volume consistently growing: offer upgrade to INR 30K bike loan

#### Stage 6: Graduation

- After 1st payout-linked loan: eligible for INR 30K (bike upgrade)
- After 2 successful loans: eligible for INR 50K (can start multi-platform delivery)
- After 12 months of platform history: "Gig Credit Score" established for formal bank access

---

## Journey 5: Suresh -- Kirana Store Owner (Phase 1-2)

### Profile

| Attribute | Value |
|---|---|
| Name archetype | Suresh Sharma |
| Location | Nashik, Maharashtra |
| Age | 45 |
| Business | Kirana store (general provisions), 12 years old |
| Monthly revenue | INR 3-5L |
| Current credit access | CC limit INR 2L (80% utilized); distributor credit (30-day) |
| Income pattern | Daily cash + UPI; month-end spike (salary-day shopping) |
| Language | Marathi + Hindi |
| Phone | Android smartphone, uses Paytm Business + PhonePe |
| Credit need | INR 1.5L for Diwali inventory stocking; INR 50K for store renovation |
| GST status | Registered (turnover INR 40-60L/year) |

### Journey

#### Stage 1: Discovery

**Trigger:** Suresh's distributor (Hindustan Unilever / P&G) offers "early payment" financing option at point of invoice.

**Channel:** WhatsApp Business message from distributor with loan link

#### Stage 2: Onboarding

**AI Agent: KYC/Onboarding Evidence Builder**
- GST number → auto-fetch business profile
- Aadhaar + PAN
- AA consent: bank statement (2 accounts -- business + personal)
- UPI/QR transaction history (Paytm Business dashboard API)

**ULI data sources:**
1. GST returns (last 12 months of filing)
2. AA bank statements (both accounts, 12 months)
3. UPI/QR merchant transactions
4. Credit bureau (existing CC and any loans)
5. Distributor credit history (if available via trade credit platform)

#### Stage 3: Underwriting

**AI Agent: Underwriting Workbench Copilot**

**Cross-source verification (ULI-enabled):**
| Check | Data 1 | Data 2 | Result |
|---|---|---|---|
| Revenue consistency | GST monthly filing: INR 4.2L avg | Bank credits: INR 4.5L avg | Match (bank slightly higher = some cash) |
| UPI share of revenue | QR collections: INR 2.8L/month | Bank QR credits: INR 2.8L | Exact match |
| Existing obligations | Bureau: CC INR 1.6L outstanding | Bank debits: CC payment INR 15K/month | Consistent |
| Distributor dependence | GST purchase invoices | Bank debits to distributors | Top 3 distributors = 70% of purchases |
| Seasonal pattern | GST: Oct-Nov 40% higher | Bank: confirmed Oct-Nov spike | Festival inventory cycle confirmed |

**Credit decision:**
- **Eligible amount:** INR 1.5L at 16% p.a.
- **Product structure:** EDI Dynamic
  - Daily deduction from QR inflows: 5% of daily QR collections
  - Festival months (Oct-Nov): auto-acceleration at 8% (higher inflows = faster repayment)
  - If daily QR inflow <INR 2,000: reduced deduction at 3%
- **Expected repayment period:** 120-150 days

#### Stage 4: Disbursement

Disbursement directly to distributor account (inventory financing) or to Suresh's business account.

#### Stage 5: Servicing

**EDI Dynamic for Kirana:**
1. Daily QR settlement processes at 9 PM
2. System calculates: today's QR inflow x 5% = repayment
3. Typical day: INR 9,000 QR inflow → INR 450 deducted
4. Diwali week: INR 25,000 QR inflow → INR 2,000 deducted (accelerated at 8%)
5. Monsoon slow day: INR 3,000 QR inflow → INR 90 deducted (reduced at 3%)

**AI Agent: Collections Strategy Agent**
- Monitors GST filing patterns. If Suresh misses a monthly GST filing: early warning flag
- Compares QR inflow trend with nearby kirana stores (anonymous). If Suresh declining while area stable: potential business issue
- Tracks distributor payment patterns. If Suresh starts delaying distributor payments: distress signal

**AI Agent: Disbursement & Servicing Guardrail**
- Ensures daily deduction never exceeds 12% of inflow (regulatory and fairness guardrail)
- Auto-adjusts if Suresh's daily cash flow pattern shifts structurally

#### Stage 6: Graduation

- After 1st EDI loan: eligible for INR 3L (larger inventory cycle)
- After 2 successful cycles: eligible for INR 5L term loan for store renovation/expansion
- After 12 months of GST + QR data: "Kirana Credit Score" unlocks formal bank MSME products

---

## Cross-Segment Comparison

| Dimension | Dairy Farmer | Street Vendor | SHG Woman | Gig Worker | Kirana Store |
|---|---|---|---|---|---|
| **Phase** | 1 | 1-2 | 2-3 | 2-3 | 1-2 |
| **First loan amount** | INR 25-30K | INR 15K | INR 25K | INR 15K | INR 1.5L |
| **Repayment model** | Cooperative-cycle-linked | EDI Dynamic | Monthly flex | Payout-linked | EDI Dynamic |
| **Primary data source** | Milk pouring + cooperative | UPI/QR inflows | SHG history | Platform payouts | GST + QR inflows |
| **Verification signal** | Cooperative records vs bank | QR inflows vs bank | SHG register vs bank | Platform data vs bank | GST vs bank vs QR |
| **Channel** | IVR + WhatsApp (voice) | WhatsApp (voice) | IVR (feature phone) | WhatsApp (text + voice) | WhatsApp Business |
| **Language** | Gujarati | Hindi | Kannada | Hindi/Marathi | Marathi/Hindi |
| **Graduation path** | 25K → 75K → 1.5L → 3L | 15K → 25K → 50K | 25K → 75K → 2L → 5L | 15K → 30K → 50K | 1.5L → 3L → 5L |
| **Time to formal credit** | 24-36 months | 12-18 months | 36-48 months | 12-18 months | 12-18 months |
| **Risk mitigant** | Cooperative deduction + weather monitoring | Daily granular deduction | Social collateral + graduation | Platform deduction | Daily granular + GST monitoring |

---

## Common Design Patterns Across All Journeys

### 1. Voice-First, Document-Last
Every journey starts with a voice interaction, not a form. Documents (Aadhaar photo, GST printout) are captured via WhatsApp image, not uploaded through a web portal.

### 2. Cash Flow as Primary Signal
No journey relies on collateral or traditional credit score as the primary underwriting signal. Cash flow (milk pouring, QR inflows, platform payouts, SHG savings) is always the primary signal.

### 3. Dynamic Repayment
No journey uses fixed EMI. Every repayment schedule adapts to the borrower's income cycle -- cooperative fortnightly, daily QR, weekly payout, or seasonal agricultural.

### 4. Proactive Servicing
Collections agents don't wait for default. They monitor leading indicators (milk pouring drop, QR inflow decline, platform activity pause, GST filing miss) and intervene before the borrower is in distress.

### 5. Graduation Built In
Every journey has a clear path from first micro-loan to formal credit eligibility. The system produces verifiable outcome data at each stage that trains the next underwriting model.

### 6. Trust Through Transparency
Every deduction is visible (WhatsApp receipt). Every term is explained in the borrower's language (voice KFS reading). Every adjustment is proactive (weather moratorium, seasonal flex).

---

## Cross-References

- Phased execution plan: [roadmap.md](./roadmap.md)
- ULI data sources for each journey: [uli-integration-spec.md](./uli-integration-spec.md)
- AI agents powering each journey: [agentic-architecture.md](./agentic-architecture.md)
- Market data validating each segment: [fbr-market-data.md](./fbr-market-data.md)
