# ULI Integration Specification

## Overview

This document maps the Unified Lending Interface (ULI) data ecosystem to each borrower segment, defines API integration architecture, consent flows, cross-source verification logic, and the agent design patterns that consume ULI data. ULI provides 136 data services across 89 lenders -- this spec defines how an AI-native lending platform consumes that ecosystem.

---

## 1. ULI Architecture Overview

### What ULI Provides

ULI is India's consent-gated, standardized data exchange layer for lending. It aggregates data from multiple government and private sources into a unified API surface.

```
┌──────────────────────────────────────────────────────────┐
│                    ULI Data Layer                         │
├──────────────┬──────────────┬──────────────┬─────────────┤
│   Identity   │    Income    │    Assets    │   History   │
│              │              │              │             │
│ Aadhaar eKYC │ AA Bank Stmt │ Land Records │ Credit      │
│ PAN Verify   │ GST Returns  │ Satellite    │  Bureau     │
│ CKYC         │ UPI/QR Data  │ Vehicle Reg  │ KCC Records │
│ Voter ID     │ EPFO/Salary  │ Crop Insur.  │ SHG Records │
│ Driving Lic. │ ITR          │ Gold (est.)  │ Trade Credit│
│              │ Coop Payments│ Inventory    │ Utility Pay │
└──────────────┴──────────────┴──────────────┴─────────────┘
         │              │              │              │
         ▼              ▼              ▼              ▼
┌──────────────────────────────────────────────────────────┐
│              AI Agent Consumption Layer                   │
│                                                          │
│  KYC Agent → Underwriting Copilot → KFS Agent →         │
│  Disbursement Agent → Collections Agent → Compliance     │
└──────────────────────────────────────────────────────────┘
```

### ULI Scale

| Metric | Value |
|---|---|
| Total data services | 136 |
| Connected lenders | 89 |
| Identity sources | 5+ (Aadhaar, PAN, CKYC, Voter ID, DL) |
| Financial data sources | AA (300+ FIPs), GST, ITR, EPFO |
| Asset data sources | Land records (digitizing), satellite, vehicle registry |
| Consent mechanism | Aadhaar-based e-consent with purpose limitation |
| Response format | Standardized JSON |
| Latency | Real-time for identity; async (minutes) for AA/GST |

---

## 2. Data Mapping by Borrower Segment

### 2.1 Dairy Farmer

| ULI Source | API Endpoint | Data Extracted | Underwriting Use | Verification Signal |
|---|---|---|---|---|
| Aadhaar e-KYC | `/identity/aadhaar/verify` | Name, DOB, address, photo | Identity verification | Binary pass/fail |
| PAN verification | `/identity/pan/verify` | PAN status, name match | Tax identity linkage | Name match score |
| Cooperative membership | `/livelihood/cooperative/verify` | Member ID, join date, village | Livelihood verification | Address vs cooperative village |
| Milk pouring records | `/agri/dairy/pouring` | Daily liters, fat %, SNF, payment amount | Income estimation | Pouring consistency score |
| Cooperative payments | `/agri/dairy/payments` | Fortnightly amounts, 12-month history | Cash flow pattern | Payment vs bank credit match |
| AA bank statement | `/financial/aa/statement` | 6-month transaction history | Balance pattern, obligations | Credits match cooperative payments |
| KCC records | `/agri/kcc/status` | Sanctioned amount, utilization, repayment | Existing credit behavior | On-time vs overdue status |
| Satellite imagery | `/agri/satellite/ndvi` | NDVI (vegetation index) around homestead | Fodder/crop health | Green cover vs declared farming |
| Crop insurance | `/agri/pmfby/status` | Coverage, claims history | Risk mitigation evidence | Active policy = lower risk |
| Weather data | `/environment/weather/forecast` | 7-day forecast, seasonal outlook | Moratorium trigger | Extreme event → auto-adjust |

**Composite Score Calculation:**

```
dairy_score = (
    milk_consistency_score * 0.30 +    # 12-month pouring regularity
    cooperative_payment_score * 0.25 +  # On-time payment history
    bank_balance_health * 0.15 +        # Average balance, no bounces
    kcc_repayment_score * 0.15 +        # Existing credit behavior
    satellite_health * 0.05 +           # Supplementary verification
    cooperative_tenure * 0.10           # Membership longevity
)
```

### 2.2 Street Vendor / Micro-Merchant

