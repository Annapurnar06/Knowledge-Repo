# Agentic AI Architecture for Lending

## Overview

This document defines the technical architecture for AI agents that power the lending platform. The design is grounded in the CS153 reinforcement learning framing from the ULI analysis: lending is a verifiable environment where agent actions produce measurable outcomes (KYC pass/fail, EMI received/bounced, loan cured/rolled). This makes lending ideal for RL-style improvement loops.

---

## 1. Core Thesis: Lending as a Verifiable Environment

### Why Lending Works for Agentic AI

Most real-world environments are only partially verifiable -- you can't always tell if an agent's action was "correct." Lending is different:

| Agent Action | Outcome Signal | Verifiability | Feedback Latency |
|---|---|---|---|
| KYC verification | Pass/fail (identity confirmed or not) | **Fully verifiable** | Seconds |
| Data extraction | Accuracy (GST turnover matches bank credits or not) | **Fully verifiable** | Minutes |
| Rules/pricing check | Compliant or non-compliant | **Fully verifiable** | Seconds |
| Underwriting decision | Loan repaid or defaulted | **Verifiable but lagged** | 30-360 days (EMI) |
| Dynamic repayment adjustment | Borrower repaid adjusted amount or not | **Verifiable, fast** | 1-7 days (EDI/dynamic) |
| Collections intervention | Cure rate, roll rate | **Verifiable, lagged** | 30-90 days |
| Compliance check | Regulatory audit pass/fail | **Fully verifiable** | Periodic |

**Key insight:** Dynamic repayment (EDI, cooperative-cycle) compresses the feedback loop from 30-360 days to 1-7 days. This transforms underwriting from a slow-feedback to a fast-feedback learning environment.

### The RL Advantage

Traditional ML in lending: train model on historical data, deploy, retrain periodically.

RL-style agents in lending: observe state (borrower data + ULI signals), take action (approve/adjust/intervene), receive reward (repayment outcome), update policy -- continuously.

```
                    ┌─────────────┐
                    │   State     │
                    │ (ULI data + │
                    │  borrower   │
                    │  context)   │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   Agent     │
                    │  (policy)   │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │   Action    │
                    │ (approve,   │
                    │  adjust,    │
                    │  intervene) │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  Reward     │
                    │ (repaid,    │
                    │  defaulted, │
                    │  cured)     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │  Update     │
                    │  Policy     │
                    └─────────────┘
```

---

## 2. Six Agent Architecture

### Agent Orchestration

```
                        ┌──────────────────────┐
                        │  Agent Orchestrator   │
                        │                       │
                        │  - Request routing    │
                        │  - State management   │
                        │  - Agent coordination │
                        │  - Audit logging      │
                        └───────────┬───────────┘
                                    │
          ┌─────────────┬───────────┼───────────┬─────────────┐
          │             │           │           │             │
    ┌─────▼─────┐ ┌─────▼─────┐ ┌──▼──────┐ ┌─▼────────┐ ┌──▼─────────┐
    │ Agent 1   │ │ Agent 2   │ │ Agent 3 │ │ Agent 4  │ │ Agent 5    │
    │ KYC/      │ │ Under-    │ │ KFS/Doc │ │ Disburse │ │ Collections│
    │ Onboard   │ │ writing   │ │ QA      │ │ & Service│ │ Strategy   │
    └───────────┘ └───────────┘ └─────────┘ └──────────┘ └────────────┘
                                                                │
                                                          ┌─────▼──────┐
                                                          │ Agent 6    │
                                                          │ Compliance │
                                                          │ Monitor    │
                                                          └────────────┘
```

### Agent 1: KYC/Onboarding Evidence Builder

**Purpose:** Verify borrower identity and build the evidence file for underwriting.

**Input state:**
- Borrower voice/text input (name, Aadhaar, cooperative ID, etc.)
- Aadhaar photo (WhatsApp image)
- Segment type (dairy/vendor/SHG/gig/kirana)

