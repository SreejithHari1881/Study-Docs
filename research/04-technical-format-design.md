# Research Data: Technical Format Design Principles

> Source research feeding `design.md` §7 (HealthLM) and §10 (implementation). Why current formats
> fail for LLMs, what "LLM-friendly" means, emerging best practices, and example sketches.

## 1. Why current formats fail for LLM ingestion

- **FHIR JSON/XML:** token inefficiency (40–60% overhead); deep nesting (coding arrays in category
  structures); reference-following overhead (`Patient/123` runtime resolution); provenance stored
  separately. Runtime canonical-reference resolution only addressed at FHIR Camp 2025.
- **HL7 v2:** real-time event format, flat, no hierarchy/versioning/governance metadata.
- **Raw PDFs/scans:** unstructured; need OCR+NLP; no semantics; non-deterministic lossy chunking.

## 2. What makes data "LLM-friendly"

- **Token efficiency:** compact representations (TOON merges YAML indentation + CSV tabular,
  30–60% reduction); eliminate redundant field names.
- **Markdown/structured-text:** HL7 AI-optimized Markdown bundles (`hl7.org/fhir/us/core/ai.zip`);
  prose + structured annotations reduce ambiguity.
- **Flat vs nested:** pure flattening = redundancy; pure nesting = parse penalty. Solution: **dual
  representations** (nested clinical model + flat/semi-flat AI view).
- **Self-describing context:** minimal metadata per element (units, code system, ranges); semantic
  layer maps local codes → SNOMED/LOINC/ICD with display text.
- **Embedded provenance & timestamps:** inline origin/transformation/quality; W3C PROV (+ GDPRov
  for consent); created/updated/source timestamps for temporal reasoning.
- **Codes WITH labels:** `SNOMED|80943009|Sprain of ankle`, not bare `80943009`. 57% of leading
  interop initiatives prioritize semantic harmonization (2025).
- **Chunk-ability for RAG:** hierarchical segmentation (sections → sub-chunks with overlaps);
  semantic + hierarchical chunking; deterministic for reproducible retrieval.
- **Deterministic ordering:** canonical section order (header → demographics → problems →
  medications → observations → assessments) for auditability.

## 3. Emerging best practices (2025–2026)

- **HL7 AI Office** (Jul 2025) + Markdown bundles.
- **Context engineering** (Karpathy, Jun 2025): "what does the agent know/see/remember at the
  moment of action" — embed guidelines, history, decision rules, data contracts into context.
- **Structured outputs / JSON Schema:** constrained decoding (vLLM, Outlines, LM Format Enforcer);
  JSONSchemaBench (10,000+ schemas); self-healing for malformed JSON.
- **RAG chunk design:** chunk-based / index-based / summarization-based; hierarchical for clinical
  notes; EHR-RAG bridges structured EHR + LLMs. Most RAG focuses on unstructured notes; structured
  EHR integration is the frontier.
- **Semantic layers & data contracts:** raw multimodal → curated semantic (embeddings, KGs);
  two-tier Medical Data Lakehouse; FHIR+SNOMED+LOINC+ICD (71% of countries use FHIR somewhere).
- **Provenance:** W3C PROV (entities/activities/agents); GDPRov for consent lifecycle; FAIR;
  blockchain/immutable audit logs.
- **MCP for data exposure:** healthcare MCP servers as unifying EHR↔AI layer with HIPAA-aligned
  scoped access; Medplum launched MCP beta + fhir-request tool.
- **AI Model Passport:** data/system traceability framework for transparent health AI.

## 4. Design principles for a new AI-native format

1. **Dual representation** — human Markdown/prose + machine-parseable structured annotations.
2. **Source-agnostic mapping** — FHIR/HL7v2/C-CDA/CDA → canonical IR (normalize terminology,
   flatten deep structures, embed provenance), emit multiple formats (compact JSON, TOON, Markdown).
3. **Versioning** — version in header; semver; migration guides; graceful degradation.
4. **PHI/governance metadata** — `is_phi`, `pii_fields`, `consent_level`, `retention_until`;
   automated masking/de-id.