| ULI Source | API Endpoint | Data Extracted | Underwriting Use | Verification Signal |
|---|---|---|---|---|
| Aadhaar e-KYC | `/identity/aadhaar/verify` | Identity | KYC | Binary |
| PAN verification | `/identity/pan/verify` | Tax identity | Linkage | Match score |
| UPI/QR transactions | `/financial/upi/merchant` | Daily QR collections, transaction count, customer frequency | Primary income signal | Inflow consistency |
| AA bank statement | `/financial/aa/statement` | Account history | Balance pattern | QR inflows vs bank credits |
| PM SVANidhi status | `/government/svanidhi/status` | Registration, loan history | Existing micro-credit | Repayment behavior |
| Trade license | `/business/license/verify` | Municipal registration | Business legitimacy | Active status |
| Utility payments | `/financial/utility/history` | Electricity, phone bill payments | Behavioral scoring | On-time payment pattern |

**Composite Score Calculation:**

```
vendor_score = (
    qr_inflow_consistency * 0.35 +     # Daily QR collection regularity
    qr_volume_trend * 0.20 +           # Growing vs declining business
    bank_balance_health * 0.15 +        # Minimum balance maintenance
    svanidhi_repayment * 0.10 +         # Prior micro-credit behavior
    transaction_diversity * 0.10 +      # Number of unique customers
    utility_payment_score * 0.10        # Behavioral discipline
)
```

### 2.3 SHG Woman

| ULI Source | API Endpoint | Data Extracted | Underwriting Use | Verification Signal |
|---|---|---|---|---|
| Aadhaar e-KYC | `/identity/aadhaar/verify` | Identity | KYC | Binary |
| SHG membership | `/social/shg/membership` | SHG ID, group size, join date, NRLM link | Group verification | Active membership status |
| SHG savings record | `/social/shg/savings` | Monthly savings, regularity | Financial discipline | Consistency over 12+ months |
| SHG loan repayment | `/social/shg/loans` | Group loan history, individual share, repayment | Credit behavior | On-time rate |
| SHG bank account (AA) | `/financial/aa/statement` | Group account transactions | Contribution pattern | Individual deposits match records |
| MGNREGA payments | `/government/mgnrega/payments` | Wage payment history | Income supplementation | Payment regularity |
| Ration card status | `/government/pds/status` | Category (BPL/APL), family size | Vulnerability assessment | Household context |

**Composite Score Calculation:**

```
shg_score = (
    group_repayment_history * 0.30 +   # Group loan repayment rate
    savings_regularity * 0.25 +         # Monthly savings consistency
    shg_tenure * 0.15 +                 # Months of active membership
    attendance_rate * 0.10 +            # Meeting participation
    peer_assessment * 0.10 +            # Group leader evaluation
    individual_income_evidence * 0.10   # UPI/wage payment signals
)
```

### 2.4 Gig Worker

| ULI Source | API Endpoint | Data Extracted | Underwriting Use | Verification Signal |
|---|---|---|---|---|
| Aadhaar e-KYC | `/identity/aadhaar/verify` | Identity | KYC | Binary |
| PAN verification | `/identity/pan/verify` | Tax identity | Linkage | Match score |
| Platform payout data | `/employment/gig/payouts` | Weekly payouts, delivery count, ratings, incentive hit rate | Primary income signal | Payout consistency |
| AA bank statement | `/financial/aa/statement` | Account history | Balance pattern | Platform payouts vs bank credits |
| UPI transaction history | `/financial/upi/personal` | P2P + merchant transactions | Spending pattern | Income vs expenditure |
| Vehicle registration | `/asset/vehicle/verify` | Bike ownership, insurance status | Asset verification | Active insurance = responsible |
| EPFO (if applicable) | `/employment/epfo/passbook` | Prior formal employment | Employment history | Supplementary signal |

**Composite Score Calculation:**

```
gig_score = (
    payout_consistency * 0.30 +        # Weekly payout regularity
    platform_tenure * 0.20 +           # Months on platform
    platform_rating * 0.15 +           # Customer/platform trust signal
    bank_balance_health * 0.15 +       # Post-payout balance maintenance
    multi_platform_income * 0.10 +     # Diversified income sources
    vehicle_insurance_status * 0.10    # Asset responsibility signal
)
```

### 2.5 Kirana Store / Micro-MSME