**Actions:**
1. Initiate ULI identity verification (Aadhaar, PAN, CKYC)
2. Request segment-specific verification (cooperative membership, SHG records, platform ID)
3. Initiate AA consent flow
4. Cross-check identity signals across sources
5. Flag inconsistencies for manual review

**Output:**
- Verified identity record (JSON)
- Evidence file with all ULI responses
- Consistency score (0-100)
- Red flags (if any)

**Verifiability:** Fully verifiable. Every identity check is binary pass/fail.

**Reward function:**
```
reward_kyc = (
    identity_verification_accuracy * 0.4 +
    fraud_detection_rate * 0.3 +
    onboarding_completion_rate * 0.2 +
    time_to_complete * 0.1  # faster = better, within accuracy bounds
)
```

**Learning loop:** Track which identity inconsistencies actually predict fraud (lagged signal from loan outcomes). Over time, agent learns which red flags matter and which are false positives.

---

### Agent 2: Underwriting Workbench Copilot

**Purpose:** Synthesize all ULI data into a credit decision and dynamic product structure.

**Input state:**
- Evidence file from KYC Agent
- AA bank statement (6-12 months)
- Segment-specific data (milk pouring, QR inflows, GST returns, platform payouts)
- Credit bureau data
- Environmental data (satellite, weather)
- Historical portfolio performance for similar borrowers

**Actions:**
1. Calculate composite credit score (segment-specific formula, see [uli-integration-spec.md](./uli-integration-spec.md))
2. Determine eligible loan amount
3. Design repayment structure based on cash flow pattern
4. Set seasonal adjustments (flush/lean, festival, monsoon)
5. Propose terms to NBFC partner for approval

**Output:**
- Credit memo (human-readable summary)
- Loan amount, rate, tenure recommendation
- Dynamic repayment schedule (per-period amounts)
- Confidence score and key risk factors
- NBFC-ready approval package

**Verifiability:** Partially verifiable with fast feedback under dynamic repayment.
- Traditional EMI: outcome known in 30-360 days
- Dynamic repayment: outcome known in 1-7 days per payment
- Cross-source data accuracy: verifiable immediately

**Reward function:**
```
reward_underwriting = (
    repayment_rate_of_approved_loans * 0.35 +
    approval_rate * 0.15 +  # don't be too conservative
    revenue_generated * 0.20 +
    npa_rate_penalty * -0.25 +  # penalize defaults heavily
    borrower_satisfaction * 0.05
)
```

**Learning loop:**
- Short-term (daily/weekly): EDI/dynamic repayment signals -- is the borrower making payments?
- Medium-term (monthly): Is the borrower's cash flow matching the predicted pattern?
- Long-term (quarterly): Portfolio NPA rate, comparison to benchmark

**Key innovation: Continuous underwriting.**
Traditional underwriting is a point-in-time decision. With dynamic repayment + ULI monitoring, underwriting becomes continuous:
- Week 1: initial decision based on available data
- Week 4: adjust limit based on actual repayment behavior
- Week 12: re-underwrite with 3 months of behavioral data
- Week 24: graduate to larger loan with high confidence

---

### Agent 3: KFS/Documentation QA Agent

**Purpose:** Generate and verify Key Fact Statement and loan documentation against RBI requirements.

**Input state:**
- Loan terms from Underwriting Copilot
- Borrower language preference
- RBI KFS template requirements
- NBFC-specific documentation requirements

**Actions:**
1. Generate KFS in borrower's language
2. Verify all mandatory fields present (APR, total cost, charges, grievance info)
3. Generate voice script for KFS reading (voice-first borrowers)
4. Record borrower consent (voice or digital)
5. Verify consent quality (was the explanation clear? did borrower acknowledge?)

**Output:**
- KFS document (PDF + voice script)
- Consent artifact (voice recording or digital signature)
- Compliance checklist (all items pass/fail)

**Verifiability:** Fully verifiable. Every KFS field is either present or not. Every disclosure is either compliant or not.

**Reward function:**
```
reward_kfs = (
    compliance_score * 0.5 +       # all mandatory fields present
    borrower_comprehension * 0.3 + # voice analysis: did borrower understand?
    processing_speed * 0.1 +
    zero_regulatory_flags * 0.1    # no issues found in audit
)
```