5. **Deterministic & idempotent** — canonical ordering, sorted arrays → bit-identical hashes.
6. **Embeddable & composable** — micro-formats (inline provenance, inline codes) within larger docs.

## 5. Example sketches

### (a) Raw FHIR JSON — ~380 tokens, deep nesting, references
```json
{"resourceType":"Observation","status":"final",
 "code":{"coding":[{"system":"http://loinc.org","code":"55284-4","display":"Blood Pressure"}]},
 "subject":{"reference":"Patient/pt-123"},"effectiveDateTime":"2026-06-15T10:30:00Z",
 "component":[
  {"code":{"coding":[{"system":"http://loinc.org","code":"8480-6","display":"Systolic BP"}]},
   "valueQuantity":{"value":140,"unit":"mm[Hg]"}},
  {"code":{"coding":[{"system":"http://loinc.org","code":"8462-4","display":"Diastolic BP"}]},
   "valueQuantity":{"value":90,"unit":"mm[Hg]"}}]}
```

### (b) AI-native (Markdown + YAML) — ~180 tokens, human+machine readable
```markdown
---
format_version: "2025-Q2"
patient: { id: pt-123, name: "John Doe", dob: 1965-04-02, is_phi: true, pii_fields: [name] }
source: { system: EPIC, ts: 2026-06-15T10:30:00Z, facility_id: hosp-001 }
---
## Vital Signs [2026-06-15 10:30]
**Blood Pressure** (LOINC|55284-4)
- Systolic: 140 mm[Hg] (LOINC|8480-6) — [ELEVATED]
- Diastolic: 90 mm[Hg] (LOINC|8462-4) — [NORMAL]
```

### (c) TOON tabular — 40–60% token reduction on uniform arrays
```toon
Observations
code,display,value,unit,timestamp,status
LOINC|8480-6,Systolic BP,140,mm[Hg],2026-06-15T10:30:00Z,final
LOINC|8462-4,Diastolic BP,90,mm[Hg],2026-06-15T10:30:00Z,final
LOINC|2823-3,Heart Rate,72,/min,2026-06-15T10:30:00Z,final
```

## 6. Key implementation decisions

| Principle | Choice | Rationale |
|---|---|---|
| Primary format | Markdown + YAML header + embedded metadata | Human-readable, token-efficient, annotatable |
| Nested vs flat | Semi-flat with hierarchical sections | Balances clinical hierarchy + LLM parsing |
| Terminology | Codes + display text (SNOMED/LOINC/ICD) | Label anchoring improves reasoning |
| Provenance | W3C PROV in header + inline timestamps | Audit, FAIR, reproducibility |
| Chunking | Hierarchical (sections → length) | Respects structure; deterministic |
| Versioning | Semver in header + migration guides | Graceful evolution |
| PHI | Metadata tags + automated masking | Governance compliance |
| RAG | 2K–4K token chunks | Retrieval granularity vs context richness |
| MCP | Canonical format as MCP resource + scoped access | Safe agent integration |

## Selected sources
- [TOON / InfoQ](https://www.infoq.com/news/2025/11/toon-reduce-llm-cost-tokens/)
  · [FHIR DevDays 2025 LLM insights](https://fire.ly/blog/top-llm-insights-from-fhir-devdays-2025/)
- [HL7 AI Office](https://hl7news.hl7.org/2025/07/16/hl7-launches-ai-office-to-set-global-standards-for-healthcares-ai-revolution/)
  · [FHIR package mgmt / runtime resolution](https://www.health-samurai.io/articles/fhir-package-management)
- [RAG in healthcare review (MDPI)](https://www.mdpi.com/2673-2688/6/9/226)
  · [EHR-RAG](https://arxiv.org/pdf/2601.21340) · [Chunking strategies 2026](https://www.firecrawl.dev/blog/best-chunking-strategies-rag)
- [W3C PROV in healthcare](https://pubmed.ncbi.nlm.nih.gov/30147034/)
  · [AI Model Passport](https://arxiv.org/pdf/2506.22358)
- [MCP for healthcare](https://www.vouched.id/learn/blog/the-model-context-protocol-unlocking-trust-and-efficiency-for-ai-in-healthcare)
  · [JSONSchemaBench](https://arxiv.org/pdf/2501.10868)
