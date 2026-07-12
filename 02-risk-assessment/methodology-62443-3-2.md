# Risk Assessment Methodology - IEC 62443-3-2

This document defines how the AVSA OT risk assessment was conducted: the workflow, the scope, the scoring scales, and how target security levels were derived. It is deliberately explicit, because a risk assessment is only as credible as the rules it applies consistently. The outputs produced under this methodology are the [zone & conduit register](zone-conduit-register.md) and the [risk register](risk-register.md).

> **Fictional scenario.** All findings concern the fictional operator AVSA and use only public frameworks. No client data is present.

---

## 1. Approach

The assessment follows the workflow of **IEC 62443-3-2**, *Security risk assessment for system design*, which structures OT risk work as a sequence of Zone and Conduit Requirements (ZCR):

| Step | Activity | Where it appears |
|---|---|---|
| ZCR 1 | Identify the System under Consideration (SuC) | §2 below |
| ZCR 2 | Initial high-level risk assessment | §3 below |
| ZCR 3 | Partition the SuC into zones and conduits | [zone & conduit register](zone-conduit-register.md) |
| ZCR 4 | Detailed risk assessment per zone/conduit | [risk register](risk-register.md) |
| ZCR 5 | Document requirements, assumptions, constraints | §7 below + registers |
| ZCR 6 | Asset owner approval | Out of scope (no real asset owner) |

Supporting references: **NIST SP 800-82r3** (OT security guidance), **MITRE ATT&CK for ICS** (threat technique taxonomy, referenced per risk), and the AVSA [reference architecture](../01-architecture/reference-architecture.md), which supplies the asset and data-flow baseline.

## 2. System under Consideration (ZCR 1)

**In scope:** the ETAP Río Almora plant control system, process areas PA-1 (intake), PA-2 (coagulation), PA-3 (filtration), PA-4 (chlorine dosing), PA-5 (clear well/high-lift), together with the PA-6 distribution telemetry network (13 remote RTU/PLC units), the supervisory layer (SCADA, operator workstations, HMIs), site operations (historian, engineering workstation, field laptop), and the connections that cross the plant boundary: the corporate firewall link, the historian replication path, the vendor remote-access path, and the cellular telemetry path.

**Out of scope:** the EDAR wastewater facility (separate operating contract, separate management and network, so effectively a different SuC) and corporate IT systems. Corporate IT is treated as an **external zone and a threat source**, not assessed on its own merits.

Explicit exclusions are a feature of a sound assessment, not a gap: a risk rating is only valid for the boundary it was measured against, and claiming to have assessed systems outside the engagement's authority would be dishonest. The Nov 2025 corporate ransomware incident is precisely why corporate IT is modelled as a live threat vector into the SuC.

## 3. Initial risk assessment (ZCR 2)

A high-level pass established worst-case unmitigated exposure before partitioning. Three characteristics dominate AVSA's inherent risk:

- **A public-health consequence ceiling.** As a drinking-water supplier, the worst credible outcome is unsafe water reaching ~152,000 consumers. This anchors the top of the consequence scale and drives the highest target security levels toward the treatment-critical zones.
- **A flat, unsegmented control network.** With no interior zoning, a single foothold anywhere reaches everywhere, so inherent likelihood is high across most threat scenarios regardless of entry point.
- **Multiple externally reachable paths** (vendor VPN, cellular telemetry, dual-homed historian) converging on that flat network, each an independent way in.

These characteristics are what the zone/conduit partitioning and the detailed risk register then quantify.

## 4. Consequence scale

Consequence is rated 1–5 on the **highest applicable dimension** among public health, service continuity, personnel safety, environmental impact, and regulatory/financial exposure. Rating the worst credible outcome (not the average) is deliberate: OT risk is about tail events.

| Rating | Descriptor | Water-utility anchor |
|---|---|---|
| 1 | Negligible | No measurable effect on water quality, supply, or safety; trivial operational noise. |
| 2 | Minor | Short, localized operational disruption; no customer supply loss; no health or safety impact. |
| 3 | Moderate | Localized supply interruption (some customers, hours) or degraded monitoring/control, recoverable within a shift; minor regulatory notification. |
| 4 | Major | Wider or extended supply interruption, or loss of control/view over key processes; a water-quality deviation requiring intervention but caught before harm; NIS2-reportable; a personnel safety near-miss. |
| 5 | Severe | Unsafe water reaches consumers, **or** a chlorine-gas release harms personnel, **or** prolonged population-wide loss of supply; major regulatory sanction; potential loss of life. |

## 5. Likelihood scale