| ULI Source | API Endpoint | Data Extracted | Underwriting Use | Verification Signal |
|---|---|---|---|---|
| Aadhaar e-KYC | `/identity/aadhaar/verify` | Identity | KYC | Binary |
| PAN verification | `/identity/pan/verify` | Tax identity | Linkage | Match score |
| GSTIN verification | `/business/gst/profile` | GST registration, business type, filing status | Business legitimacy | Active filing status |
| GST returns | `/business/gst/returns` | Monthly turnover, purchase-sale ratio, tax compliance | Revenue verification | Turnover vs bank credits |
| AA bank statement | `/financial/aa/statement` | Multi-account history | Cash flow pattern | GST turnover vs bank flows |
| UPI/QR merchant data | `/financial/upi/merchant` | Daily QR collections | Real-time revenue | QR inflows vs GST declared |
| Credit bureau | `/credit/bureau/report` | Existing loans, CC, repayment history | Existing obligations | Total exposure check |
| Trade credit | `/business/trade/credit` | Distributor credit terms, payment history | Supply chain health | On-time supplier payments |
| Shop/business license | `/business/license/verify` | Municipal registration | Business continuity | Active status |
| Utility payments | `/financial/utility/history` | Commercial electricity, phone | Behavioral + business activity | Consistent usage = active business |

**Composite Score Calculation:**

```
kirana_score = (
    gst_filing_regularity * 0.20 +     # Monthly GST filing consistency
    revenue_trend * 0.20 +             # 12-month turnover trajectory
    qr_to_gst_match * 0.15 +           # Cross-source revenue verification
    bank_cash_flow_health * 0.15 +     # Balance, no bounces, credit pattern
    existing_credit_behavior * 0.15 +  # Bureau score + CC utilization
    trade_credit_payment * 0.10 +      # Supplier payment discipline
    utility_regularity * 0.05          # Business activity proxy
)
```

---

## 3. Cross-Source Verification Matrix

The core innovation of ULI-enabled AI lending: no single data source is trusted alone. Every claim is cross-verified.

| Claim | Source 1 | Source 2 | Source 3 | Verification Logic |
|---|---|---|---|---|
| "I earn INR 4L/month" | GST return (INR 4.2L) | Bank credits (INR 4.5L) | QR collections (INR 2.8L) | Match within 15%: PASS. QR < total = cash component exists |
| "I own 2 acres" | Land records (2.1 acres) | Satellite (cultivated area ~2 acres) | -- | Satellite confirms declared area: PASS |
| "I pour 20L milk/day" | Cooperative records (avg 19.5L) | Bank credits match coop payment | -- | Within 5%: PASS |
| "I have no other loans" | Bureau: 0 active loans | AA: no EMI debits visible | -- | Both clean: PASS |
| "My business is 12 years old" | GST reg date: 2019 | Shop license: 2014 | Bank account opened: 2013 | Consistent long history: PASS |
| "I work 25 deliveries/day" | Platform data: avg 24.3 | Bank: weekly payouts match | -- | Platform + bank consistent: PASS |

### Red Flag Detection

| Red Flag | Detection Logic | Action |
|---|---|---|
| GST turnover >> bank credits | Revenue inflation or cash hoarding | Request additional verification; reduce eligible amount |
| Cooperative pouring suddenly spikes before application | Possible data manipulation | Check historical 12-month trend; flag if >30% deviation |
| Multiple loan applications in 30 days | Bureau inquiry count high | Stacking risk; reduce amount or decline |
| Address mismatch across sources | Aadhaar village != cooperative village != bank branch | Possible identity issue; manual verification required |
| Platform rating suddenly drops | Performance issue on gig platform | Wait for stabilization before disbursement |
| GST filing gaps | Business may be struggling | Recent gaps = cautious; old gaps (12+ months ago) = less concern |

---

## 4. Consent Architecture

### Consent Flow

```
Borrower          AI Agent          ULI Gateway       Data Sources
   │                  │                  │                  │
   │  Voice request   │                  │                  │
   │─────────────────>│                  │                  │
   │                  │                  │                  │
   │  "I need to      │                  │                  │
   │   check your     │                  │                  │
   │   records"       │                  │                  │
   │<─────────────────│                  │                  │
   │                  │                  │                  │
   │  Voice consent   │                  │                  │
   │  "Haa, thik hai" │                  │                  │
   │─────────────────>│                  │                  │
   │                  │ Consent request  │                  │
   │                  │ (purpose-bound)  │                  │
   │                  │─────────────────>│                  │
   │                  │                  │  Data requests   │
   │                  │                  │─────────────────>│
   │                  │                  │  Data responses  │
   │                  │                  │<─────────────────│
   │                  │ Aggregated data  │                  │
   │                  │<─────────────────│                  │
   │                  │                  │                  │
   │  "Based on your  │                  │                  │
   │   records, you   │                  │                  │
   │   qualify for    │                  │                  │
   │   INR 30K"       │                  │                  │
   │<─────────────────│                  │                  │
```

### Consent Parameters

