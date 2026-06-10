# Unified Compliance Ontology Framework (UCOF)

## Architecture & Component Design Document

- Version: 0.1 — Initial Design
- Author: Eduardo Luz <eduardo@eduardo-luz.com>
- Status: Draft for Review


---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Design Principles](#2-design-principles)
3. [Core Ontology Model](#3-core-ontology-model)
   - 3.1 Namespace Architecture
   - 3.2 Universal Primitives
   - 3.3 Norm-Specific Extensions
4. [Norm Coverage Map](#4-norm-coverage-map)

---

## 1. Executive Summary

The **Unified Compliance Ontology Framework (UCOF)** is a platform that allows organizations to plan, implement, oversee, and maintain compliance with multiple ISO and NIST standards simultaneously, using a single shared model of compliance concepts rather than managing each standard in isolation.

The core insight is that compliance standards — despite their distinct domains — share a common structural grammar: they define **requirements**, organize them into **controls**, expect organizations to demonstrate **evidence**, and mandate periodic **reviews**. UCOF makes this grammar explicit as a formal ontology, then maps each standard's specific vocabulary onto it. The result is a system where:

- Controls from ISO 9001 and ISO 27001 that address the same underlying concern (e.g., document control, internal audit) can be merged into a single implementation task rather than duplicated.
- Organizations working toward multiple certifications see a unified view of their compliance posture.
- New standards can be added by loading a norm definition file — without modifying the platform's core.

The initial norm set is ISO 9001, ISO 27001, ISO 42001 (with ISO 22989 as its AI terminology foundation), and NIST AI RMF. The architecture is designed to also accommodate ISO 45001, ISO 14001, and any future additions.

---

## 2. Design Principles

| Principle | Implication |
|---|---|
| **Ontology-first** | All compliance concepts are modeled formally before any UI or workflow is designed. The ontology is the source of truth. |
| **Norm-agnostic core** | The engine does not know about ISO or NIST specifically. Norms are loaded as data, not code. |
| **Single-instance controls** | Where two norms require the same practice, one control implementation satisfies both. Cross-references are explicit. |
| **Evidence primacy** | No control is considered implemented without linked, dated evidence artifacts. Claims without evidence are planned states, not achieved ones. |
| **Auditability over convenience** | Every state change — a control marked complete, evidence updated, risk re-assessed — is logged with author, timestamp, and rationale. |
| **Progressive disclosure** | Users see complexity proportional to their role. An implementer sees tasks; an auditor sees evidence chains; an executive sees posture scores. |
| **Open extensibility** | The framework publishes a documented schema for norm definition files so third parties (consultants, standards bodies) can author and distribute norm packs. |

---

## 3. Core Ontology Model

### 3.1 Namespace Architecture

The ontology is organized across eight namespaces. The first three are universal and norm-independent. The remaining five are populated by norm loaders.

```
ucof:core          — fundamental entity types shared across all norms
ucof:org           — organizational context (entities, roles, assets, processes)
ucof:evidence      — evidence artifacts, records, and audit trails
ucof:norm          — norm metadata (standard, version, clauses, status)
ucof:control       — control statements, implementation states, and mappings
ucof:risk          — risk items, assessments, treatment plans
ucof:workflow      — certification lifecycle phases, tasks, milestones
ucof:observe       — monitoring events, indicators, review triggers
```

### 3.2 Universal Primitives

These are the atomic types from which all compliance concepts are composed. Every norm-specific concept maps to one or more of these.

#### `Requirement`
A normative statement that an organization **shall** or **should** satisfy. Requirements are the smallest addressable unit of a standard.

Key attributes:
- `requirementId` — globally unique identifier (e.g., `iso27001:2022/A.8.8`)
- `normRef` — pointer to source norm and clause
- `obligationLevel` — `{SHALL, SHOULD, MAY}`
- `requirementSource` — `{Normative, Customer, Regulatory, Contractual, InternalPolicy}`
- `requirementText` — canonical text from the standard
- `applicabilityCondition` — optional; describes when this requirement applies
- `tags` — free-form semantic labels for cross-norm mapping

#### `Control`
An organizational measure — a policy, process, technical safeguard, or behavioral practice — that addresses one or more requirements.

Key attributes:
- `controlId` — organization-scoped identifier
- `title`, `description`
- `controlType` — `{Policy, Process, Technical, Physical, People}`
- `implementationState` — `{NotStarted, InProgress, Implemented, Verified, Deprecated}`
- `satisfies` — set of Requirement references (can span multiple norms)
- `owner` — role or person responsible
- `reviewCycle` — frequency of review

#### `ControlMapping`
An explicit link between a Control and the Requirements it satisfies, potentially across multiple norms. This is what enables cross-norm consolidation.

Key attributes:
- `control` — reference
- `requirement` — reference
- `mappingType` — `{FullySatisfies, PartiallySatisfies, Contributes}`
- `rationale` — justification for the mapping
- `validatedBy` — auditor or assessor who confirmed the mapping

#### `Evidence`
A record, document, log, screenshot, report, or other artifact that demonstrates a control is implemented and operating.

Key attributes:
- `evidenceId`
- `title`, `description`
- `evidenceType` — `{Document, Log, TestResult, Interview, Observation, Certification}`
- `linkedControl` — reference
- `collectedDate`, `expiryDate`
- `collectedBy`
- `storageRef` — pointer to artifact in vault
- `verificationState` — `{Unverified, Accepted, Rejected}`

#### `RiskItem`
A potential event or condition that could cause non-conformance or harm. Risk management is central to ISO 27001, 42001, 45001, and NIST AI RMF.

Key attributes:
- `riskId`
- `category` — `{Information Security, AI/ML, OHS, Environmental, Quality, Privacy}`
- `description`
- `likelihood` — `{VeryLow, Low, Medium, High, VeryHigh}`
- `impact` — same scale
- `inherentRiskScore` — computed
- `treatmentOption` — `{Mitigate, Accept, Transfer, Avoid}`
- `linkedControl` — treatment measure (a Control)
- `residualRiskScore` — post-treatment assessment

#### `RiskAndOpportunityItem`
A planning record used to model risk-based thinking as a combined evaluation of downside risk and upside opportunity.

Key attributes:
- `raoId`
- `description`
- `linkedRisk` — reference to a `RiskItem`
- `linkedOpportunity` — reference to an `OpportunityItem`
- `planningContext` — process, objective, project, or change context

#### `OpportunityItem`
A potential condition or action that can improve outcomes, resilience, quality, or efficiency.

Key attributes:
- `opportunityId`
- `description`
- `expectedBenefit`
- `linkedControl`

#### `Asset`
Anything of value to the organization that a norm requires to be identified and protected.

Key attributes:
- `assetId`
- `assetType` — `{DataAsset, AIModel, Process, Person, PhysicalAsset, System, Supplier}`
- `owner`
- `classification` — `{Public, Internal, Confidential, Restricted}`
- `linkedRisks` — associated RiskItems

#### `OrganizationalEntity`
A legal entity, business unit, or scope boundary within which compliance applies.

Key attributes:
- `entityId`
- `entityType` — `{LegalEntity, BusinessUnit, Site, Department, Product}`
- `parentEntity`
- `applicableNorms` — norms in scope for this entity

#### `ReviewCycle`
A scheduled or triggered event requiring reassessment of controls, risks, or the entire management system.

Key attributes:
- `cycleId`
- `cycleType` — `{InternalAudit, ManagementReview, SurveillanceAudit, Recertification, IncidentTriggered}`
- `scheduledDate`, `completedDate`
- `scope` — controls, norms, or asset groups in review
- `findings` — list of NonConformance or ObservationItem
- `linkedWorkflow` — reference to Workflow

#### `NonConformance`
A deviation from a requirement, discovered during audit, incident, or self-assessment.

Key attributes:
- `ncId`
- `severity` — `{Minor, Major, Critical}`
- `discoveredDate`, `discoveredBy`
- `linkedRequirement`
- `linkedControl`
- `rootCause`
- `correctiveAction` — reference to a Task or Control update
- `closureDate`, `closedBy`

### 3.3 Norm-Specific Extensions

Each norm loaded into the system can extend these primitives with additional attributes or introduce subtype hierarchies. Extensions are additive — they never modify universal primitive definitions.

Example: `StatementOfApplicability` (a structured list of selected controls with inclusion/exclusion justification) is modeled as a specialization of `ControlSelectionDocument`, itself a type of Evidence. Although commonly used for ISO 27001, the pattern is reusable by other norms that require control applicability/selection registers.

Example: ISO 42001 introduces `AISystemRecord`, `AIImpactAssessment` (modeled under `RiskAssessment`, and usable via a `RiskAssessment` alias in AI contexts), and `BiasRisk` (a subtype of `RiskItem` with AI-specific attributes). These are registered by the ISO 42001 norm pack.

---

## 4. Norm Coverage Map

This section maps each target standard to the ontology primitives it primarily exercises. This also serves as the basis for generating norm-specific views in the UI.

### ISO 9001:2015 — Quality Management Systems

| ISO 9001 Concept | Ontology Primitive |
|---|---|
| Quality policy | Control (type: Policy) |
| Quality objectives | OrganizationalGoal (extension of Asset) |
| Customer requirements | Requirement (`requirementSource = Customer`) |
| Process approach | Control (type: Process) + Asset (type: Process) |
| Risk-based thinking | RiskAndOpportunityItem (links to RiskItem and/or OpportunityItem) |
| Documented information | Evidence (type: Document) |
| Internal audit | ReviewCycle (type: InternalAudit) |
| Management review | ReviewCycle (type: ManagementReview) |
| Nonconformity & corrective action | NonConformance |
| Continual improvement | ImprovementItem (extension) |

### ISO 27001:2022 — Information Security Management Systems

| ISO 27001 Concept | Ontology Primitive |
|---|---|
| Information assets | Asset (type: DataAsset, System) |
| Information security risk | RiskItem (category: Information Security) |
| Statement of Applicability (or equivalent control applicability register) | StatementOfApplicability (specialization of ControlSelectionDocument) |
| Annex A controls | Control (sourced from ISO 27001 norm pack) |
| Security incident | IncidentTrigger (optional escalation to NonConformance + ReviewCycle) |
| ISMS scope | OrganizationalEntity |
| Interested parties | Stakeholder (extension of OrganizationalEntity) |

### ISO 42001:2023 — AI Management Systems (+ ISO 22989 Terminology)

ISO 22989 is the AI vocabulary standard. Its terms are loaded as a **terminology extension** that enriches the ontology's labels and definitions for AI-related primitives.

| ISO 42001 Concept | Ontology Primitive |
|---|---|
| AI system | Asset (type: AIModel) |
| AI system record | AISystemRecord (Evidence extension) |
| AI risk assessment | RiskItem (category: AI/ML) |
| AI impact assessment | RiskAssessment (alias: AIImpactAssessment) |
| AI policy | Control (type: Policy, tagged: AI) |
| Responsible AI objectives | OrganizationalGoal extension |
| AI literacy | TrainingRecord + CompetenceAssessmentRecord + AwarenessCommunicationRecord (Evidence extensions) |
| Bias and fairness controls | BiasRisk + Control |
| Transparency documentation | Evidence (type: Document, tagged: AI-Transparency) |
| Human oversight controls | Control (type: Process, tagged: AI-Oversight) |


### NIST AI RMF (AI Risk Management Framework)

NIST AI RMF is function-based rather than clause-based, organized around four core functions: **Govern, Map, Measure, Manage**.

| NIST AI RMF Function/Category | Ontology Primitive |
|---|---|
| GOVERN — AI risk governance policies | Control (type: Policy) |
| GOVERN — Roles and responsibilities | Role + OrganizationalEntity |
| MAP — AI context and categorization | Asset (type: AIModel) + AISystemRecord |
| MAP — Risk identification | RiskItem (category: AI/ML) |
| MEASURE — Risk analysis metrics | RiskIndicator (Observe namespace) |
| MEASURE — Testing & evaluation | Evidence (type: TestResult) |
| MANAGE — Risk treatment and response handling | RiskItem.treatmentOption + Control + Task + MonitoringEvent |
| MANAGE — Incident escalation (optional) | NonConformance + ReviewCycle |

**Cross-mapping with ISO 42001:** NIST AI RMF and ISO 42001 are highly convergent. The system should auto-generate a cross-mapping report showing which NIST subcategories are addressed by which ISO 42001 clauses, so organizations pursuing both do not implement duplicate controls.

### OpportunityItem Examples

- `ProcessOptimizationOpportunity` — streamline a process to reduce defects and cycle time.
- `AutomationOpportunity` — automate repetitive controls to improve consistency and evidence quality.
- `SupplierDiversificationOpportunity` — reduce dependency risk by onboarding alternate qualified suppliers.
- `CustomerExperienceOpportunity` — improve customer satisfaction through better feedback handling and service reliability.