---

### Agent 4: Disbursement & Servicing Guardrail Agent

**Purpose:** Execute disbursement after all checks pass, then monitor loan servicing for anomalies.

**Input state:**
- Approved loan package from Underwriting Copilot
- KFS consent from Documentation Agent
- NBFC approval confirmation
- Borrower bank account details
- Post-disbursement: daily ULI monitoring signals

**Actions (Pre-disbursement):**
1. Final checklist verification (KYC complete, underwriting approved, KFS consented, NBFC approved)
2. Verify disbursement account matches Aadhaar-linked bank account
3. Trigger UPI/NEFT disbursement
4. Confirm receipt in borrower's account
5. Send confirmation to borrower (WhatsApp/SMS)

**Actions (Post-disbursement -- ongoing):**
1. Monitor daily repayment signals (EDI deductions, cooperative deductions)
2. Track cash flow signals from ULI (QR inflows, bank balance, cooperative payments)
3. Detect anomalies (sudden drop in activity, unusual patterns)
4. Trigger dynamic adjustments (seasonal flex, moratorium, acceleration)
5. Escalate to Collections Agent if early warning triggers

**Verifiability:** Fully verifiable for disbursement (money sent or not). Fast feedback for servicing (daily signals).

**Reward function:**
```
reward_servicing = (
    successful_disbursement_rate * 0.2 +
    early_warning_detection_accuracy * 0.3 +  # flagged issues that were real
    false_alarm_penalty * -0.1 +               # flagged issues that weren't
    dynamic_adjustment_effectiveness * 0.3 +   # adjustments that prevented default
    borrower_satisfaction_post_adjustment * 0.1
)
```

---

### Agent 5: Collections Strategy & Execution Agent

**Purpose:** Determine and execute optimal intervention for borrowers showing distress signals.

**Input state:**
- Early warning from Servicing Agent
- Borrower's full interaction history (Mem0 graph)
- Current cash flow signals from ULI
- Segment-specific context (market-wide vs individual distress)
- Historical intervention outcomes for similar borrowers

**Actions (graduated intervention):**

| Level | Trigger | Action | Channel |
|---|---|---|---|
| 0 - Monitor | 1-2 missed daily deductions | No action; within normal variance | -- |
| 1 - Nudge | 3-5 missed deductions | Gentle WhatsApp reminder | Automated |
| 2 - Check-in | 5-7 missed deductions | Voice call asking if borrower needs help | AI voice |
| 3 - Restructure | 7-14 missed deductions + income drop confirmed | Offer restructuring (lower amount, extended tenure) | AI voice + WhatsApp |
| 4 - Moratorium | External event (weather, health, market closure) | Automatic moratorium, no borrower action needed | WhatsApp notification |
| 5 - Escalate | 30+ days overdue + no response | Refer to NBFC collections team | Dashboard alert |

**Key innovation: Context-aware collections.**
- If milk pouring drops across entire cooperative (not just this borrower): market-wide issue → automatic moratorium
- If only this borrower's pouring drops: individual issue → personal check-in
- If weather event in region: preemptive moratorium before any payments are missed

**Verifiability:** Verifiable with 30-90 day lag. Cure rate (did the borrower resume payments?) and roll rate (did the loan worsen?) are measurable.

**Reward function:**
```
reward_collections = (
    cure_rate * 0.35 +                    # borrowers who resumed payment
    cost_per_recovery * -0.20 +           # lower cost = better
    borrower_retention_rate * 0.20 +      # borrower stays on platform after resolution
    time_to_cure * -0.15 +               # faster resolution = better
    regulatory_compliance * 0.10          # no harassment, proper process
)
```

**Learning loop:** Every intervention has a measurable outcome. Over 12 months with 10,000+ loans, the agent learns:
- Which borrowers respond to nudges vs need restructuring
- When moratoriums help vs enable dependency
- Which segments cure fastest with which intervention type
- Optimal timing for escalation

---

### Agent 6: Compliance and Fairness Monitor

