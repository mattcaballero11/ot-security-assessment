# Risk Register - AVSA (IEC 62443-3-2, ZCR 4)

Detailed risk assessment for each zone and conduit, scored using the [methodology](methodology-62443-3-2.md). Each entry records the threat event, the source, the vulnerability it exploits (cross-referenced to the architectural observations **AO-1 to AO-5** from the [reference architecture](../01-architecture/reference-architecture.md)), a consequence rating, inherent and residual likelihood, the resulting risk bands, the zone's target security level, and the relevant **MITRE ATT&CK for ICS** technique.

The same content is available as a formatted, filterable spreadsheet: [`avsa-risk-assessment.xlsx`](avsa-risk-assessment.xlsx) (Risk Register tab). A visual of how the inherent risk is distributed is in [`diagrams/risk-matrix.svg`](diagrams/risk-matrix.svg).

> **Reading the scores.** **C** = consequence (1–5). **L(i)** and **L(r)** = inherent and residual likelihood. Risk = L × C, banded 🟩 Low 1–5 · 🟨 Med 6–10 · 🟧 High 11–15 · 🟥 Critical 16–25. Residual reflects the controls AVSA has *today*. Treatment to reach the target risk level is developed in the [remediation roadmap](../03-maturity-assessment/).

---

## Risk summary (all entries)

| ID | Threat event | Zone / Conduit | Vuln | C | L(i) | Inherent | L(r) | Residual | SL-T | ATT&CK for ICS |
|---|---|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|---|
| **R-01** | Corporate ransomware pivots into the plant via the dual-homed historian | C1 / Z3 | AO-1 | 4 | 4 | 🟥 16 | 3 | 🟧 12 | 3 | T0866 Exploitation of Remote Services; T0826 Loss of Availability |
| **R-02** | Compromise of the cellular VPN or a remote RTU enables lateral movement into the flat plant LAN | C6 / Z4 | AO-2 | 4 | 3 | 🟧 12 | 3 | 🟧 12 | 3 | T0822 External Remote Services; T0886 Remote Services |
| **R-03** | Theft or misuse of the shared Nortec VPN credential grants direct SCADA/PLC access | C7 / Z4 | AO-3 | 4 | 4 | 🟥 16 | 3 | 🟧 12 | 3 | T0822 External Remote Services; T0859 Valid Accounts |
| **R-04** | Manipulation of chlorine dosing setpoints degrades water quality reaching consumers | Z6 | Z6 + control access | 5 | 3 | 🟧 15 | 2 | 🟨 10 | 3 | T0836 Modify Parameter; T0831 Manipulation of Control |
| **R-05** | Exploitation of a Windows 7 HMI provides a foothold in the supervisory zone | Z4 | AO-4 | 3 | 4 | 🟧 12 | 3 | 🟨 9 | 3 | T0866 Exploitation of Remote Services |
| **R-06** | Phishing of the engineering-workstation user leads to an unauthorized PLC logic change | Z3 → Z5 | AO-5 | 4 | 3 | 🟧 12 | 3 | 🟧 12 | 2 | T0865 Spearphishing Attachment; T0843 Program Download; T0821 Modify Controller Tasking |
| **R-07** | Access to a remote RTU via default credentials manipulates pumping or reservoir re-dosing | Z7 | W-09 | 4 | 4 | 🟥 16 | 3 | 🟧 12 | 3 | T0812 Default Credentials |
| **R-08** | Unmanaged USB used for logic updates introduces malware into the OT environment | Z4 / Z5 | W-10 | 4 | 3 | 🟧 12 | 3 | 🟧 12 | 2 | T0847 Replication Through Removable Media; T0864 Transient Cyber Asset |
| **R-09** | Loss or failure of the field laptop holding the only PLC logic backups extends incident recovery | Z5 | W-07 | 3 | 3 | 🟨 9 | 3 | 🟨 9 | n/a (RA) | supports recovery from T0809 Data Destruction |
| **R-10** | Absence of an OT-specific incident response plan prolongs and worsens any incident | SuC-wide | W-08 | 4 | 4 | 🟥 16 | 3 | 🟧 12 | n/a (TRE) | degrades response to Inhibit Response Function techniques |
| **R-11** | Physical access to an unmanned distribution site enables local RTU compromise | Z7 | Z7 exposure / W-09 | 3 | 3 | 🟨 9 | 2 | 🟨 6 | 2 | T0864 Transient Cyber Asset; T0812 Default Credentials |
| **R-12** | The flat network lets a single foothold reach every OT asset (no restricted data flow) | SuC-wide | AO-2 | 4 | 4 | 🟥 16 | 3 | 🟧 12 | 3 (RDF) | T0886 Remote Services (systemic amplifier) |
| **R-13** | Ransomware or DoS on the SCADA pair causes loss of view and control for operators | Z4 | AO-1 / AO-2 | 4 | 3 | 🟧 12 | 3 | 🟧 12 | 3 | T0814 Denial of Service; T0815 Denial of View; T0813 Denial of Control |
| **R-14** | Alarm suppression or spoofed readings mask an ongoing process manipulation | Z6 / Z4 | Z6 integrity | 5 | 2 | 🟨 10 | 2 | 🟨 10 | 3 | T0878 Alarm Suppression; T0856 Spoof Reporting Message; T0838 Modify Alarm Settings |

