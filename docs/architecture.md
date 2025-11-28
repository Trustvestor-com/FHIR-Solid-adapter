# FHIR-Solid Bridge — Technical Architecture

**Version:** 2.1  
**Status:** Design Phase (TRL 2)  
**Last Updated:** November 2025

---

## 1. Overview

FHIR-Solid Bridge operates as a **Semantic ETL Middleware** with the following pipeline:

```
Hospital LIMS ──► FHIR R4 API ──► Adapter ──► Solid Pod
                                    │
                                    ▼
                              DPV Consent Check
```

## 2. Core Components

### 2.1 Ingestion Layer

**Purpose:** Extract clinical data from institutional systems.

- **Protocol:** HL7 FHIR R4 (JSON)
- **Supported Resources:** `Patient`, `Specimen`, `Condition`, `Observation`
- **Authentication:** OAuth 2.0 / SMART on FHIR

### 2.2 Transformation Engine

**Purpose:** Convert FHIR JSON to MIABIS-compliant RDF.

#### Example Transformation

**Input (FHIR JSON):**
```json
{
  "resourceType": "Specimen",
  "id": "sample-001",
  "type": {
    "coding": [{
      "system": "http://snomed.info/sct",
      "code": "119297000",
      "display": "Blood specimen"
    }]
  },
  "collection": {
    "collectedDateTime": "2025-03-15"
  }
}
```

**Output (RDF Turtle):**
```turtle
@prefix miabis: <https://github.com/MIABIS/miabis/wiki/> .
@prefix snomed: <http://snomed.info/sct/> .
@prefix sprec: <https://www.isber.org/sprec/> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

<urn:uuid:sample-001> a miabis:Sample ;
    miabis:materialType snomed:119297000 ;
    miabis:collectionDate "2025-03-15"^^xsd:date ;
    sprec:storageTemperature "-80C" .
```

#### SHACL Validation

All transformations are validated against SHACL shapes to ensure MIABIS compliance:

```turtle
miabis:SampleShape a sh:NodeShape ;
    sh:targetClass miabis:Sample ;
    sh:property [
        sh:path miabis:materialType ;
        sh:minCount 1 ;
        sh:nodeKind sh:IRI ;
    ] .
```

### 2.3 Governance Layer (The Gatekeeper)

**Purpose:** Enforce consent before writing to Pod.

#### Consent Flow

1. Adapter queries user's Pod for DPV Consent Receipt
2. If valid consent exists → Write data
3. If revoked/missing → Deny access, log attempt

#### DPV Consent Structure

```turtle
@prefix dpv: <https://w3c.github.io/dpv/dpv/#> .

<urn:consent:user-123> a dpv:Consent ;
    dpv:hasDataSubject <urn:user:123> ;
    dpv:hasPersonalData miabis:Sample ;
    dpv:hasPurpose dpv:AcademicResearch ;
    dpv:hasExpiry "2027-12-31"^^xsd:date ;
    dpv:hasStatus dpv:ConsentGiven .
```

### 2.4 Consent Dashboard

**Purpose:** User-friendly interface for managing permissions.

- **Framework:** React.js
- **Accessibility:** WCAG 2.1 AA principles
- **Features:**
  - Simple Allow/Deny toggles
  - Visual timeline of access grants
  - One-click revocation

## 3. Security Architecture

### 3.1 Encryption

| Layer | Method |
|-------|--------|
| In Transit | TLS 1.3 (mandatory) |
| At Rest | Storage encryption (Pod provider) |
| Client-Side | Optional RSA+AES hybrid for genomic data |

### 3.2 Authentication

- **Protocol:** Solid-OIDC (WebID-OIDC)
- **Admin Access:** Hardware keys (YubiKey) required

### 3.3 Resilience

- Circuit Breaker pattern for external API calls
- Graceful degradation if FHIR server unavailable
- Immutable audit logs for all operations

## 4. Deployment

### 4.1 Phase 1 (Pilot)

```yaml
# docker-compose.yml (simplified)
services:
  adapter:
    image: trustvestor/fhir-solid-adapter:latest
    environment:
      - FHIR_SERVER_URL=https://fhir.example.org
      - SOLID_IDP=https://solidcommunity.net
    ports:
      - "3000:3000"
```

### 4.2 Production (Future)

- Kubernetes deployment on Hetzner Cloud (EU)
- Horizontal scaling for multiple biobank connections
- Centralized logging (ELK stack)

## 5. Phase 2: Federation (Design Only)

> **Note:** Full implementation deferred to Phase 2. This section describes the architectural specification.

### 5.1 Aggregator Node Concept

```
Researcher ──► Aggregator ──► Pod 1
                          ──► Pod 2
                          ──► Pod N
                               │
                               ▼
                         k-Anonymity Filter
                               │
                               ▼
                         Aggregated Results
```

### 5.2 Privacy Guarantees

- **k-anonymity:** Results suppressed if k < 5
- **Query restrictions:** No individual-level data retrieval
- **Audit trail:** All queries logged with researcher ID

---

## References

1. [HL7 FHIR R4 Specification](https://hl7.org/fhir/R4/)
2. [W3C Solid Protocol](https://solidproject.org/TR/protocol)
3. [MIABIS 2.0 Core](https://github.com/MIABIS/miabis/wiki)
4. [Data Privacy Vocabulary](https://w3c.github.io/dpv/)
5. [SHACL Specification](https://www.w3.org/TR/shacl/)
