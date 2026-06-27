# A Standardized Data Format for AI Ingestion in Healthcare

### Solving health-data fragmentation for the AI era — an India-first design & market thesis

> **Document type:** Combined product design doc · market-opportunity report · investor brief · technical spec proposal
> **Primary market:** India (healthcare-first), with a cross-industry expansion path
> **Status:** v0.1 — research draft
> **Last updated:** June 2026

---

## 1. Executive Summary

Healthcare is generating more machine-readable data than ever, yet **AI systems still cannot
reliably consume it**. The data exists — but it is fragmented across hospitals, labs, pharmacies,
and government schemes, and the formats that *do* standardize it (HL7 v2, C‑CDA, FHIR) were
designed for **system-to-system exchange**, not for **ingestion by large language models**. The
result: verbose, deeply nested, reference-heavy payloads that are token-inefficient, semantically
indirect, and hostile to retrieval-augmented generation (RAG).

**The thesis of this document:**

1. **Data fragmentation is the #1 bottleneck for healthcare AI** — not model quality. "AI success
   in 2026 will be defined less by model sophistication and more by data readiness."
   ([ZLTI, 2026](https://www.zlti.com/blog/in-2026-ai-success-will-be-decided-by-unstructured-data-management))
2. **Existing standards are exchange-native, not AI-native.** FHIR retrieval can produce up to
   **391× more tokens** than a query-first approach
   ([FHIRPath-QA, 2026](https://arxiv.org/pdf/2602.23479)); HL7 itself began publishing
   experimental Markdown "AI bundles" in 2025 precisely because FHIR's structure is suboptimal for
   LLMs ([HL7 AI Office, 2025](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/)).
3. **India is the ideal beachhead.** The Ayushman Bharat Digital Mission (ABDM) has created the
   world's largest digital-health substrate — **~90 crore ABHA health IDs and 100 crore+ linked
   records by mid‑2026** ([Organiser, May 2026](https://organiser.org/2026/05/31/355992/bharat/ayushman-bharat-crosses-90-cr-abha-accounts-how-modi-govt-is-building-the-worlds-largest-digital-health-ecosystem/)) —
   on an open, FHIR‑R4‑mandated, consent-based, India-Stack-style architecture. It is greenfield,
   mandated, and adoption-incentivized in a way the fragmented US/EU markets are not.
4. **The opportunity is a new layer, not a new EHR:** an **AI-native canonical data format**
   ("HealthLM" in this doc) plus a mapping/serving layer that turns FHIR/HL7/C‑CDA/scanned records
   into LLM-ready, RAG-chunkable, consent-aware, multilingual context — exposed to agents via the
   Model Context Protocol (MCP).

The whitespace is real: **no production-grade, AI-native, source-agnostic ingestion format exists
today.** This document lays out the problem, sizes the market (India-first), explains why now,
analyzes existing solutions, and proposes a concrete format with before/after examples.

---

## 2. The Problem: Data Fragmentation

### 2.1 The global shape of the problem

- **80%+ of enterprise data is unstructured** (notes, audio, scans, sensor streams), and
  **43% of organizations cite data quality/readiness as the top obstacle** to AI — with ~80% of AI
  projects failing, roughly twice the rate of traditional IT
  ([CDO Magazine / Komprise, 2026](https://www.zlti.com/blog/in-2026-ai-success-will-be-decided-by-unstructured-data-management)).
- In US healthcare specifically, fragmentation drives an estimated **~$200B/year in duplicate
  services** ([Chief Healthcare Executive, 2025](https://www.chiefhealthcareexecutive.com/view/consequences-of-fragmented-healthcare-information-systems))
  and ~$30B/year recoverable through device interoperability alone
  ([blueBriX, 2025](https://bluebrix.health/blogs/connecting-the-dots-eliminating-fragmented-systems-workflows-for-coordinated-care)).
- Fragmentation is economy-wide: data silos are cited as a **~$3.1 trillion lost-value** problem
  across industries ([LeadGen Economy, 2025](https://www.leadgen-economy.com/blog/enterprise-data-fragmentation-mcp-rag-solution/)).

This is the cross-industry tailwind. But the sharpest, most addressable version of the problem —
and the right beachhead — is **healthcare in India**.

### 2.2 The Indian problem (primary focus)

India's health data is fragmented along **four axes simultaneously**:

| Axis | What fragments | Evidence |
|---|---|---|
| **Provider silos** | Hospitals, labs, pharmacies, govt schemes each hold partial records | Only ~17,000 of ~130,000 ABDM-enabled facilities are private despite 44% of facilities being private ([Zealthix, 2025](https://zealthix.com/abdm-adoption-in-2025-opportunities-and-roadblocks-for-indian-healthcare-providers/)) |
| **Paper ↔ digital** | EHR adoption concentrated in urban private chains; tier‑2/3 & rural largely paper | India EHR market still only ~USD 726M in 2025 ([IMARC](https://www.imarcgroup.com/india-electronic-health-records-market)) |
| **Standards / interoperability** | Inconsistent coding, non-uniform implementations | ~28% of EHR initiatives stalled by interoperability gaps and ~28% by privacy/security concerns ([IMARC](https://www.imarcgroup.com/india-electronic-health-records-market)) |
| **Language** | 22 scheduled languages across prescriptions, consent, diagnoses | BHASHINI 22-language AI now integrated into ABDM/eSanjeevani ([AICerts, 2025](https://www.aicerts.ai/news/bhashini-boosts-health-platform-accessibility-with-22-language-ai/)) |

**Patient-level impact.** Fragmentation forces **serial diagnostics, repeated imaging, and
delayed diagnosis** — a documented driver of catastrophic out-of-pocket expenditure in India,
where OOP spend is still **39.4% of total health expenditure** and **~63% of the population lacks
health insurance** ([WEF, 2025](https://www.weforum.org/stories/2025/01/india-can-be-a-global-pathfinder-in-digital-health-here-s-how/)).
Globally, **~20% of patient records are duplicates**, rippling into errors and revenue-cycle waste
([Healthcare IT News](https://www.healthcareitnews.com/news/duplication-fragmentation-hamper-interoperability-efforts-impact-patient-safety));
India's diagnostic-cost crisis is explicitly attributed to this fragmented ecosystem
([Medical Buyer](https://medicalbuyer.co.in/indias-diagnostic-cost-crisis/)).

**The AI-specific problem.** Even where data *is* digital and FHIR-compliant, it is not
*ingestible*: it is verbose, nested, code-as-pointer, split across resources, and monolingual per
record. An AI scribe, diagnostic model, or clinical copilot must each rebuild the same expensive
normalization pipeline. **There is no shared "last mile" from standardized data to AI context.**

---

## 3. Market Opportunity (India-first)

> Where sources conflict, **lower-bound / conservative figures are used** and ranges are flagged.

### 3.1 Headline India markets

| Market (India) | 2025 size | Projection | CAGR | Source |
|---|---|---|---|---|
| Healthcare IT | ~USD 19.4B | USD 83.6B (2033) / USD 112B (2034) | ~19.7–20% | [DataBridge](https://www.databridgemarketresearch.com/reports/india-healthcare-it-market) · [IMARC](https://www.imarcgroup.com/india-healthcare-it-market) |
| Digital health | ~USD 14.5–19.1B | USD 84B (2034) | ~17.3% (up to ~26%) | [Medical Buyer](https://medicalbuyer.co.in/india-digital-health-market-to-reach-usd-84076-5m/) · [Nexdigm](https://www.nexdigm.com/market-research/report-store/india-digital-health-market/) |
| **AI in healthcare** | ~USD 0.44B | USD 4.8B (2034) / USD 18.6B (2035) | **~28–42%** | [IMARC](https://www.imarcgroup.com/india-artificial-intelligence-in-healthcare-market) · [MRFR](https://www.marketresearchfuture.com/reports/india-healthcare-artificial-intelligence-market-43893) · [Grand View](https://www.grandviewresearch.com/horizon/outlook/ai-in-healthcare-market/india) |
| EHR | ~USD 726M | USD 1.48B (2034) | ~8% | [IMARC](https://www.imarcgroup.com/india-electronic-health-records-market) |
| Telemedicine | ~USD 3.64B | USD 12.63B (2031) | ~23% | [Mordor](https://www.mordorintelligence.com/industry-reports/india-telemedicine-market) |

> **Estimate variance is real.** India AI-in-healthcare CAGR ranges from 27.7% (conservative,
> [MRFR](https://www.marketresearchfuture.com/reports/india-healthcare-artificial-intelligence-market-43893))
> to 41.8% (aggressive, [Grand View](https://www.grandviewresearch.com/horizon/outlook/ai-in-healthcare-market/india)),
> reflecting differing definitions of "AI in healthcare." We anchor planning to the **~28–30%** mid-range.

### 3.2 TAM / SAM / SOM (India)

- **TAM** — the AI-in-healthcare + healthcare-IT data layer in India, **~USD 19–20B (2025)**
  growing to **USD 80B+ by 2033–34**. Data infrastructure / "data readiness for AI" is the
  fastest-growing slice (globally the AI-data-integration subset grows ~19% CAGR,
  [HTF, 2026](https://www.htfmarketinsights.com/report/4374241-data-integration-ai-market)).
- **SAM** — organizations that must both (a) comply with ABDM/FHIR mandates and (b) feed AI:
  ABDM-empaneled facilities, diagnostic-lab chains, payers via NHCX, and the health-AI startup
  ecosystem. A **"data normalization + AI-ingestion layer"** SAM in the low **USD billions**.
- **SOM (Year 1–3)** — design partners among large private chains (Apollo, Manipal, Max, Fortis),
  national diagnostic labs (Dr. Lal PathLabs, SRL, Metropolis), and 20–50 AI-diagnostics/scribe
  startups needing clean training/RAG data. A focused **USD 10–50M** obtainable services + platform
  wedge.

### 3.3 Buyer segments & willingness to pay

| Segment | Why they pay | Examples |
|---|---|---|
| **Hospital chains** | ABDM/FHIR compliance + AI copilots + reduced documentation burden | Apollo, Manipal, Max, Fortis, CARE ([Watchdoq](https://watchdoq.com/blog/post/top-25-multispecialty-hospital-chains-in-india-2025)) |
| **Diagnostic labs** | Cross-referral data, AI triage, duplicate-test reduction | Dr. Lal PathLabs, SRL, Metropolis, Redcliffe ([Industrial Review](https://theindustrialreview.com/2026/02/25/diagnostic-labs-in-india/)) |
| **Payers / NHCX** | Faster, cleaner claims adjudication | NHCX under ABDM ([NATHEALTH](https://nathealthindia.org/wp-content/uploads/2025/06/National-Health-Claims-Exchange_Latest.pdf)) |
| **Health-tech / SaaS** | Foundational clean-data layer for products | Tata 1mg, Practo, Eka Care, HealthPlix ([Growth Jockey](https://www.growthjockey.com/blogs/healthcare-startups-in-india)) |
| **AI diagnostics startups** | Standardized, multilingual training/RAG data | Qure.ai, Niramai, Tricog, SigTuple, Dozee ([Analytics Insight](https://www.analyticsinsight.net/artificial-intelligence/10-best-indian-startups-using-ai-to-transform-healthcare-in-2025/)) |
| **Government / NHA** | ABDM data activation, population health | NHA, MoHFW, state health depts ([NHA](https://nha.gov.in/PM-JAY)) |
| **AI labs / model cos.** | Clean health corpora for fine-tuning & eval | OpenAI/Anthropic/Google all entered healthcare in Jan 2026 ([DigitalHealth.net](https://www.digitalhealth.net/2026/01/anthropic-and-google-follow-chatgpt-to-launch-healthcare-ai/)) |

### 3.4 Cross-industry signal (expansion path)

The same fragmentation tax appears in **financial services** (~$8M/quarter per large enterprise,
[Squid AI, 2025](https://getsquid.ai/blog/the-hidden-cost-of-data-fragmentation-in-financial-services))
and across enterprise knowledge. India's DEPA/Account-Aggregator model already proved the
consent-based data-sharing pattern in finance — the same canonical-format playbook generalizes
beyond health once proven.

---

## 4. Why Now

Four forces converge in 2025–2026, uniquely strongly in India:

1. **ABDM has reached critical mass.** ~90 crore ABHA IDs and 100 crore+ linked records by
   mid‑2026, with ~10 crore records linked every 2–3 months
   ([PIB](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2264241)). The data substrate now exists.
2. **Mandated standards + incentives.** ABDM mandates **HL7 FHIR R4** with India-specific
   Implementation Guides, plus **SNOMED CT, LOINC, ICD‑10, DICOM**
   ([VertiComply](https://verticomply.com/compliance-info/abdm)); the Digital Health Incentive
   Scheme pays facilities to digitize. Compliance is becoming a cost-of-doing-business.
3. **Governance is ready for AI.** **DPDP Act 2023** + regulated **Consent Managers** (built on
   DEPA) ([AZB](https://www.azbpartners.com/bank/consent-managers-under-indias-dpdp-act-and-dpdp-rules/)),
   **ICMR AI ethics (2023)**, **CDSCO** device regulation, and the **SAHI national health-AI
   strategy (Feb 2026)** ([OC Academy](https://www.ocacademy.in/blogs/national-health-ai-strategy-india-2026/))
   create a clear lane for AI at scale. The **IndiaAI Mission** backs it with ₹10,371 crore.
4. **The LLM/agentic + RAG boom.** RAG cuts hallucinations ~70–90% vs. standalone LLMs
   ([iatrox, 2025](https://www.iatrox.com/blog/rag-in-healthcare-benefits-evidence-safe-deployment-guide))
   but demands clean, chunkable, grounded data — exactly what current formats fail to provide. MCP
   (Anthropic, late 2024) is now the de-facto standard for connecting agents to data.

**Why now, in one line:** the data exists (ABDM), the rules exist (FHIR/DPDP/SAHI), the demand
exists (RAG/agents) — but the **AI-ingestion layer between them does not.**

---

## 5. Existing Solutions & Gap Analysis

### 5.1 Standards — built for exchange, not ingestion

| Standard | Standardizes | Limitation for AI ingestion |
|---|---|---|
| **HL7 v2** | ADT, orders, results messaging | Pipe-delimited, weak semantics, no ontology links, token bloat |
| **C‑CDA** | Clinical documents (XML) | Deeply nested XML; narrative buried in free-text blocks |
| **FHIR R4/R5** | All clinical resources (REST/JSON) | Verbose nesting; **codes are pointers, not labels**; cross-resource references; up to **391× token blow-up** in retrieval ([FHIRPath-QA](https://arxiv.org/pdf/2602.23479)) |
| **USCDI** | Mandatory data elements (US) | Defines *what*, not *how to serialize for AI*; still FHIR-verbose |
| **OMOP CDM** | Observational research schema | SQL/BI-oriented; huge vocab lookups; no narrative |
| **DICOM** | Imaging + metadata | Binary pixels; needs separate vision encoding; no clinical context |
| **X12** | Claims/billing EDI | Transactional only; opaque loops/segments |

### 5.2 Interoperability vendors — they stop at FHIR

Redox, Health Gorilla, Particle Health, Datavant, 1upHealth, Innovaccer, Rhapsody/Lyniate,
Metriport, Zus Health — and India-active Innovaccer — all **normalize to FHIR/HL7 and stop there**.
None ship a **token-efficient, semantically inlined, RAG-chunkable serialization**; downstream
teams must re-denormalize for every AI use case
([Keragon](https://www.keragon.com/blog/healthcare-interoperability-vendors)).

### 5.3 AI-specific efforts — partial, not productized

- **HL7 Markdown "AI bundles"** (2025) — experimental, reduces noise, but is *reformatted FHIR*,
  not a richer semantic model ([HL7](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/)).
- **MEDS** (Medical Event Data Standard) — minimal `(subject, code, timestamp, value)` events;
  great for ML, but loses hierarchy and ignores unstructured text
  ([MEDS](https://medical-event-data-standard.github.io/)).
- **FHIR‑RAG‑MEDS / FHIR‑AgentBench / LLMonFHIR** — research that *consumes* verbose FHIR; they
  measure or work around the problem rather than fixing the format
  ([FHIR-AgentBench](https://arxiv.org/html/2509.19319v2)).
- **Clinical NLP/scribes** (Abridge, Nabla, Regard; India: similar tooling) — produce proprietary
  structured output for a narrow slice (notes/coding), not a general ingestion standard.

### 5.4 Cross-industry analogs

- **MCP** — a *protocol* for exposing data/tools to agents, not a *data format*; still returns raw
  payloads ([Google Cloud](https://cloud.google.com/discover/what-is-model-context-protocol)).
- **llms.txt** — a navigation descriptor; doesn't specify efficient serialization.
- **Structured Outputs / JSON Schema** — constrains LLM *output*, not *input*.
- **Data contracts** — define producer/consumer schemas upstream of ingestion.

### 5.5 The whitespace

> **No production-grade, AI-native, source-agnostic ingestion format exists.** Such a format would
> need to be: compact (1 event ≈ 1 line, not 30+ nested fields), semantically self-describing
> (codes *with* labels), unify structured + narrative + multilingual content, embed provenance and
> **consent/PHI metadata**, be deterministically chunkable for RAG, and be exposable via MCP. This
> is the gap "HealthLM" targets.

---

## 6. Why India Is the Beachhead

1. **A proven DPI leapfrog playbook.** UPI leapfrogged cards; Aadhaar issued 1.4B+ IDs; ABDM
   replicates the open, federated, consent-based pattern for health
   ([ORF America](https://orfamerica.org/newresearch/dpi-catalyst-private-sector-innovation)).
   New layers ride existing rails instead of fighting incumbents.
2. **Greenfield + mandated.** Unlike the US (entrenched Epic/Cerner, FHIR-by-regulation but
   vendor-locked), India is digitizing *now*, on open standards, with government mandate and
   incentives — far lower adoption friction for a new format layer.
3. **Acute, quantified pain.** Duplicated diagnostics + high OOP spend + low insurance penetration
   make fragmentation immediately, financially visible to patients and payers.
4. **Vernacular AI is a requirement, not a feature.** 22 languages mean any AI-ingestion format
   must carry **language-tagged content** natively — a structural advantage India forces early,
   and a moat when expanding globally.

---

## 7. Proposed Solution — "HealthLM": an AI-Native Canonical Format

**HealthLM is not a new EHR or a replacement for FHIR.** It is a thin **canonical
intermediate-representation + serving layer** that ingests existing standards and emits
**LLM-ready context**. FHIR remains the system-of-record/exchange standard; HealthLM is the
**last mile to AI**.

### 7.1 Design principles

1. **Dual representation** — every record serializes to (a) **human-readable Markdown** clinicians
   can read and (b) **machine-parseable structured blocks** for deterministic parsing.
2. **Source-agnostic mapping** — adapters map **FHIR R4/R5, HL7 v2, C‑CDA, and scanned/PDF
   records** into one canonical IR (no information loss; provenance preserved).
3. **Codes with labels, inline** — `LOINC|8480-6|Systolic BP` instead of a pointer requiring
   lookup. Removes the indirection that costs LLMs accuracy and tokens.
4. **Embedded provenance & time** — source system, facility, timestamp, and transformation
   lineage travel *with* the data (W3C PROV-aligned).
5. **Consent & PHI metadata, DPDP-aligned** — every record carries `is_phi`, `pii_fields`,
   `consent_scope`, retention, and role-visibility — enabling automated masking and Consent-Manager
   integration.
6. **Language tags (BHASHINI/22-lang)** — content blocks carry `lang` tags so the same schema
   serves multilingual AI across India.
7. **Token efficiency & deterministic ordering** — canonical section order, deduped codes, compact
   tabular form for repeated measures (TOON). Reproducible hashes for audit.
8. **RAG-chunkable & MCP-exposable** — hierarchical sections map cleanly to retrieval chunks;
   served to agents as an MCP resource over ABDM data.

### 7.2 Concrete example — same blood-pressure observation

**(a) Raw FHIR JSON** — verbose, nested, code-as-pointer (~380 tokens):

```json
{
  "resourceType": "Observation",
  "status": "final",
  "code": { "coding": [{ "system": "http://loinc.org", "code": "55284-4", "display": "Blood Pressure" }] },
  "subject": { "reference": "Patient/pt-123" },
  "effectiveDateTime": "2026-06-15T10:30:00Z",
  "component": [
    { "code": { "coding": [{ "system": "http://loinc.org", "code": "8480-6", "display": "Systolic BP" }] },
      "valueQuantity": { "value": 140, "unit": "mm[Hg]" } },
    { "code": { "coding": [{ "system": "http://loinc.org", "code": "8462-4", "display": "Diastolic BP" }] },
      "valueQuantity": { "value": 90, "unit": "mm[Hg]" } }
  ]
}
```

**(b) HealthLM — Markdown + YAML front-matter** (~180 tokens; human + machine readable):

```markdown
---
healthlm_version: "0.1"
patient: { abha: "12-3456-7890-1234", name: "—", is_phi: true, pii_fields: ["name","abha"] }
source: { system: "ABDM/HFR:hosp-001", facility: "Apollo CHN", ts: "2026-06-15T10:30:00Z" }
consent: { scope: "clinical_care", manager: "ABDM-HIE-CM" }
lang: ["en","ta"]
---

## Vital Signs — 2026-06-15 10:30
**Blood Pressure** (LOINC|55284-4)
- Systolic: 140 mm[Hg] (LOINC|8480-6) — ELEVATED
- Diastolic: 90 mm[Hg] (LOINC|8462-4) — NORMAL
```

**(c) HealthLM — TOON tabular** (for time-series / repeated measures; ~40–60% fewer tokens than
JSON, [InfoQ, 2025](https://www.infoq.com/news/2025/11/toon-reduce-llm-cost-tokens/)):

```toon
Observations
code,display,value,unit,ts,status
LOINC|8480-6,Systolic BP,140,mm[Hg],2026-06-15T10:30Z,final
LOINC|8462-4,Diastolic BP,90,mm[Hg],2026-06-15T10:30Z,final
LOINC|2823-3,Heart Rate,72,/min,2026-06-15T10:30Z,final
```

### 7.3 Architecture sketch

```
            ┌────────── Source adapters ──────────┐
 FHIR R4/R5 │  HL7 v2   C-CDA   PDF/scan (OCR+NLP) │
            └──────────────────┬──────────────────┘
                               ▼
                  Canonical IR (HealthLM core)
        normalize terminology · inline labels · attach
        provenance + consent + language · dedupe · order
                               ▼
        ┌──────────────┬───────────────┬──────────────┐
        ▼              ▼               ▼              ▼
   Markdown view   TOON tables   RAG chunks +     MCP server
   (clinician /    (analytics /  embeddings       (agents query
    LLM context)    time-series)  (vector DB)      ABDM data safely)
```

Positioned this way, HealthLM is **complementary to ABDM/FHIR** (consumes them) and to **MCP**
(serves through it) — reducing adoption risk.

---

## 8. Go-to-Market & Business Model

**Wedge (Year 1):** be the clean-data layer for teams already blocked on it —
**AI-diagnostics/scribe startups** (Qure.ai, Tricog, SigTuple-type buyers) and **diagnostic-lab
chains** drowning in cross-referral fragmentation. These have urgent, budgeted pain and short
sales cycles.

**Land-and-expand:**
1. **Startups & labs** → paid SDK/API + managed pipelines.
2. **Hospital chains** → enterprise deployment for ABDM compliance + internal AI copilots.
3. **Payers / NHCX** → claims-grade canonical data.
4. **Government / NHA** → reference open spec + certification (standard-setter position).

**Business model:**
- **Open core**: publish the HealthLM spec openly (drives adoption + standard-status moat); monetize
  the **mapping/serving engine, managed pipelines, consent integration, and certification**.
- **Usage-based pricing** (per record normalized / per MCP query) + enterprise contracts.

**Partnerships:** NHA/ABDM (reference alignment), C‑DAC (national FHIR adaptation), hospital and
lab chains as design partners, and AI labs needing Indian-language health corpora.

---

## 9. Open-Source, Adoption & Distribution Strategy

A data *format* only has value if it becomes a *standard* — and standards are won through
adoption, not licensing. The strategy is **open-core**: the **specification and reference tooling
are open source**; the **managed engine, hosted pipelines, consent integration, and certification**
are commercial.

### 9.1 Why open source (the advantages)

1. **Standards win by ubiquity, not secrecy.** A proprietary "standard" is a contradiction. Open
   sourcing is how MCP (Anthropic → Linux Foundation), FHIR, and India's own UPI/DEPA achieved
   network effects. Each new adopter and adapter makes the format more valuable to everyone — a
   classic flywheel a closed format cannot start.
2. **Trust in a regulated, PHI domain.** Hospitals, NHA, and payers will not bet patient data on an
   opaque format. Open specs are **auditable** for privacy/DPDP compliance and safety — a
   prerequisite for institutional and government adoption.
3. **Lower adoption friction = faster distribution.** Free SDK + permissive license lets a startup
   engineer adopt today without procurement. Bottom-up developer adoption precedes top-down
   enterprise deals.
4. **Government / standard-setter alignment.** An open spec can be proposed for **NHA/ABDM reference
   alignment** and C‑DAC collaboration. Public infrastructure adopts open standards, not vendor
   lock-in. This is the single biggest distribution lever in India.
5. **Community as R&D and moat.** External contributors build adapters (more source systems),
   language packs (22 Indian languages), and integrations — work you'd otherwise fund. The breadth
   of the ecosystem *becomes* the moat.
6. **Credibility & talent.** A respected open project attracts contributors, design partners, and
   hires, and earns citations/benchmarks that compound trust.
7. **Defensive standardization.** If you don't set the open standard, a competitor (or HL7's
   Markdown bundles) will. Open sourcing pre-empts a rival owning the category.

**The trade-off to manage:** open source ≠ free business. Monetize the **hard-to-replicate** parts
(managed service, SLAs, certification, enterprise consent/governance, support), not the spec.

### 9.2 How to open source it — mechanics

- **License:** spec under **CC BY 4.0** (or CC0 for maximum reach); reference SDKs/adapters under
  **Apache 2.0** (patent grant reassures enterprises). Keep the managed engine in a separate
  proprietary/commercial repo (open-core boundary).
- **Governance:** start as a **BDFL/company-led** project for velocity; publish a public roadmap and
  RFC process; commit to migrating to a **neutral foundation** (e.g., Linux Foundation / a health
  DPI body) once adopted — this neutrality is what unlocks government and competitor buy-in.
- **Repo hygiene:** clear spec versioning (semver), conformance test suite, `CONTRIBUTING.md`, code
  of conduct, reference implementation, and a **public conformance badge** so vendors can claim
  "HealthLM-compatible."
- **Spec + conformance, not just code:** publish the schema, JSON-Schema/validators, round-trip
  fidelity tests vs. FHIR, and a benchmark (token efficiency + retrieval accuracy vs. raw FHIR) so
  adoption is evidence-backed.

### 9.3 How to create adoption & distribution

**Sequence — developers → projects → institutions → standard body:**

1. **Make the first integration trivial.** One-command SDK, a hosted playground that pastes raw
   FHIR/HL7 and shows the HealthLM + token-savings diff, great docs, and an **MCP server** so any
   agent works out-of-the-box. Time-to-first-value in minutes.
2. **Seed with budgeted, high-pain adopters.** Land design partners among AI-diagnostics/scribe
   startups and diagnostic-lab chains (§3.3); co-publish case studies with hard numbers (tokens
   saved, retrieval accuracy, build time avoided).
3. **Ride existing rails.** Ship **adapters for ABDM/FHIR R4, HL7 v2, C‑CDA**, and plugins for the
   tools people already use (LangChain/LlamaIndex, vector DBs, popular EHR/EMR SaaS like HealthPlix,
   Eka Care). Distribution = meeting developers where they are.
4. **Publish a public benchmark.** A "HealthLM vs. FHIR for AI" leaderboard (token cost, RAG
   accuracy) turns the format into a citable, defensible reference — the way benchmarks drove RAG
   and MCP adoption.
5. **Pursue standard-body endorsement.** Engage NHA/ABDM, C‑DAC, and HL7 India early; aim for the
   spec to be referenced in ABDM implementation guidance. Government endorsement is the distribution
   multiplier unique to India's DPI model.
6. **Community building:** RFC process, contributor grants/bounties for adapters and language packs,
   sample datasets, hackathons (tie into IndiaAI Mission / SAHI), and a healthcare-AI developer
   community.

### 9.4 Monetization alongside open source (open-core)

| Open (free) | Commercial (paid) |
|---|---|
| Spec, schemas, validators | Managed normalization engine + hosted pipelines |
| Reference SDKs & core adapters | Enterprise adapters, SLAs, support |
| Conformance test suite | DPDP/consent governance + audit tooling |
| MCP reference server | Hosted MCP-as-a-service over ABDM, usage-based pricing |
| Benchmarks & docs | Certification ("HealthLM-certified") program |

This mirrors proven open-core companies (e.g., data-infra and dev-tool firms): the standard is free
and ubiquitous; the **operational reliability, compliance, and scale** are the business.

---

## 10. Building the Solution — Implementation Blueprint

The format is the standard; the **solution** is the engine + services that produce, validate, and
serve it. Think of it as three layers: **(1) the spec & SDK** (open), **(2) the normalization
engine** (the core IP), and **(3) the serving/product surface** (where users and agents consume it).

### 10.1 Component architecture

```
 ┌───────────────────────── 1. SPEC & SDK (open source) ─────────────────────────┐
 │  HealthLM schema · validators · canonical-IR model · Markdown/TOON renderers   │
 └───────────────────────────────────────────────────────────────────────────────┘
                                      ▲ uses
 ┌───────────────────────── 2. NORMALIZATION ENGINE (core IP) ───────────────────┐
 │  Source adapters → Parser → Terminology service → Enrichment → Canonical IR    │
 │  FHIR/HL7v2/C-CDA/PDF    (SNOMED/LOINC/ICD)   (provenance,consent,lang,dedupe) │
 └───────────────────────────────────────────────────────────────────────────────┘
                                      ▼ emits
 ┌───────────────────────── 3. SERVING & PRODUCT SURFACE ────────────────────────┐
 │  REST/gRPC API · RAG chunker + embeddings → vector DB · MCP server · Playground │
 │  Consent/DPDP gateway (ABDM HIE-CM) · audit log · dashboard · conformance badge │
 └───────────────────────────────────────────────────────────────────────────────┘
```

**1. Spec & SDK (open).** The HealthLM data model as typed objects, JSON-Schema validators, and
deterministic Markdown/TOON serializers. Ships as a library (Python first — the AI ecosystem lives
there; then TS/Java for enterprise EHR teams). This is what developers integrate; keep it tiny and
dependency-light so adoption is frictionless.

**2. Normalization engine (the moat).** A pipeline:
- **Source adapters** parse FHIR R4/R5, HL7 v2, C‑CDA, and scanned/PDF records (OCR + clinical NLP
  for the unstructured tail).
- **Terminology service** resolves codes ↔ human labels across SNOMED CT / LOINC / ICD‑10 (India's
  ABDM-mandated sets), caching the heavy vocab tables.
- **Enrichment** attaches provenance (W3C PROV), consent/PHI metadata (DPDP-aligned), language tags
  (BHASHINI/22-lang), then dedupes and canonically orders into the **canonical IR**.
- **Round-trip fidelity tests** guarantee no information loss vs. the source FHIR.

**3. Serving & product surface.**
- **API** (REST + gRPC) to submit source records and retrieve HealthLM views (Markdown / TOON /
  chunks).
- **RAG pipeline:** chunker → embeddings → vector DB (pgvector / Qdrant), so consumers get
  retrieval-ready output, not just normalized text.
- **MCP server:** exposes patient context to any agent (Claude, etc.) with scoped, consent-checked
  access — the agent-native distribution channel.
- **Consent/DPDP gateway:** integrates ABDM's HIE Consent Manager; enforces purpose/role visibility
  and writes an immutable audit log.
- **Playground + dashboard:** paste raw FHIR/HL7 → see HealthLM output + token-savings diff; usage,
  conformance, and audit views.

### 10.2 Reference tech stack

| Layer | Choice | Why |
|---|---|---|
| SDK / engine | **Python** (core), then TS/Java | AI/ML ecosystem; enterprise EHR teams later |
| OCR / clinical NLP | Off-the-shelf OCR + an LLM extraction step (structured outputs) | Handle the paper/PDF tail; constrained decoding for valid schema |
| Terminology | Hosted SNOMED/LOINC/ICD service + cache | Heavy tables; resolve once, reuse |
| Storage | Object store (raw) + Postgres (metadata) + **pgvector/Qdrant** (embeddings) | Lakehouse-lite; vectors for RAG |
| Serving | FastAPI / gRPC + an **MCP server** | Standard APIs + agent-native channel |
| Embeddings / LLM | Pluggable (multilingual models for 22-lang) | Avoid lock-in; India-language support |
| Deploy | Containers; **India-region / on-prem option** | Data residency (DPDP) for hospitals |

### 10.3 MVP → scale (phased build)

- **Phase 0 — Spec v0.1 + FHIR adapter (weeks).** Publish schema, validators, Markdown/TOON
  serializer, and a **FHIR R4 → HealthLM** converter with a public playground showing token savings.
  *Goal: developers can try it and see the diff.*
- **Phase 1 — Engine + RAG + MCP (1–2 quarters).** Add terminology resolution, provenance, the RAG
  chunker/embeddings, and an MCP server. Land 3–5 design partners (an AI-scribe/diagnostics startup
  + a diagnostic-lab chain). *Goal: a real product feeding real AI.*
- **Phase 2 — Consent/DPDP + ABDM + more adapters (2–3 quarters).** HIE-CM consent integration,
  audit logging, HL7 v2 / C‑CDA / PDF adapters, multilingual enrichment. *Goal: hospital-grade,
  compliant, India-ready.*
- **Phase 3 — Conformance program + benchmark + scale.** "HealthLM-certified" badge, public
  "HealthLM vs. FHIR for AI" benchmark, hosted MCP-as-a-service, enterprise SLAs. *Goal: standard
  status + recurring revenue.*

### 10.4 What to validate (technical proof points)

1. **Token efficiency** — measured reduction vs. raw FHIR on real bundles (target meaningful % cut).
2. **Retrieval accuracy** — RAG answer quality on HealthLM chunks vs. FHIR baseline (e.g., on an
   EHR-QA set akin to FHIR-AgentBench).
3. **Round-trip fidelity** — no information loss source → IR → source.
4. **Consent enforcement** — masked fields never reach the model; audit log is complete.
5. **Multilingual integrity** — language-tagged content survives normalization and retrieval.

### 10.5 Team to build it (lean MVP)

A founding team of ~3–5: a **health-data/FHIR engineer**, an **AI/RAG engineer**, a **backend/infra
engineer**, plus **clinical + compliance (DPDP/ABDM) advisory**. The open SDK and benchmark are the
top-of-funnel; design-partner deployments fund the engine build.

---

## 11. Investor Thesis (Condensed)

- **Problem:** Fragmented health data is the binding constraint on healthcare AI; existing
  standards are exchange-native, not AI-native.
- **TAM:** India healthcare-IT/AI data layer ~USD 19–20B (2025) → USD 80B+ by 2033–34; AI-in-health
  growing ~28–42% CAGR; data-readiness is the fastest-growing slice.
- **Why now:** ABDM at scale + FHIR/DPDP/SAHI mandates + RAG/agent demand + MCP standardization.
- **Moat:** an open standard with network effects (every adapter & adopter increases value) +
  ABDM data-network position + DPDP/consent compliance + multilingual structural advantage.
- **Wedge → expansion:** health AI startups/labs → hospitals/payers → cross-industry (finance,
  enterprise) using the same canonical-format + consent playbook India already proved with DEPA.
- **Ask / milestones (illustrative):** seed to ship spec v1 + adapters (FHIR/HL7/C‑CDA) + MCP
  server, land 10–20 design partners, and pursue NHA reference alignment.

---

## 12. Risks & Open Questions

| Risk | Mitigation / open question |
|---|---|
| **Standards inertia** (FHIR is mandated) | Position as *complement* to FHIR, not replacement; consume FHIR, emit AI-ready views |
| **Adoption incentives** | Lead with budgeted buyers (AI startups, labs); ride ABDM DHIS incentives |
| **Privacy / consent** | DPDP-aligned consent metadata + Consent-Manager integration from day one |
| **Multimodal (imaging)** | v1 handles text + codes; DICOM via references + captions; full multimodal later |
| **Who owns the standard?** | Open core vs. proprietary — recommend open spec + monetized engine/certification |
| **Mapping fidelity** | Round-trip tests vs. source FHIR; no-information-loss guarantee + provenance |
| **Market-size uncertainty** | Plan to conservative (~28–30%) CAGR; ranges flagged in §3 |

---

## 13. Appendix: Sources

**India — ABDM, policy, market**
- [Organiser — 90 crore ABHA milestone (May 2026)](https://organiser.org/2026/05/31/355992/bharat/ayushman-bharat-crosses-90-cr-abha-accounts-how-modi-govt-is-building-the-worlds-largest-digital-health-ecosystem/)
- [PIB — 100 crore health records linked](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2264241)
- [NHA — Ayushman Bharat Digital Mission](https://abdm.gov.in/) · [NHA — PM-JAY](https://nha.gov.in/PM-JAY)
- [VertiComply — ABDM compliance (FHIR R4, SNOMED/LOINC/ICD)](https://verticomply.com/compliance-info/abdm)
- [AZB — Consent Managers under DPDP](https://www.azbpartners.com/bank/consent-managers-under-indias-dpdp-act-and-dpdp-rules/) · [Securiti — DPDPA Consent Managers](https://securiti.ai/india-dpdpa-consent-managers/)
- [ICMR — AI ethics guidelines (2023)](https://www.icmr.gov.in/icmrobject/custom_data/pdf/Ethical-guidelines/Ethical_Guidelines_AI_Healthcare_2023.pdf) · [OC Academy — SAHI framework (2026)](https://www.ocacademy.in/blogs/national-health-ai-strategy-india-2026/)
- [Zealthix — ABDM adoption 2025](https://zealthix.com/abdm-adoption-in-2025-opportunities-and-roadblocks-for-indian-healthcare-providers/)
- [WEF — India as digital-health pathfinder](https://www.weforum.org/stories/2025/01/india-can-be-a-global-pathfinder-in-digital-health-here-s-how/)
- [AICerts — BHASHINI 22-language health AI](https://www.aicerts.ai/news/bhashini-boosts-health-platform-accessibility-with-22-language-ai/)

**India — market sizing**
- [IMARC — Healthcare IT](https://www.imarcgroup.com/india-healthcare-it-market) · [AI in healthcare](https://www.imarcgroup.com/india-artificial-intelligence-in-healthcare-market) · [EHR](https://www.imarcgroup.com/india-electronic-health-records-market)
- [DataBridge — Healthcare IT](https://www.databridgemarketresearch.com/reports/india-healthcare-it-market) · [Grand View — AI in healthcare (India)](https://www.grandviewresearch.com/horizon/outlook/ai-in-healthcare-market/india) · [MRFR — Healthcare AI](https://www.marketresearchfuture.com/reports/india-healthcare-artificial-intelligence-market-43893)
- [Medical Buyer — digital health](https://medicalbuyer.co.in/india-digital-health-market-to-reach-usd-84076-5m/) · [Mordor — telemedicine](https://www.mordorintelligence.com/industry-reports/india-telemedicine-market)

**India — players**
- [Watchdoq — top hospital chains](https://watchdoq.com/blog/post/top-25-multispecialty-hospital-chains-in-india-2025) · [Industrial Review — diagnostic labs](https://theindustrialreview.com/2026/02/25/diagnostic-labs-in-india/)
- [Growth Jockey — health startups](https://www.growthjockey.com/blogs/healthcare-startups-in-india) · [Analytics Insight — AI health startups](https://www.analyticsinsight.net/artificial-intelligence/10-best-indian-startups-using-ai-to-transform-healthcare-in-2025/)
- [NATHEALTH — National Health Claims Exchange](https://nathealthindia.org/wp-content/uploads/2025/06/National-Health-Claims-Exchange_Latest.pdf)

**Standards, competition, technical**
- [FHIRPath-QA — token blow-up](https://arxiv.org/pdf/2602.23479) · [FHIR-AgentBench](https://arxiv.org/html/2509.19319v2) · [FHIR-RAG-MEDS](https://arxiv.org/pdf/2509.07706)
- [HL7 — AI Office (Markdown bundles)](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/) · [MEDS](https://medical-event-data-standard.github.io/)
- [Keragon — interoperability vendors](https://www.keragon.com/blog/healthcare-interoperability-vendors)
- [Google Cloud — Model Context Protocol](https://cloud.google.com/discover/what-is-model-context-protocol) · [InfoQ — TOON](https://www.infoq.com/news/2025/11/toon-reduce-llm-cost-tokens/)
- [iatrox — RAG in healthcare](https://www.iatrox.com/blog/rag-in-healthcare-benefits-evidence-safe-deployment-guide)

**Global market / cross-industry**
- [ZLTI — data readiness 2026](https://www.zlti.com/blog/in-2026-ai-success-will-be-decided-by-unstructured-data-management) · [HTF — data-integration AI market](https://www.htfmarketinsights.com/report/4374241-data-integration-ai-market)
- [Chief Healthcare Executive — fragmentation costs](https://www.chiefhealthcareexecutive.com/view/consequences-of-fragmented-healthcare-information-systems) · [blueBriX — fragmentation](https://bluebrix.health/blogs/connecting-the-dots-eliminating-fragmented-systems-workflows-for-coordinated-care)
- [Squid AI — financial-services fragmentation](https://getsquid.ai/blog/the-hidden-cost-of-data-fragmentation-in-financial-services) · [LeadGen Economy — $3.1T silos](https://www.leadgen-economy.com/blog/enterprise-data-fragmentation-mcp-rag-solution/)
- [DigitalHealth.net — model cos. enter healthcare](https://www.digitalhealth.net/2026/01/anthropic-and-google-follow-chatgpt-to-launch-healthcare-ai/)

---

*Draft v0.1 — figures reflect 2025–2026 sources; where estimates conflict, conservative values are
used and ranges noted. Intended as a living design/strategy document.*
