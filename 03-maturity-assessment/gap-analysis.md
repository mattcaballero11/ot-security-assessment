# Gap Analysis - AVSA (C2M2 v2.1)

This document takes each C2M2 domain from the [maturity scorecard](maturity-scorecard.md), states what stands between AVSA's current MIL and its target MIL, and points to the [remediation roadmap](remediation-roadmap.md) items that close the gap. It is the bridge between "here is how mature AVSA is" and "here is what to do about it."

> **Fictional scenario.** All findings concern the fictional operator AVSA and use only public frameworks. No client data is present.

---

## Gaps at a glance

| Domain | Current | Target | Gap | What is missing to close it | Roadmap |
|---|:--:|:--:|:--:|---|---|
| Asset, Change & Config Mgmt | MIL1 | MIL2 | 1 | A maintained (not 2024) inventory with assigned ownership, plus change and configuration management for OT assets | RM-04, RM-05, RM-10 |
| Threat & Vulnerability Mgmt | MIL0 | MIL2 | 2 | A basic OT vulnerability process, use of threat intelligence, and compensating controls for the unpatchable Windows 7 HMIs | RM-12 |
| Risk Management | MIL0 | MIL3 | 3 | A documented, owned, and policy-governed risk process that is reviewed on a cycle (this assessment is the starting point) | RM-10 |
| Identity & Access Mgmt | MIL0 | MIL3 | 3 | Uniquely attributable access (no shared accounts, no default credentials), MFA, least privilege, and periodic access review | RM-01, RM-02, RM-08 |
| Situational Awareness | MIL1 | MIL2 | 1 | Security logging and monitoring for OT, beyond the operational view the control room already provides | RM-14 |
| Event & Incident Response | MIL1 | MIL3 | 2 | An OT-specific incident response plan with roles, resourcing, regular exercises, and improvement | RM-03 |
| Third-Party Risk Mgmt | MIL0 | MIL2 | 2 | Security requirements in supplier contracts, a vendor questionnaire, and periodic review of Nortec | RM-11 |
| Workforce Management | MIL1 | MIL2 | 1 | Defined OT security roles and role-based OT security training | RM-13 |
| Cybersecurity Architecture | MIL0 | MIL3 | 3 | An OT DMZ, network segmentation into zones with enforced conduits, and governed architecture standards | RM-06, RM-07, RM-08, RM-09 |
| Cybersecurity Program Mgmt | MIL0 | MIL2 | 2 | A governed OT security program with sponsorship, scope, roles, and strategy | RM-10 |

## The three largest gaps in detail

These three domains each carry a full three-level gap and a MIL3 target, because each maps directly to AVSA's top risks from Phase 2. They are where most of the effort goes.

### Cybersecurity Architecture (MIL0 to MIL3)

This is the gap that matters most, because the flat network is the finding that makes every other risk worse. Reaching **MIL1** means basic segmentation and secure-design practices are actually performed. **MIL2** means the architecture is documented and includes an OT DMZ and defined zones. **MIL3** means architecture standards are governed by policy, reviewed, and improved as the environment changes. The roadmap closes this in the structural horizon: build the DMZ (RM-06), segment the flat LAN into the Phase 2 zones (RM-07), fix where external access lands (RM-08), and isolate the dosing zone (RM-09). Closing this single domain collapses the attack paths behind R-01, R-02, R-05, R-12, R-13, and R-14.

### Identity & Access Management (MIL0 to MIL3)

AVSA scores MIL0 here for a specific reason: access is not uniquely attributable. A shared vendor VPN account and default RTU credentials mean you cannot tell who did what, which fails the most basic identity practice. **MIL1** is reached by making every access attributable (RM-01 removes default credentials, RM-02 replaces the shared account with named identities). **MIL2** adds MFA, least privilege, and proper provisioning. **MIL3** adds policy governance and periodic access recertification, with vendor access brokered through a jump host (RM-08). This domain closes the paths behind R-03 and R-07.

### Risk Management (MIL0 to MIL3)

Before this engagement, risk was handled as part of plant maintenance, with no structured process, which is MIL0. This assessment itself is the first MIL1 activity: risk is now being identified and rated. **MIL2** requires that the risk process be documented, owned, and resourced rather than a one-off. **MIL3** requires it to be governed by policy and reviewed on a regular cycle, which is also what NIS2 expects of an essential entity. This is delivered as part of standing up the OT security program (RM-10), and it is what keeps the other nine domains from sliding back once they improve.

## How the gaps close over time

The gaps do not close evenly, and they should not. The quick wins lift Access and Response fast and cheaply. The structural horizon is where Architecture moves, and it is the most expensive work. The program horizon lifts Risk, Program, Third-Parties, Threat, Workforce, and Situational Awareness together, because those domains depend on having a governed program to sit inside. The sequencing and the expected maturity trajectory are set out in the [remediation roadmap](remediation-roadmap.md).

---

*Fictional scenario. No real client data. Part of the AVSA OT/ICS security assessment case study.*
