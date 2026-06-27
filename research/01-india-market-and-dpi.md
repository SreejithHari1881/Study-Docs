# Research Data: India Digital Health Market & DPI (ABDM)

> Source research feeding `design.md` §2–§6. Figures are 2025–2026; where estimates conflict,
> ranges are noted. All claims carry inline source links.

## 1. India's health data fragmentation

- EHR adoption concentrated in urban private chains (Apollo, Fortis, Max) + select govt (AIIMS);
  tier-2/3 & rural largely paper-based.
- Only ~60% of healthcare orgs implementing online patient record access (2025)
  ([EHR Industry Statistics, 2026](https://media.market.us/ehr-industry-statistics/)).
- India EHR market: USD 726.1M (2025) → USD 1,483.4M (2034) at 8.01% CAGR
  ([IMARC](https://www.imarcgroup.com/india-electronic-health-records-market)); alt: USD 739.3M
  (2025) → USD 1,396.2M (2033) at 8.3% ([Grand View](https://www.grandviewresearch.com/horizon/outlook/electronic-health-records-market/india)).
  Web/cloud EHR = 90.82% revenue share (2025).
- Fragmentation metrics: ~28% of EHR initiatives stalled by interoperability gaps; ~28% by
  privacy/security ([IMARC](https://www.imarcgroup.com/india-electronic-health-records-market)).
- Patient impact: serial diagnostics, duplicated imaging, delayed diagnosis → catastrophic OOP
  spend; ~20% of records are duplicates globally
  ([Healthcare IT News](https://www.healthcareitnews.com/news/duplication-fragmentation-hamper-interoperability-efforts-impact-patient-safety));
  India diagnostic-cost crisis attributed to fragmentation
  ([Medical Buyer](https://medicalbuyer.co.in/indias-diagnostic-cost-crisis/));
  Karnataka qualitative study on patient navigation burden
  ([NCBI PMC5645647](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5645647/)).
- Language: 22 scheduled languages; BHASHINI AI integrated into eSanjeevani/ABDM; Voice Rx pilots
  ~30% faster dispensing ([AICerts](https://www.aicerts.ai/news/bhashini-boosts-health-platform-accessibility-with-22-language-ai/));
  Karya/Jugalbandi translators ([IBEF](https://www.ibef.org/blogs/ai-for-local-language-inclusion-through-vernacular-models)).

## 2. India's digital health stack & regulation ("why now")

### ABDM / ABHA adoption
- FY2025-26: 8.79 crore ABHA IDs created. Cumulative: 2022=30.4cr, 2023=50.6cr, 2024=72.2cr,
  2025=84.5cr, **May 2026 = 90 crore** (world's largest digital health ID program)
  ([Organiser, May 31 2026](https://organiser.org/2026/05/31/355992/bharat/ayushman-bharat-crosses-90-cr-abha-accounts-how-modi-govt-is-building-the-worlds-largest-digital-health-ecosystem/)).
- Linked health records: 82.69 crore (Jan 2026); **100 crore+** linked (Feb 2025→Jun 2026),
  ~10 crore every 2-3 months ([PIB PRID 2264241](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2264241)).
- State leaders (2026): UP 15.03cr, AP 11.95cr, Bihar 7.37cr, Rajasthan 6.32cr, Gujarat 4.77cr
  ([Digital Health News](https://www.digitalhealthnews.com/over-half-of-india-now-has-digital-health-records-govt-data-shows)).
- Components: ABHA (IDs), HPR (professionals registry), HFR (facility registry), UHI (open service
  discovery), HIE-CM (consent manager) ([ABDM](https://abdm.gov.in/)).
- Private participation: 236+ private entities integrated (2025); only ~17,000 of 130,000
  ABDM-enabled facilities private despite 44% of facilities being private
  ([Zealthix](https://zealthix.com/abdm-adoption-in-2025-opportunities-and-roadblocks-for-indian-healthcare-providers/)).

### Standards
- ABDM mandates **HL7 FHIR R4** with India IGs (ABHA IDs, Indian coding, HI Types); **SNOMED CT**
  for clinical docs, **ICD-10** (ICD-11 mapping planned), **LOINC** for labs, **DICOM** imaging;
  C-DAC maintains India's national FHIR adaptation
  ([VertiComply](https://verticomply.com/compliance-info/abdm)).

### DPDP Act 2023 & consent
- DPDP Act 2023 (Rules 2025) introduces regulated **Consent Manager** intermediary (registers with
  Data Protection Board; holds only consent artefacts, not data); built on DEPA (Account Aggregator
  model) ([AZB](https://www.azbpartners.com/bank/consent-managers-under-indias-dpdp-act-and-dpdp-rules/);
  [Securiti](https://securiti.ai/india-dpdpa-consent-managers/)).

### AI policy
- ICMR Ethical Guidelines for AI in Biomedical Research & Healthcare (March 2023) — 10
  patient-centric principles ([ICMR PDF](https://www.icmr.gov.in/icmrobject/custom_data/pdf/Ethical-guidelines/Ethical_Guidelines_AI_Healthcare_2023.pdf);
  [IndiaAI](https://indiaai.gov.in/news/icmr-releases-ethical-guidelines-for-ai-in-biomedical-research-and-healthcare)).
- CDSCO regulates AI-based medical devices ([Diligence](https://www.diligencecertification.com/ai-based-medical-devices-in-india/)).
- **SAHI** ("Strategy for AI in Healthcare", Feb 2026) — MoHFW national governance framework,
  integrates ABDM/eSanjeevani/IndiaAI ([OC Academy](https://www.ocacademy.in/blogs/national-health-ai-strategy-india-2026/);
  [Organiser](https://organiser.org/2026/02/25/341713/bharat/how-ai-and-ayushman-bharat-digital-mission-are-powering-an-ai-driven-healthcare-revolution-in-india/)).
- IndiaAI Mission (approved Mar 7 2024): ₹10,371.92 crore. eSanjeevani: 282M consultations
  (Apr 2023–Nov 2025), ~12M AI-assisted recommendations
  ([PIB PRID 2180713](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2180713)).

## 3. India market size & growth

| Market | 2025 | Projection | CAGR | Source |
|---|---|---|---|---|
| Healthcare IT | USD 19.45B (IMARC) / 19.36B (GVR) | USD 83.63B (2033) / 112.09B (2034) | 19.7–20% | [DataBridge](https://www.databridgemarketresearch.com/reports/india-healthcare-it-market) · [IMARC](https://www.imarcgroup.com/india-healthcare-it-market) |
| Digital health | USD 14.5–19.1B | USD 84.08B (2034); USD 50B potential (2033) | 17.33–26% | [Medical Buyer](https://medicalbuyer.co.in/india-digital-health-market-to-reach-usd-84076-5m/) · [Nexdigm](https://www.nexdigm.com/market-research/report-store/india-digital-health-market/) |
| AI in healthcare | USD 435.7M (IMARC) / 1,606M (MRFR) | USD 4,773.7M (2034) / 18,550M (2035) | **27.7–41.8%** | [IMARC](https://www.imarcgroup.com/india-artificial-intelligence-in-healthcare-market) · [MRFR](https://www.marketresearchfuture.com/reports/india-healthcare-artificial-intelligence-market-43893) · [GVR](https://www.grandviewresearch.com/horizon/outlook/ai-in-healthcare-market/india) |
| Telemedicine | USD 3.64B | USD 12.63B (2031) | 23.05% | [Mordor](https://www.mordorintelligence.com/industry-reports/india-telemedicine-market) |
| Health insurance | USD 16–158B (def. variance) | — | 8.27–16.3% (cluster 10–13%) | [IMARC](https://www.imarcgroup.com/india-health-insurance-market) · [GVR](https://www.grandviewresearch.com/horizon/outlook/healthcare-insurance-market/india) |

### Context
- PM-JAY: 12 crore families (~55 crore beneficiaries, ~40% pop), ₹5 lakh/family/year, 1,929
  procedures, 32,320 hospitals empanelled (Oct 2025), 116.9M cumulative admissions, ₹1.73 lakh
  crore sanctioned; FY25-26 budget ₹9,406 crore (+29% YoY)
  ([NHA](https://nha.gov.in/PM-JAY); [PIB PRID 2185049](https://www.pib.gov.in/PressReleasePage.aspx?PRID=2185049)).
- Total health spend: ₹6.1 lakh crore (2024-25), up from ₹3.2 lakh crore (2020-21), ~18% CAGR
  ([Down To Earth](https://www.downtoearth.org.in/health/economic-survey-2024-25-indias-health-spending-doubles-in-four-years-to-rs-61-lakh-crore)).
  Health = 3.8% of GDP (2022); govt share rose 29% (FY15) → 48% (FY22). OOP = 39.4% (2021-22),
  down from 62.6% (2014-15); ~63% lack insurance
  ([WEF](https://www.weforum.org/stories/2025/01/india-can-be-a-global-pathfinder-in-digital-health-here-s-how/)).

## 4. Indian players & buyers

- **Digital health platforms:** Practo; Tata 1mg (31% e-pharmacy share FY23, 40M+ MAU, ₹2,392 cr
  revenue FY25); HealthPlix (EMR SaaS); Eka Care (ABHA PHR, $15M Series A); Apollo 24/7
  ([Growth Jockey](https://www.growthjockey.com/blogs/healthcare-startups-in-india);
  [Deccan Founders](https://deccanfounders.com/2025/29/editor_picks/tata-1mgs-rise-how-a-simple-idea-became-indias-trusted-digital-health-platform-2025)).
- **Hospital chains:** Apollo (~7,942 beds), Manipal (33 hospitals/17 cities), Max, CARE, Fortis
  (Manipal-Fortis merger) ([Watchdoq](https://watchdoq.com/blog/post/top-25-multispecialty-hospital-chains-in-india-2025)).
- **Diagnostic labs:** Dr. Lal PathLabs (200 labs, ~5,000 centers), SRL, Metropolis, Thyrocare,
  Apollo Diagnostics, Redcliffe ([Industrial Review](https://theindustrialreview.com/2026/02/25/diagnostic-labs-in-india/)).
- **AI diagnostics startups:** Qure.ai (radiology), Niramai (thermal breast screening), Tricog
  (cardiac, 20M lives), SigTuple (AI100 microscopy, FDA 510k), Dozee (contactless monitoring)
  ([Analytics Insight](https://www.analyticsinsight.net/artificial-intelligence/10-best-indian-startups-using-ai-to-transform-healthcare-in-2025/)).
- **Multinational on India:** Innovaccer ($275M Series F; Epic/Cerner/Oracle/MEDITECH+100 sources)
  ([Innovaccer](https://innovaccer.com/); [Out-of-Pocket](https://www.outofpocket.health/p/what-does-innovaccer-actually-do-a-look-under-the-hood)).
- **Government/regulators:** NHA (ABDM owner, PM-JAY, UHI, NHCX), MoHFW (SAHI, eSanjeevani),
  state health depts, insurers (NHCX) ([NHA](https://nha.gov.in/); [NATHEALTH NHCX](https://nathealthindia.org/wp-content/uploads/2025/06/National-Health-Claims-Exchange_Latest.pdf)).

## 5. Why India is the beachhead

- **DPI leapfrog playbook:** UPI (2016), Aadhaar (1.4B+ IDs), Jan Dhan, ABDM (2021+) — federated,
  open, consent-based; govt mandate + private ecosystem + open protocols
  ([ORF America](https://orfamerica.org/newresearch/dpi-catalyst-private-sector-innovation);
  [Vajiram](https://vajiramandravi.com/current-affairs/indias-digital-public-infrastructure/)).
- **Greenfield digitization** in progress (100cr+ linked records; 90.82% cloud EHR).
- **Demographics/cost:** 1.4B pop; 55cr PM-JAY beneficiaries; 39.4% OOP; 63% uninsured; 282M
  eSanjeevani consultations.
- **Govt incentives:** Digital Health Incentive Scheme (DHIS) up to ₹4 crore/facility
  ([HOPS](https://hops.healthcare/about-abdm.html)).
- **Vernacular AI requirement:** 22 languages → standardized format with language-tagged metadata
  multiplies AI model reuse.

## Conflicting estimates (flagged)
- Healthcare IT 2025: narrow USD 19.36–19.45B; 2033-34 diverges (USD 83B vs 112B by definition).
- AI-in-healthcare CAGR: 27.7% (MRFR) – 41.8% (GVR); plan to ~28–30% mid-range.
- Health insurance 2025: USD 16–158B (definitional variance); CAGR 10–13% reliable.
- ABHA: 90 crore IDs + 100 crore linked (2026); high creation, lower active usage.