| Parameter | Value | Rationale |
|---|---|---|
| Purpose | "Credit assessment for working capital loan" | RBI requires purpose limitation |
| Data scope | Specified per segment (see Section 2) | Minimum necessary data principle |
| Consent duration | 12 months (renewable) | Covers loan lifecycle + monitoring |
| Revocation | Anytime via voice command or WhatsApp | Borrower control |
| Storage | Encrypted at rest, TTL-bound | Data minimization |
| Sharing | Only with lending partner (named NBFC) | No third-party sharing without separate consent |

### Voice Consent Protocol

For borrowers using IVR/voice (dairy farmers, SHG women):

1. Agent reads consent terms in borrower's language
2. Agent asks: "Do you agree to share your [specific data] with [NBFC name] for a loan application?"
3. Borrower responds verbally ("haa" / "yes" / equivalent)
4. Voice recording stored as consent artifact (encrypted, timestamped)
5. WhatsApp/SMS confirmation sent with consent summary in text

---

## 5. API Integration Architecture

### System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      Platform Layer                          │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────────┐  │
│  │ Voice AI │  │ WhatsApp │  │ NBFC     │  │ Dashboard  │  │
│  │ Gateway  │  │ Bot      │  │ Portal   │  │ (Internal) │  │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └─────┬──────┘  │
│       │              │              │              │          │
│  ┌────▼──────────────▼──────────────▼──────────────▼──────┐  │
│  │              Agent Orchestrator                         │  │
│  │  (Routes requests to appropriate agents)                │  │
│  └────┬──────────────┬──────────────┬─────────────────────┘  │
│       │              │              │                         │
│  ┌────▼─────┐  ┌─────▼────┐  ┌─────▼────────┐               │
│  │ KYC      │  │ Under-   │  │ Collections  │  ... agents   │
│  │ Agent    │  │ writing  │  │ Agent        │               │
│  │          │  │ Copilot  │  │              │               │
│  └────┬─────┘  └────┬─────┘  └──────┬───────┘               │
│       │              │               │                       │
│  ┌────▼──────────────▼───────────────▼────────────────────┐  │
│  │              ULI Gateway Client                         │  │
│  │  (Consent management, request routing, caching)         │  │
│  └────┬───────────────────────────────────────────────────┘  │
│       │                                                      │
└───────┼──────────────────────────────────────────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────────────────┐
│                     ULI Gateway (External)                     │
│                                                               │
│  Identity APIs │ Financial APIs │ Asset APIs │ Government APIs │
└───────────────────────────────────────────────────────────────┘
```

### Key Integration Components

#### ULI Gateway Client

**Responsibilities:**
- Consent token management (creation, renewal, revocation)
- Request routing to appropriate ULI endpoints
- Response caching (with TTL based on data freshness requirements)
- Rate limiting and retry logic
- Audit logging for compliance

**Design principles:**
- All ULI calls are asynchronous (AA data can take minutes)
- Parallel requests where possible (identity + financial data simultaneously)
- Graceful degradation (if one source unavailable, continue with others)
- Response normalization (all sources output unified JSON schema)

#### Data Freshness Requirements

| Data Type | Freshness | Cache TTL | Rationale |
|---|---|---|---|
| Identity (Aadhaar, PAN) | Static | 30 days | Rarely changes |
| Bank statement (AA) | Near real-time | 1 day | Cash flow changes daily |
| GST returns | Monthly | 7 days | Filed monthly |
| Milk pouring data | Daily | 6 hours | Pouring happens twice daily |
| UPI/QR transactions | Real-time | No cache | Used for EDI deductions |
| Satellite imagery | Weekly | 3 days | Updated every few days |
| Credit bureau | On-demand | 7 days | Updated on inquiry |
| Weather data | Hourly | 1 hour | Used for moratorium triggers |

---

## 6. Agent-Specific ULI Consumption Patterns

### KYC/Onboarding Agent

**ULI calls per borrower onboarding:**

| Step | ULI API | Expected Latency | Failure Handling |
|---|---|---|---|
| 1. Aadhaar verify | `/identity/aadhaar/verify` | <2 seconds | Retry 2x, then manual check |
| 2. PAN verify | `/identity/pan/verify` | <2 seconds | Retry 2x, flag for manual |
| 3. PAN-Aadhaar linkage | `/identity/pan-aadhaar/link` | <2 seconds | If unlinked, guide borrower |
| 4. CKYC check | `/identity/ckyc/search` | <3 seconds | Optional; enhance if available |
| 5. AA consent initiate | `/financial/aa/consent` | <5 seconds | SMS/app-based consent flow |
| 6. Segment-specific verify | Various (see Section 2) | 5-30 seconds | Parallel execution |

**Total onboarding ULI cost per borrower:** ~INR 15-25 (varies by segment)

### Underwriting Copilot

**ULI calls per underwriting decision:**

| Data Need | ULI API(s) | Processing | Output |
|---|---|---|---|
| Income assessment | AA statement + GST + QR + cooperative | Cross-source reconciliation | Monthly income estimate with confidence score |
| Obligation mapping | Bureau + AA debits | EMI/payment pattern detection | Total monthly obligations |
| Asset verification | Land records + satellite + vehicle | Ownership confirmation | Asset-backed capacity |
| Behavioral scoring | Utility + trade credit + SHG records | Payment discipline assessment | Behavioral score (0-100) |
| Risk signals | Weather + crop insurance + market data | Environmental risk overlay | Risk adjustment factor |

**Total underwriting ULI cost per application:** ~INR 30-50

### Collections Strategy Agent

**Ongoing ULI monitoring per active loan:**

| Signal | ULI Source | Frequency | Trigger Threshold |
|---|---|---|---|
| QR inflow decline | UPI merchant data | Daily | >30% decline for 5 days |
| Bank balance drop | AA statement | Weekly | Below INR 500 for 3+ days |
| GST filing miss | GST returns | Monthly | 1 missed filing |
| Milk pouring drop | Cooperative data | Daily | >30% decline for 5 days |
| Platform activity pause | Gig platform data | Weekly | 0 deliveries for 7+ days |
| Weather event | Weather API | Hourly | Extreme heat/flood/drought alert |
| Satellite crop stress | Satellite NDVI | Weekly | NDVI drop >20% in borrower's area |

**Monthly monitoring ULI cost per active loan:** ~INR 5-10

---

## 7. Data Privacy and Security

### Principles

1. **Minimum necessary data:** Only request ULI sources relevant to the specific borrower segment
2. **Purpose limitation:** Each consent token specifies exact purpose (credit assessment, ongoing monitoring, etc.)
3. **Storage minimization:** Raw ULI responses are processed and discarded; only derived features are stored
4. **Encryption:** All data encrypted at rest (AES-256) and in transit (TLS 1.3)
5. **Access control:** Agent-level permissions -- KYC agent cannot access collections data and vice versa
6. **Audit trail:** Every ULI API call logged with timestamp, purpose, agent ID, and consent token

### Data Retention Policy

| Data Category | Retention Period | After Retention |
|---|---|---|
| Raw ULI responses | 7 days | Deleted; only derived features retained |
| Derived credit features | Loan lifetime + 7 years | Required for regulatory compliance |
| Voice consent recordings | Loan lifetime + 7 years | Regulatory requirement |
| Monitoring signals | 90 days rolling | Aggregated into trend metrics |
| Agent decision logs | Loan lifetime + 7 years | Audit trail for AI decisions |

---

## 8. Implementation Phases

### Phase 1 (Months 0-6): Core Integration

| Component | Priority | Effort |
|---|---|---|
| Aadhaar + PAN verification | P0 | 2 weeks |
| AA bank statement integration | P0 | 4 weeks |
| Cooperative data API (Amul ecosystem) | P0 | 6 weeks (partnership dependent) |
| Consent management system | P0 | 3 weeks |
| ULI Gateway Client (basic) | P0 | 4 weeks |
| Response normalization layer | P0 | 2 weeks |

### Phase 2 (Months 6-12): Extended Sources

| Component | Priority | Effort |
|---|---|---|
| GST integration | P1 | 3 weeks |
| UPI/QR merchant data | P1 | 4 weeks |
| Credit bureau integration | P1 | 3 weeks |
| Satellite imagery pipeline | P1 | 4 weeks |
| Weather data integration | P1 | 2 weeks |
| Cross-source verification engine | P1 | 6 weeks |

### Phase 3 (Months 12-18): Full Ecosystem

| Component | Priority | Effort |
|---|---|---|
| SHG/NRLM database integration | P2 | 4 weeks |
| Gig platform data APIs | P2 | 4 weeks (per platform) |
| Trade credit platform integration | P2 | 3 weeks |
| Real-time monitoring pipeline | P2 | 6 weeks |
| RL feedback loop from ULI signals | P2 | 8 weeks |

---

## Cross-References

- How this integration serves borrower journeys: [borrower-journeys.md](./borrower-journeys.md)
- Roadmap phases that depend on this integration: [roadmap.md](./roadmap.md)
- AI agents that consume this data: [agentic-architecture.md](./agentic-architecture.md)
- Market data driving integration priorities: [fbr-market-data.md](./fbr-market-data.md)
