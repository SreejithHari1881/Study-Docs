# Research Data: Competitive Landscape & Gap Analysis

> Source research feeding `design.md` §5. Standards, vendors, AI-specific efforts, cross-industry
> analogs, and the whitespace.

## 1. Established standards — capabilities & AI limitations

| Standard | Standardizes | Key AI/LLM limitation |
|---|---|---|
| **HL7 v2** (1989) | ADT, orders, results messaging | Pipe-delimited, verbose, no ontology links, weak semantics, token bloat |
| **C-CDA** (XML docs) | Discharge summaries, CCD, notes | Deeply nested XML; narrative in unstructured text blocks; optional structure |
| **FHIR R4/R5** (REST/JSON) | All clinical resources; US-certified API standard | Structural bloat (60–80% low-signal metadata); **codes as pointers, not labels**; cross-resource references; **391× token blow-up** in retrieval vs query-first ([FHIRPath-QA](https://arxiv.org/pdf/2602.23479)); R5 breaking changes fragment ecosystem |
| **USCDI** | Mandatory "must-support" elements | Defines *what* not *how to serialize for AI*; still FHIR-verbose; v6→v7 expanding |
| **OMOP CDM** | Observational research star-schema | SQL/BI-oriented; huge vocab tables; no narrative; ETL-before-load |
| **DICOM** | Imaging + metadata | Binary pixels; needs vision encoding; no clinical context; incomplete anonymization |
| **X12 EDI** | Claims/eligibility/billing | Transactional only; opaque loops/segments; batch; siloed from clinical |

HL7 response (2025): launched **AI Office** + experimental **Markdown FHIR bundles** (reduce noise,
improve token efficiency) — but experimental, just reformatted FHIR
([HL7](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/)).

## 2. Interoperability vendors & networks

| Vendor | What | Gap for AI |
|---|---|---|
| **Redox** | API platform, 90+ EHRs normalized | Outputs FHIR/HL7; no token optimization; weak unstructured |
| **Health Gorilla** | TEFCA QHIN, 66M+ queries/mo | "AI-ready" is marketing; verbose FHIR bundles |
| **Particle Health** | ~320M records, 70k+ orgs | Read-only; raw FHIR/C-CDA; no transformation |
| **Datavant** | Privacy-preserving record linkage | Linkage/de-id focus; FHIR pass-through |
| **1upHealth** | Cloud lakehouse, FHIR R4 | SQL/BI-optimized, not LLM streaming |
| **Innovaccer** | Health intelligence cloud | Proprietary model; lock-in; not source-agnostic |
| **Rhapsody/Lyniate** | #1 integration engine | Rule-based mappings; not AI-optimized |
| **Metriport** | OSS API (FHIR/C-CDA/PDF) | Raw output; no semantic compression |
| **Zus Health** | "AI-first" data platform | FHIR R4 standard; no public LLM-optimization docs |

**Whitespace:** all stop at FHIR/HL7 normalization. None offer token-efficient serialization,
semantic compression (inlined context), structured+unstructured merging, or context caching.

## 3. AI-specific efforts

- **Clinical NLP/scribes:** Abridge ($5.3B val, Kaiser/Mayo/JHU; proprietary JSON output, narrow),
  Nabla ($70M Series C, agentic coding), Regard ($81M+, note-taking)
  ([Crunchbase](https://news.crunchbase.com/health-wellness-biotech/ai-doctor-note-taking-startup-abridge/);
  [Fierce](https://www.fiercehealthcare.com/ai-and-machine-learning/nabla-banks-70m-series-c)).
- **RAG-over-FHIR research:** FHIR-AgentBench (2,931 Qs on MIMIC-IV-FHIR; only 9% of medical RAG
  systems agentic) ([arxiv](https://arxiv.org/html/2509.19319v2)); FHIR-RAG-MEDS
  ([arxiv](https://arxiv.org/pdf/2509.07706)); FHIRPath-QA (391× token finding); LLMonFHIR (mobile
  PoC); FHIR-Former (flatten FHIR→tabular, lossy).
- **MEDS** (Medical Event Data Standard): minimal `(subject, code, timestamp, value)`; ML-portable
  (CSV/Parquet); MEDS-Tab, MEDS-OWL, MEDS-DEV ecosystem. Gap: loses hierarchy; no unstructured text
  ([MEDS](https://medical-event-data-standard.github.io/)).

## 4. Cross-industry analogs

| Approach | Strength | Gap |
|---|---|---|
| **MCP** (Anthropic→Linux Fdn) | Standardized agent↔tool/data interface; 97M+ SDK downloads | Protocol, not data format; returns raw payloads |
| **llms.txt** | Markdown navigation descriptor for LLMs | Descriptive, not prescriptive serialization |
| **OpenAI Structured Outputs / JSON Schema** | Constrains LLM *output* to schema | Output only, not ingestion |
| **Schema.org medical types** | Semantic web markup (JSON-LD) | Too shallow for EHR; public-web focus |
| **Data contracts** | Producer/consumer schema + SLAs | Upstream of LLM; no AI-optimized serialization |
| **Unstructured.io / LlamaIndex** | Document parsing, chunking, embeddings | Produces embeddings, not a standard format; lossy chunking; no native FHIR |

## 5. The key gap (whitespace)

No production AI-native, source-agnostic ingestion format exists. Problems unsolved:
1. Structural verbosity (60–80% low-signal metadata; 391× token blow-up).
2. Semantic fragmentation (nesting + cross-resource references).
3. Unstructured-text isolation (notes buried, handled separately).
4. Vocabulary indirection (codes as pointers, not inline labels).
5. Source-agnostic inefficiency (normalize to FHIR, re-denormalize per use case).
6. Privacy/consent complexity (limited semantic modeling).
7. Multimodal fragmentation (DICOM/HL7/FHIR/PDF in separate standards).

**Why it doesn't exist yet:** FHIR mandate inertia; conflicting stakeholders (EHR vendors prefer
lock-in, payers prefer X12, researchers prefer SQL/OMOP, AI cos lack leverage); no AI-ingestion
efficiency benchmark; privacy complexity; multimodal representation disagreements; LLM clinical
capability only recently proven.

## Selected sources
- [FHIRPath-QA](https://arxiv.org/pdf/2602.23479) · [FHIR-AgentBench](https://arxiv.org/html/2509.19319v2)
  · [FHIR-RAG-MEDS](https://arxiv.org/pdf/2509.07706) · [MEDS](https://medical-event-data-standard.github.io/)
- [HL7 AI Office](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/)
  · [Keragon vendors](https://www.keragon.com/blog/healthcare-interoperability-vendors)
- [MCP – Google Cloud](https://cloud.google.com/discover/what-is-model-context-protocol)
  · [MCP – Wikipedia](https://en.wikipedia.org/wiki/Model_Context_Protocol)
- [llms.txt](https://llmstxtgenerator.org/llmstxt-documentation) · [OpenAI Structured Outputs](https://openai.com/index/introducing-structured-outputs-in-the-api/)
  · [Schema.org meddocs](https://schema.org/docs/meddocs.html)
