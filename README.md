# FHIR-Solid Bridge

**Open-Source Semantic Middleware for Biobank Data Portability**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status: Pre-Alpha](https://img.shields.io/badge/Status-Pre--Alpha-orange.svg)]()
[![TRL: 2→5](https://img.shields.io/badge/TRL-2→5-blue.svg)]()

---

## 🎯 The Problem

European biobanks hold life-saving data but cannot share it effectively. Patients have the legal right to data portability (GDPR Art. 20), but lack the technical tools to exercise it. This creates a "trust deficit" that blocks voluntary donation and medical research.

## 💡 The Solution

FHIR-Solid Bridge is a semantic adapter that connects institutional **HL7 FHIR** servers with decentralized **Solid Pods**, enabling:

- **Patient sovereignty:** Donors control their own health data
- **Semantic interoperability:** Automatic mapping to MIABIS 2.0 biobanking ontology
- **Dynamic consent:** Machine-readable permissions via Data Privacy Vocabulary (DPV)
- **Privacy by design:** Data stays in personal Pods, never centralized

## 🏗️ Architecture

```
┌─────────────────┐      ┌──────────────────┐      ┌─────────────────┐
│  Hospital LIMS  │      │  FHIR-Solid      │      │  User's Solid   │
│  (FHIR R4 API)  │ ──── │  Adapter         │ ──── │  Pod            │
└─────────────────┘      │                  │      └─────────────────┘
                         │  • Validation    │             │
                         │  • SHACL Mapping │             │
                         │  • Consent Check │             ▼
                         └──────────────────┘      ┌─────────────────┐
                                                   │  Research       │
                                                   │  (Federated     │
                                                   │   Queries)      │
                                                   └─────────────────┘
```

### Data Flow (Phase 1 — Unidirectional)

1. **Ingestion:** Extract clinical data from FHIR R4 API
2. **Transformation:** Validate and map to MIABIS 2.0 RDF triples
3. **Governance:** Write to Pod only if valid DPV consent exists

## 🛠️ Technology Stack

| Component | Technology |
|-----------|------------|
| Runtime | Node.js v20 LTS, TypeScript |
| FHIR Client | `fhir.js` |
| Solid Client | `@inrupt/solid-client` |
| RDF Processing | `jsonld`, `rdf-ext` |
| Validation | SHACL shapes |
| Auth | Solid-OIDC (WebID-OIDC) |
| Deployment | Docker, Kubernetes |

## 📋 Roadmap

### Phase 1 (Current — Q4 2025 - Q4 2026)
- [ ] Core FHIR→RDF adapter
- [ ] MIABIS 2.0 ontology mapping
- [ ] SHACL validation engine
- [ ] Consent Dashboard (React)
- [ ] TRL 5 pilot validation

### Phase 2 (Future)
- [ ] Aggregator Node for federated queries
- [ ] k-anonymity enforcement
- [ ] BBMRI-ERIC Directory integration

## 📦 Installation

> ⚠️ **Pre-Alpha:** This project is in early development. Code will be available following NGI Zero funding confirmation.

```bash
# Coming soon
npm install fhir-solid-adapter
```

## 🔗 Standards & References

- [W3C Solid Protocol](https://solidproject.org/TR/protocol)
- [HL7 FHIR R4](https://hl7.org/fhir/R4/)
- [MIABIS 2.0](https://github.com/MIABIS/miabis/wiki)
- [Data Privacy Vocabulary](https://w3c.github.io/dpv/)
- [SNOMED CT](https://www.snomed.org/)

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

**Areas where we need help:**
- FHIR resource mapping expertise
- Solid Pod integration testing
- MIABIS ontology validation
- Accessibility testing (WCAG 2.1 AA)

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

## 🏛️ About

**FHIR-Solid Bridge** is developed by [Trustvestor Ltd.](https://trustvestor.com) (Bulgaria) as foundational infrastructure for **Trust4Life** — a future Data Altruism Organisation for voluntary health donation in the EU.

### Team

- **Andon Lucien** — Project Lead, Legal & Compliance
- **Stoyan Mishev** — Technical Lead, Systems Architecture

## 🙏 Acknowledgments

This project is submitted to the **NGI Zero Commons Fund**, part of the European Commission's Next Generation Internet initiative.

---

*Building the Digital Commons for European Health Data*
