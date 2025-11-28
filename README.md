# FHIR-Solid-adapter
Abstract

European biobanks hold life-saving data but cannot share it effectively due to fragmented legacy systems and GDPR complexities. Patients possess the right to data portability but lack the technical tools to exercise it, creating a "trust deficit" that blocks voluntary donation.

FHIR-Solid Bridge solves this by building an open-source semantic adapter that connects institutional HL7 FHIR servers with decentralized Solid Pods. The adapter transforms patients from passive subjects into active data stewards: it automatically converts clinical data into interoperable RDF formats (using MIABIS and SNOMED CT ontologies) and stores it in the user's personal Pod. Access is governed by machine-readable dynamic consent (Data Privacy Vocabulary).

The result is a privacy-preserving Digital Commons infrastructure that unlocks Europe's biomedical silos for secondary use without centralizing sensitive data. This project serves as the foundational TRL-5 pilot for Trust4Life—a future Data Altruism Organisation incentivizing voluntary blood and organ donation in the EU.

Architecture

TECHNICAL ARCHITECTURE SPECIFICATION (v2.1)

Scope of Work (The "Adapter")

The project focuses on delivering the **Core Ingestion & Consent Layer**:

**Ingestion:** Unidirectional extraction of clinical data from Hospital LIMS via HL7 FHIR R4 API.

**Transformation (The Adapter):** Validates and maps FHIR codes to MIABIS 2.0 (Biobanking) RDF triples using SHACL shapes.

**Governance (The Gatekeeper):** Writes data to the User's Pod only if a valid DPV Consent Receipt exists.

**Federation Readiness:** We will deliver the *Architectural Specification* for a future Aggregator Node, designing the protocol for k-anonymity queries without full implementation in this phase.