Likelihood is rated 1–5 as a function of **threat capability and motivation, exposure, and the effectiveness of existing controls**. It is assessed twice: **inherent** (as if no compensating factors existed) and **residual** (accounting for the controls AVSA actually has today).

| Rating | Descriptor |
|---|---|
| 1 | Rare: requires exceptional resources or access; strongly mitigated; no known targeting. |
| 2 | Unlikely: requires significant capability or insider access; meaningful barriers present. |
| 3 | Possible: achievable by a moderately capable actor; exposure exists; controls partial or weak. |
| 4 | Likely: achievable with known techniques by a common actor; clear exposure; little or no effective control. |
| 5 | Almost certain: trivial to exploit; actively targeted in the wild; exposed with no barrier. |

## 6. Risk rating

Risk = **Likelihood × Consequence**, on a 1–25 scale, banded as:

| Band | Score | Meaning |
|---|---|---|
| 🟩 Low | 1–5 | Tolerable; monitor. |
| 🟨 Medium | 6–10 | Treat within normal planning cycles. |
| 🟧 High | 11–15 | Prioritized treatment required. |
| 🟥 Critical | 16–25 | Urgent; treat ahead of routine work. |

The [risk register](risk-register.md) records both the inherent score (before existing controls) and the residual score (after). For AVSA the two are usually close, because the current environment has few *effective* controls to reduce likelihood, which is itself the core argument for the remediation roadmap developed in [`03-maturity-assessment/`](../03-maturity-assessment/).

## 7. Target Security Level (SL-T) derivation

IEC 62443 grades security 1–4 by the capability of the adversary a zone must withstand: SL 1 casual, SL 2 simple/low-resource, SL 3 sophisticated with **ICS-specific** skills and moderate resources, SL 4 sophisticated with extended (nation-state) resources. AVSA assigns **SL-T** (the target) using this rule, informed primarily by consequence:

| Condition | SL-T |
|---|---|
| Consequence ≤ 2 and no meaningful targeting | 1 |
| Consequence 3 | 2 |
| Consequence 4–5, or a high-value critical-infrastructure zone under active real-world targeting | 3 |
| Catastrophic consequence facing a determined nation-state | 4 (see note) |

**On SL 4.** Water utilities are targeted by ICS-capable actors, which justifies SL-T 3 for the treatment-critical zones. SL-T 4, defending against a well-resourced, highly motivated nation-state with extended resources, is judged impractical and disproportionate for a municipal operator of AVSA's size and budget. SL 3 is treated as the pragmatic ceiling, with the honest caveat that a determined nation-state actor may exceed any level a utility this size can realistically sustain. Naming that limit is more useful to the client than pretending SL 4 is achievable.

**SL-T as a vector.** Underneath the single number, an SL-T is a vector across the seven foundational requirements: IAC (identification/authentication), UC (use control), SI (system integrity), DC (data confidentiality), RDF (restricted data flow), TRE (timely response), RA (resource availability). The register lists one overall SL-T per zone for readability; two worked vectors illustrate the finer targeting:

- **Z6 (Chlorine Dosing Control)**, SL-T = { IAC 3, UC 3, SI 3, DC 1, RDF 3, TRE 2, RA 3 } → overall **3**. Strong identity, use control, integrity, segmentation, and availability, because unauthorized or tampered dosing commands are the threat that matters. **DC is only 1**: dosing setpoints are not confidential, and spending a critical-infrastructure security budget protecting their secrecy would be misallocated. Matching effort to what each requirement is actually worth is the point of the vector.
- **Z2 (OT DMZ)**, SL-T = { IAC 3, UC 3, SI 3, DC 2, RDF 3, TRE 3, RA 2 } → overall **3**. As the keystone boundary between IT and OT, it is graded high on the requirements that govern who crosses and what is allowed through, with slightly higher TRE (it is the natural monitoring chokepoint) and lower RA (a brief DMZ outage degrades reporting, not live control).

## 8. Assumptions and constraints

- The assessment is architecture- and interview-based (a paper assessment); no active scanning or testing was performed against the environment. Ratings reflect documented design and stated practice.
- Asset counts and the network baseline are taken from the [reference architecture](../01-architecture/reference-architecture.md) and are held consistent across all deliverables.
- Threat likelihood reflects the public threat landscape for water/ICS at the time of assessment (mid-2026), including the sustained targeting of internet-reachable water-sector control systems.
- Current-state SL-A values are **estimates** derived from the architecture and stated controls, expressed to quantify the gap to SL-T; they are not the product of a formal SL-A verification.

---

*Fictional scenario, no real client data. Part of the AVSA OT/ICS security assessment case study.*