**Purpose:** Continuous inspection of all other agents' actions for regulatory compliance and fairness.

**Input state:**
- All agent actions and decisions (audit log)
- RBI regulatory requirements (rules database, updated quarterly)
- NBFC-specific compliance requirements
- Fair lending principles (no discrimination by caste, gender, religion, geography)

**Actions:**
1. Real-time rule checking: every loan decision checked against RBI guidelines
2. KFS compliance: verify every loan has compliant KFS before disbursement
3. Interest rate cap monitoring: ensure all-in cost doesn't exceed NBFC's stated rate
4. Fair lending audit: statistical analysis of approval/rejection rates by demographic
5. Data privacy compliance: verify consent exists for every ULI data access
6. Grievance resolution tracking: ensure borrower complaints are addressed within SLA

**Output:**
- Real-time compliance score (per loan, per agent, portfolio-level)
- Regulatory risk flags (any rule violation = immediate alert)
- Fairness dashboard (approval rates by segment, gender, geography)
- Audit-ready report (for RBI inspection or NBFC audit)

**Verifiability:** Fully verifiable. Every compliance check is binary pass/fail against defined rules.

**Reward function:**
```
reward_compliance = (
    zero_violations * 0.5 +              # any violation = heavy penalty
    audit_readiness_score * 0.2 +        # all documentation in order
    fairness_score * 0.2 +               # no demographic bias detected
    grievance_resolution_time * 0.1      # faster resolution = better
)
```

---

## 3. Mem0 Graph Memory Architecture

### Why Graph Memory for Lending

Traditional stateless APIs forget the borrower between interactions. For a voice-first platform serving repeat borrowers over years, persistent memory is essential.

**Mem0** provides a graph-based memory layer where:
- Every borrower interaction is stored as a node
- Relationships connect interactions to outcomes
- Context from prior interactions informs future ones

### Memory Graph Structure

```
                    ┌──────────────┐
                    │   Borrower   │
                    │   (Ramesh)   │
                    └──────┬───────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
   ┌─────▼─────┐    ┌─────▼─────┐    ┌─────▼─────┐
   │ Identity   │    │ Financial │    │ Behavioral│
   │ Context    │    │ Context   │    │ Context   │
   │            │    │           │    │           │
   │ - Aadhaar  │    │ - Income  │    │ - Voice   │
   │ - Coop ID  │    │   pattern │    │   prefs   │
   │ - Family   │    │ - Seasonal│    │ - Language │
   │   size     │    │   cycle   │    │ - Trust   │
   │ - Location │    │ - Savings │    │   level   │
   └─────┬──────┘    │ - Debts   │    │ - Response│
         │           └─────┬─────┘    │   pattern │
         │                 │          └─────┬─────┘
         │                 │                │
   ┌─────▼─────────────────▼────────────────▼─────┐
   │              Interaction History               │
   │                                                │
   │  Loan 1 (25K) ──> Repaid ──> Loan 2 (75K)    │
   │       │                           │            │
   │  Voice call 1   Weather event   Voice call 5  │
   │  Voice call 2   Moratorium      Voice call 6  │
   │  Voice call 3   Resume payment                │
   └────────────────────────────────────────────────┘
```

### Memory Use Cases

| Scenario | Without Mem0 | With Mem0 |
|---|---|---|
| Ramesh calls for 2nd loan | "Tell me your Aadhaar number" | "Ramesh bhai, last time you took 25K for cattle feed. You want another loan?" |
| Lakshmi's QR inflows drop | Generic alert to collections | "Lakshmi had a similar drop last monsoon. She recovered in 2 weeks. Hold intervention." |
| Meena applies for Level 3 | Full underwriting from scratch | "Meena graduated Level 2 in 8 months with perfect repayment. Fast-track approval." |
| Arjun switches from Swiggy to Zomato | Income verification fails | "Arjun started multi-platform. Combined income is higher. Adjust underwriting." |
| Suresh's GST drops | Default risk alert | "Suresh had a similar GST dip during last Navratri store renovation. Check context." |

### Memory Categories

