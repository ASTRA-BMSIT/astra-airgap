# ASTRA AIRGAP
### Secure Data Transfer & Sanitization Framework for Air-Gapped Defence Networks

**ASTRA AIRGAP** is an offline-first cybersecurity framework designed to inspect, analyze, sanitize, and securely transfer files between isolated network environments. It introduces a controlled security checkpoint between source and destination networks, ensuring that incoming data is evaluated against security policies before it is authorized for release.

Inspired by the iDEX/Navy challenge **“Development of an Advanced Open-Source Framework Sanitisation Tool Facilitating Secure Transfer of Data Between Multiple Air-Gapped Networks,”** ASTRA AIRGAP aims to demonstrate how layered security controls can reduce the risk of transferring malicious or unsafe content into protected environments.

> **Core principle:** No file should cross a security boundary without inspection, policy evaluation, and an explicit release decision.

---

## Table of Contents

- [The Problem](#the-problem)
- [Our Approach](#our-approach)
- [Key Features](#key-features)
- [Transfer Workflow](#transfer-workflow)
- [Security Architecture](#security-architecture)
- [Technology Stack](#technology-stack)
- [Prototype Scope](#prototype-scope)
- [Security Principles](#security-principles)
- [Planned Development Milestones](#planned-development-milestones)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Validation and Testing](#validation-and-testing)
- [Limitations](#limitations)
- [Project Context](#project-context)

---

## The Problem

Air-gapped networks are isolated from external networks to reduce their exposure to cyber threats. However, operational requirements often necessitate the transfer of documents, reports, software packages, configuration files, and other data between separate security domains.

These transfers introduce a critical security challenge: **how can useful data be moved between isolated networks without blindly trusting the incoming files?**

Traditional file-transfer mechanisms do not inherently guarantee that transferred content is safe. Files may contain malicious payloads, embedded active content, suspicious archive structures, or other security risks.

ASTRA AIRGAP addresses this challenge by introducing a controlled transfer pipeline that combines file inspection, local security analysis, sanitization, policy enforcement, and auditable release decisions.

## Our Approach

ASTRA AIRGAP treats file transfer as a security-controlled process rather than a simple copy operation.

The framework is designed around five core capabilities:

1. **Inspect** incoming files using file-type validation, metadata extraction, hashing, structural analysis, and local security tools.
2. **Analyze** inspection results to identify suspicious characteristics and determine an explainable risk assessment.
3. **Sanitize** supported file formats by removing or neutralizing unsafe content where technically appropriate.
4. **Enforce** destination-specific security policies to approve, block, or quarantine transfer requests.
5. **Audit** every transfer decision with traceable records of the source artifact, processed artifact, destination, findings, and outcome.

The initial prototype will simulate multiple isolated network zones and demonstrate the complete workflow in a local environment without depending on cloud services.

## Key Features

### 1. Offline-First Security Analysis
- Run core inspection, policy evaluation, and audit functions locally.
- Avoid mandatory cloud-based malware analysis or external API dependencies.
- Support operation in an environment without continuous internet connectivity.

### 2. Multi-Layer File Inspection
- Identify actual file formats instead of trusting file extensions alone.
- Calculate cryptographic hashes such as SHA-256.
- Extract basic metadata and inspect supported file structures.
- Detect suspicious active content, malformed structures, and archive-related risks.
- Integrate local malware scanning and configurable detection rules.

### 3. Risk Assessment and Explainability
- Aggregate findings from multiple inspection mechanisms.
- Produce an interpretable risk assessment.
- Display the evidence contributing to a security decision.
- Distinguish confirmed findings, suspicious indicators, and inspection failures.

### 4. Safe File Sanitization
- Apply format-specific sanitization to supported file types.
- Preserve the original artifact without silently overwriting it.
- Validate sanitized outputs before they can be considered for release.
- Quarantine files when safe processing cannot be established.

### 5. Policy-Driven Transfer
- Define file-handling policies for different destination security zones.
- Support explicit allow, block, and quarantine outcomes.
- Restrict unsupported or prohibited file types according to policy.
- Prevent a risk score alone from overriding mandatory security restrictions.

### 6. Auditable Transfer History
- Record transfer identifiers, timestamps, source and destination zones, file hashes, findings, and final decisions.
- Maintain a distinction between original files and sanitized artifacts.
- Provide a reviewable history for investigation and accountability.
- Design the audit layer for future tamper-evidence and integrity verification.

### 7. Security Operations Dashboard
- Submit files for controlled inspection.
- View file metadata, detected risks, and sanitization results.
- Inspect the reasons behind approval, blocking, or quarantine.
- Track transfer status and review previous decisions.

---

## Transfer Workflow

ASTRA AIRGAP follows the workflow:

**File Intake → Identification → Inspection → Risk Analysis → Sanitization → Validation → Policy Evaluation → Authorization → Controlled Transfer → Audit**

Each stage must produce an explicit result before the transfer proceeds.

A simplified decision model is:

| Condition | Expected action |
|---|---|
| File passes required checks and satisfies destination policy | Eligible for approval and release |
| File contains supported, sanitizable active content | Sanitize, validate, and reassess |
| File violates mandatory destination policy | Block |
| File is suspicious or cannot be safely processed | Quarantine or block according to policy |
| Required inspection or validation fails | Fail closed; do not release |

The exact policy rules and sanitization capabilities will be defined and tested for each supported file format.

## Security Architecture

The proposed architecture consists of the following components.

### File Intake Layer
Accepts files through an approved input mechanism and creates a transfer record. Files are assigned unique identifiers and processed in a controlled workspace.

### Inspection Engine
Performs file identification, hashing, metadata extraction, structural checks, archive inspection, and local malware scanning.

### Risk Assessment Engine
Combines inspection findings into an explainable assessment. Optional local AI-based anomaly classification may supplement deterministic security checks.

### Sanitization Engine
Applies format-specific cleaning procedures to supported files, preserving the original and producing a separate candidate artifact.

### Validation and Policy Engine
Validates processed artifacts and evaluates destination-specific rules. This layer determines whether a transfer can proceed, must be quarantined, or must be blocked.

### Controlled Release Layer
Makes only authorized artifacts available to the destination environment. The implementation must prevent ordinary application workflows from bypassing required security checks.

### Audit and Storage Layer
Stores transfer metadata, security findings, hashes, processing status, policy decisions, and timestamps.

### Operator Interface
Provides visibility into the transfer lifecycle, security findings, policy decisions, and audit history.

---

## Technology Stack

| Component | Proposed technology |
|---|---|
| Frontend | React, Vite, TypeScript |
| Backend | Python, FastAPI |
| Database | SQLite initially; PostgreSQL if required |
| File integrity | SHA-256 and file metadata |
| Detection rules | YARA-compatible rules |
| Malware analysis | Locally installed scanning tools |
| File processing | Format-specific parsing and sanitization libraries |
| AI layer | Optional locally executed anomaly or risk classification |
| Deployment | Local development and offline-capable environment |

Technology choices may evolve during implementation. Core security controls must remain functional independently of the optional AI layer.

---

## Prototype Scope

The initial prototype will demonstrate a controlled transfer workflow across two or more simulated security zones.

### Required Demonstrations

**Scenario 1 — Clean Document**
- Submit a normal PDF.
- Inspect its format, metadata, and structure.
- Perform applicable security checks.
- Evaluate destination policy.
- Release the approved artifact and record the decision.

**Scenario 2 — Document with Active Content**
- Submit a controlled test document containing representative active content.
- Identify the relevant security indicators.
- Sanitize the document if supported, then validate and reassess it.
- Quarantine the document if safe sanitization cannot be established.

**Scenario 3 — Executable File**
- Submit an executable that is prohibited by destination policy.
- Identify the file type.
- Enforce the policy and block the transfer.
- Record the decision in the audit history.

**Scenario 4 — Suspicious Archive**
- Submit a controlled archive containing suspicious or policy-violating content.
- Inspect its contents while enforcing archive size, nesting, and resource limits.
- Quarantine the archive for review when required.
- Prevent the archive from reaching the destination without authorization.

All demonstrations should use harmless test artifacts or controlled security test samples.

### Initial Scope Boundaries

The first version will focus on:
- A limited set of common file formats.
- Local inspection and policy enforcement.
- Explicit transfer states and release decisions.
- Basic sanitization of supported formats.
- Traceable transfer records.
- A functional operator dashboard.

Universal file sanitization, production-grade multi-domain isolation, and certification for operational defence environments are outside the initial prototype scope.

---

## Security Principles

ASTRA AIRGAP is designed around the following principles:

- **Fail closed:** Inspection, sanitization, or validation failures must never result in automatic release.
- **Least privilege:** Components should receive only the permissions required for their tasks.
- **Defense in depth:** Combine structural validation, local scanning, sanitization, and deterministic policy enforcement.
- **Preserve originals:** Never silently overwrite the original artifact.
- **Explicit authorization:** A risk score must not override mandatory security policy.
- **Traceability:** Every transfer must have a record of the decision and relevant evidence.
- **Offline capability:** Core security functions must not require cloud connectivity.
- **Bounded processing:** Apply file-size, archive-expansion, recursion, and processing-time limits.
- **Honest security claims:** Passing the inspection pipeline does not prove that a file is malware-free.

---

## Planned Development Milestones

| Milestone | Deliverable |
|---|---|
| M0 — Architecture | Threat model, trust boundaries, transfer lifecycle, and policy definitions |
| M1 — Inspection | File validation, hashing, metadata extraction, structural inspection, and local scanning |
| M2 — Sanitization | Supported format sanitizers, post-processing validation, and artifact separation |
| M3 — Policy and Release | Destination-specific policies, quarantine, blocking, and controlled release |
| M4 — Dashboard and Audit | Operator interface, transfer history, evidence display, and audit records |
| M5 — Evaluation | Security test corpus, measurable detection results, failure testing, and performance benchmarks |
| M6 — Optional AI | Local anomaly classification evaluated against a deterministic baseline |

Milestones are a proposed development plan; completion status will be updated as implementation progresses.

---

## Getting Started

### Prerequisites

- Python 3.11 or a compatible supported Python version.
- Node.js and npm.
- Git.
- A local malware-scanning tool and detection rules, configured according to the development environment.

### Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd <YOUR_REPOSITORY_DIRECTORY>
```

### Backend Setup

Once the backend implementation is available:

```bash
cd backend

python -m venv .venv
source .venv/bin/activate

pip install -r requirements.txt

uvicorn app.main:app --reload
```

The backend development server should be accessible at `http://127.0.0.1:8000`, and its API documentation should be available at `http://127.0.0.1:8000/docs` when enabled.

### Frontend Setup

Once the frontend implementation is available:

```bash
cd frontend

npm install
npm run dev
```

Use the local address printed by Vite to access the dashboard.

> **Note:** These commands assume the repository uses the directory layout and entry points shown above. Adjust them to match the actual implementation. The application is not considered offline-ready until its dependencies, scanning tools, rules, and assets have been installed and verified locally.

---

## Project Structure

Proposed repository layout:

```text
astra-airgap/
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── inspection/
│   │   ├── sanitization/
│   │   ├── policies/
│   │   ├── transfer/
│   │   ├── audit/
│   │   ├── models/
│   │   └── main.py
│   ├── tests/
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├