**Distribution:** Five risks are inherently **Critical** (R-01, R-03, R-07, R-10, R-12). All of them trace back to the missing IT/OT boundary, the flat network, weak remote access, or the response gap. Once existing controls are counted, most settle at **High** rather than dropping to Low, because AVSA has few *effective* controls today to bring likelihood down. Closing that gap is the job of the remediation roadmap.

---

## Highest-priority risks in detail

### R-01 · IT-to-OT ransomware pivot via the dual-homed historian
**Inherent 🟥 16 → Residual 🟧 12 · SL-T 3 · exploits AO-1**

The process historian has two network interfaces, one on the plant control LAN and one on the corporate LAN, so it can feed production reporting to business users. That makes it a bridge across the IT/OT boundary that completely bypasses the perimeter firewall. In November 2025 a ransomware outbreak encrypted AVSA's corporate billing servers. Plant operations were spared, but the historian's second interface was a viable path for that same outbreak to reach the control network. Consequence is rated **4** (loss of control and view over the plant, extended supply disruption). Residual likelihood stays high because the only thing between a corporate compromise and the plant is a general-purpose IT firewall that the historian route sidesteps. Treatment: a Level 3.5 OT DMZ with a **replica** historian, so corporate systems never touch the plant-side machine.

### R-03 · Abuse of the shared vendor VPN credential
**Inherent 🟥 16 → Residual 🟧 12 · SL-T 3 · exploits AO-3**

Nortec's engineers reach the plant through a single shared VPN account with standing (always-on) access and no multi-factor authentication. A phished or leaked credential gives direct access to SCADA and the PLCs, and because the account is shared it cannot be tied to an individual or revoked for just one person. Consequence is **4** (direct control access). There is no effective control in place to reduce the likelihood, so inherent and residual are both severe. Treatment: per-engineer identities, MFA, and just-in-time access brokered through a DMZ jump host, with no standing tunnel into OT.

### R-04 · Chlorine dosing manipulation reaching consumers
**Inherent 🟧 15 → Residual 🟨 10 · SL-T 3 · Z6, the highest-consequence zone**

An attacker who reaches the PA-4 dosing controllers can change chlorine setpoints, either under-dosing (pathogen breakthrough) or over-dosing (chemical exposure). That carries a **public-health consequence rated 5**, the top of the scale. This is the scenario that makes AVSA critical infrastructure. The plant's hardwired chlorine-leak detection and scrubber interlocks are independent of the control network, which correctly bounds a gas-release event, but they do **not** detect or prevent a manipulated dosing setpoint. The scrubber watches for gas in the building, not for chlorine levels in the treated water. Residual likelihood drops to 2 because routine laboratory sampling and operator oversight would eventually catch an anomaly, but detection after the fact is not prevention. Treatment: isolate Z6 as its own zone behind an enforced conduit (C5), protect the integrity of dosing commands, and monitor setpoint changes.

### R-07 · Default credentials on remote RTUs
**Inherent 🟥 16 → Residual 🟧 12 · SL-T 3 · exploits W-09**

Several of the 13 distribution RTUs still have factory-default credentials. These sites are remote, unmanned, and reachable over the cellular network, so an attacker who finds one can log in with published defaults and manipulate local pumping or, at the four reservoirs, hypochlorite re-dosing. That is again a water-quality consequence, rated **4**. The same geographic spread that makes the sites hard to secure physically also makes weak credentials more costly. Treatment: remove default credentials, enforce per-device authentication, and bring the telemetry conduit (C6) under proper zoning so one compromised RTU cannot pivot into the plant.

### R-10 · No OT-specific incident response plan
**Inherent 🟥 16 → Residual 🟧 12 · maps to FR6 (Timely Response to Events)**

AVSA's incident response plan is IT-focused and does not cover the plant. There are no OT-specific playbooks, no defined roles for an OT incident, and no exercises. This does not cause an incident on its own, but it makes every other risk in this register worse by stretching out detection and recovery when something does happen. That is the difference between a contained event and a prolonged loss of supply, which is why the consequence is rated **4**. The 24/7 control room helps with detection but is not a substitute for a response plan. Treatment: an OT-specific IR plan with playbooks for the scenarios above, clear roles, and tabletop exercises. This is a fast, low-cost, high-impact quick win in the roadmap.

### R-12 · Flat network, one foothold reaches everything
**Inherent 🟥 16 → Residual 🟧 12 · maps to FR5 (Restricted Data Flow)**

This is the systemic risk that makes the others worse. Levels 2 and 3 share one flat segment, and both external VPN paths terminate inside it, so there is no lateral containment. Whether an attacker gets in through the historian (R-01), the vendor VPN (R-03), a remote RTU (R-02 or R-07), an HMI (R-05), or the engineering workstation (R-06), they can reach every other OT asset from there. It is rated as its own Critical risk because it multiplies the entire register. Treatment sits at the top of the roadmap: split the flat LAN into the zones defined here, enforced by conduits with explicit allow-rules. This is the single change that reduces overall risk the most.

---

## Treatment and next steps

Every risk above is carried into the [maturity assessment and remediation roadmap](../03-maturity-assessment/), where treatments are sequenced by impact and effort. The consistent signal from this register is that AVSA's biggest exposure is **structural**: the missing OT DMZ (R-01), the flat network (R-12), and uncontrolled remote access (R-03, R-07). So the roadmap builds the IT/OT boundary and segments the network before hardening individual assets. Fixing the structure closes several attack paths at once. Patching individual endpoints removes them one at a time.

---

*Fictional scenario. No real client data. Part of the AVSA OT/ICS security assessment case study.*