| Category | What's Stored | Retention | Access |
|---|---|---|---|
| Identity facts | Name, Aadhaar, family, location | Permanent | KYC Agent, Underwriting |
| Financial patterns | Income seasonality, spending habits, savings rate | Loan lifetime + 2 years | Underwriting, Collections |
| Interaction preferences | Language, voice vs text, call time preference | Permanent | All agents |
| Loan history | Applications, approvals, repayment patterns | Permanent | All agents |
| Life events | Marriage, child birth, herd loss, crop failure | 5 years | Underwriting, Collections |
| Behavioral signals | Response patterns, trust indicators | 2 years | Collections, Servicing |

---

## 4. Sovereign Context Strategy

### What is Sovereign Context?

Sovereign context = data that only exists because your system created the interaction that produced it. No other company can access it because it didn't exist before your system ran.

### Sources of Sovereign Context

| Context Type | How It's Generated | Why It's Unique | Competitive Value |
|---|---|---|---|
| Voice interaction data in 12+ Indian languages | Borrowers speaking to AI in Gujarati, Kannada, Marathi about credit needs | No foundation model is trained on dairy farmer credit conversations in Gujarati | Vernacular voice models improve only with this data |
| Cash flow to repayment outcome under dynamic schedules | EDI/cooperative-linked repayments produce daily outcome signals | Nobody else deploys dynamic products; nobody else has this outcome data | Underwriting models that predict repayment under flexible schedules |
| Cross-lender fraud ring graphs | Multi-lender visibility reveals coordinated borrowing patterns | Only the network operator sees cross-lender patterns | Fraud detection that individual lenders cannot replicate |
| Climate-to-default correlations | Satellite + weather data mapped to actual loan outcomes in specific geographies | Combination of public climate data + private lending outcome data is unique | Climate credit models that predict default risk from environmental signals |
| SHG graduation pathway data | Individual-level data on which SHG women successfully graduate from group to individual credit | Nobody tracks individual trajectories within SHGs at scale | Models that predict graduation success from early signals |
| Gig worker income stability patterns | Platform payout + multi-platform income patterns mapped to loan outcomes | Multi-platform view doesn't exist for any single platform | Gig credit scoring that works across platforms |

### Context Accumulation Timeline

| Time | Context Volume | Capability Unlocked |
|---|---|---|
| 0-6 months | 10K borrower interactions | Basic vernacular voice model; initial cash flow patterns |
| 6-12 months | 50K interactions + 10K loan outcomes | First RL-trained underwriting adjustments; seasonal pattern models |
| 12-24 months | 500K interactions + 100K outcomes | Segment-specific models that outperform generic scoring by 20-30% |
| 24-36 months | 5M interactions + 1M outcomes | Multi-segment models; cross-lender fraud detection; climate correlations |
| 36+ months | 50M+ interactions + 10M+ outcomes | Models that cannot be replicated without spending the same 36 months |

### Competitive Moat Depth

```
Year 0                    Year 1                    Year 3
  │                         │                         │
  │  Generic ML models      │  Segment-specific       │  Unreplicable
  │  (anyone can build)     │  models (6-12 month     │  sovereign models
  │                         │  data advantage)         │  (36+ months of
  │                         │                         │  unique context)
  │                         │                         │
  ▼─────────────────────────▼─────────────────────────▼
  Shallow moat              Moderate moat             Deep moat
```

---

## 5. Technical Infrastructure

### Agent Runtime

| Component | Technology Choice | Rationale |
|---|---|---|
| Agent framework | LangGraph / custom orchestrator | Multi-step workflows with branching logic |
| LLM backbone | GPT-4o / Claude for reasoning; fine-tuned local models for classification | Cost optimization: expensive model for complex decisions, cheap model for routine |
| Voice AI | Bhashini API + custom ASR | Bhashini for Indian language support; custom model trained on lending conversations |
| Memory store | Mem0 (graph) + PostgreSQL (relational) | Graph for relationships; relational for transactional data |
| Vector store | Qdrant / Pinecone | Semantic search over borrower interactions and similar case retrieval |
| ULI client | Custom async client (Python) | Handles consent, caching, parallel requests |
| Message bus | Redis Streams / Kafka | Agent-to-agent communication; event-driven monitoring |
| Observability | OpenTelemetry + custom dashboards | Trace every agent decision for audit |

