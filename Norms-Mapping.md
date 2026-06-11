
# Norm Coverage Map

This section maps each target standard to the ontology primitives it primarily exercises. This also serves as the basis for generating norm-specific views.

## ISO 9001:2015 — Quality Management Systems

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

## ISO 27001:2022 — Information Security Management Systems

| ISO 27001 Concept | Ontology Primitive |
|---|---|
| Information assets | Asset (type: DataAsset, System) |
| Information security risk | RiskItem (category: Information Security) |
| Statement of Applicability (or equivalent control applicability register) | StatementOfApplicability (specialization of ControlSelectionDocument) |
| Annex A controls | Control (sourced from ISO 27001 norm pack) |
| Security incident | IncidentTrigger (optional escalation to NonConformance + ReviewCycle) |
| ISMS scope | OrganizationalEntity |
| Interested parties | Stakeholder (extension of OrganizationalEntity) |

## ISO 42001:2023 — AI Management Systems (+ ISO 22989 Terminology)

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


## NIST AI RMF (AI Risk Management Framework)

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

## OpportunityItem Examples

- `ProcessOptimizationOpportunity` — streamline a process to reduce defects and cycle time.
- `AutomationOpportunity` — automate repetitive controls to improve consistency and evidence quality.
- `SupplierDiversificationOpportunity` — reduce dependency risk by onboarding alternate qualified suppliers.
- `CustomerExperienceOpportunity` — improve customer satisfaction through better feedback handling and service reliability.