### Infrastructure Costs (Estimated)

| Phase | Monthly Infra Cost | Key Drivers |
|---|---|---|
| Phase 1 (10K loans) | INR 2-4L | LLM API calls, voice AI, ULI data fees |
| Phase 2 (500K loans) | INR 15-30L | Scaled voice AI, monitoring pipeline, multi-agent orchestration |
| Phase 3 (5M loans) | INR 1-2 Cr | Self-hosted models, real-time monitoring, satellite data pipeline |

### Cost per Loan

| Cost Component | Phase 1 | Phase 3 (at scale) |
|---|---|---|
| ULI data fees | INR 50-75 | INR 30-50 (volume discounts) |
| Voice AI (onboarding) | INR 15-25 | INR 5-10 (self-hosted model) |
| LLM inference (underwriting) | INR 10-20 | INR 3-5 (fine-tuned local model) |
| Monitoring (monthly) | INR 5-10 | INR 2-3 |
| **Total per loan** | **INR 80-130** | **INR 40-68** |

---

## 6. Feedback Loop Architecture

### Short-Term Loops (Daily-Weekly)

```
EDI/Dynamic Payment
       │
       ▼
  Payment received?  ──Yes──> Positive signal → reinforce policy
       │
       No
       │
       ▼
  Context check:
  - Market-wide issue? → Moratorium (no policy penalty)
  - Individual issue? → Intervention → track cure
```

### Medium-Term Loops (Monthly-Quarterly)

```
Portfolio Performance Review
       │
       ▼
  Segment-level NPA analysis
       │
       ▼
  Compare: our NPA vs benchmark
       │
       ├── Lower → Current policy working → expand
       │
       └── Higher → Root cause analysis
                    │
                    ├── Underwriting too loose → tighten scoring weights
                    ├── Collections too slow → earlier intervention
                    └── External shock → adjust environmental risk factors
```

### Long-Term Loops (Annual)

```
Model Retraining Cycle
       │
       ▼
  Full outcome dataset (all loans, all segments)
       │
       ▼
  Retrain segment-specific scoring models
       │
       ▼
  A/B test: new model vs current model on 10% of new applications
       │
       ▼
  If new model outperforms → promote to production
  If not → analyze why → iterate
```

---

## 7. Deployment Sequence

### Phase 1 (Months 0-12): Three Agents

| Agent | Deployment | Capability |
|---|---|---|
| KYC/Onboarding | Month 1 | Aadhaar + PAN + cooperative verification |
| Underwriting Copilot | Month 2 | Dairy-specific scoring + cooperative-cycle product |
| Compliance Monitor | Month 3 | KFS verification + basic rule checking |

**Human-in-loop:** All underwriting decisions reviewed by credit analyst for first 3 months. Agent learns from overrides.

### Phase 2 (Months 12-24): Six Agents

| Agent | Deployment | Capability |
|---|---|---|
| KFS/Documentation QA | Month 13 | Automated KFS generation + consent recording |
| Disbursement Guardrail | Month 14 | Automated disbursement + post-disbursement monitoring |
| Collections Strategy | Month 16 | Graduated intervention based on 12 months of outcome data |

**Reduced human-in-loop:** Only edge cases (top/bottom 10% of risk scores) reviewed by humans.

### Phase 3 (Months 24-36): Full Autonomy

- All six agents operational with RL-trained policies
- Human oversight shifts from individual decisions to portfolio-level monitoring
- New agent capabilities: cross-segment learning, fraud ring detection, climate credit adjustment

---

## Cross-References

- Borrower journeys powered by these agents: [borrower-journeys.md](./borrower-journeys.md)
- ULI data consumed by each agent: [uli-integration-spec.md](./uli-integration-spec.md)
- Market validation for agent capabilities: [fbr-market-data.md](./fbr-market-data.md)
- Phased deployment aligned to roadmap: [roadmap.md](./roadmap.md)
