# Zone & Conduit Register - AVSA (IEC 62443-3-2, ZCR 3)

This register partitions the System under Consideration into **zones** (asset groupings with shared security requirements) and **conduits** (the communication paths between them), and assigns each a **target security level (SL-T)** per the [methodology](methodology-62443-3-2.md). Zones are grouped by consequence and function, not by current network topology.

> **Important:** this is the **target** zone model, the zoning AVSA's function requires. AVSA's current network is **flat and unsegmented** (finding AO-2), so none of these boundaries are enforced today. The [risk register](risk-register.md) scores the current-state risk that results. The estimated current **SL-A** and the **gap** columns quantify that security debt, which the [remediation roadmap](../03-maturity-assessment/) then closes.

The full register is also provided as a formatted spreadsheet: [`avsa-risk-assessment.xlsx`](avsa-risk-assessment.xlsx) (Zone & Conduit Register tab).

---

## Zones

| Zone | Name | Purdue | Key assets | Consequence driver | SL-T | Est. SL-A | Gap |
|---|---|:---:|---|---|:---:|:---:|:---:|
| **Z1** | Enterprise | L4–5 | Corporate IT: billing, ERP, GIS, email, reporting server | External to SuC, modelled as a **threat source** (ransomware origin, Nov 2025) | n/a | n/a | n/a |
| **Z2** | OT DMZ | L3.5 | *Target:* replica historian, MFA jump host, patch/AV staging. **Does not exist today (AO-1).** | Keystone IT/OT boundary; its absence is the single largest structural risk | **3** | 0 | **3** |
| **Z3** | Site Operations | L3 | Process historian, engineering workstation, field engineering laptop | Historian bridges IT/OT (AO-1); EWS holds PLC-programming authority (AO-5) | **2** | 1 | 1 |
| **Z4** | Supervisory Control | L2 | Redundant SCADA pair, 4 operator workstations, 6 panel HMIs (2× Windows 7) | Central control leverage over every downstream process; loss of view/control | **3** | 1 | **2** |
| **Z5** | Process Control | L1 | PA-1/2/3/5 PLCs (20 × S7-1500) + Level 0 field devices | Disruption of intake, coagulation, filtration, or high-lift; supply continuity | **2** | 1 | 1 |
| **Z6** | Chlorine Dosing Control | L1 | PA-4 PLCs (4 × S7-1500) + Level 0 field devices | **Public health**: dosing manipulation affects water safety. Highest-consequence zone. Hardwired safety layer is separate and out-of-band | **3** | 1 | **2** |
| **Z7** | Distribution Telemetry | L1 (remote) | 13 × RTU/S7-1200 at 9 pumping stations + 4 reservoirs | Remote/unmanned, cellular-connected; reservoir re-dosing RTUs carry a water-quality consequence | **2** *(reservoir re-dosing sites: 3)* | 0–1 | 2–3 |

**Zoning rationale worth highlighting:** Z6 is separated from Z5 despite both sitting at Purdue Level 1. They are split because their **consequences differ**, chlorine dosing carries a public-health impact that intake or filtration disruption does not, so Z6 warrants a higher SL-T. Grouping assets by consequence rather than by network location is the core discipline of 62443 zoning.

## Conduits

| Conduit | Between | Purpose | Current state | SL-T |
|---|---|---|---|:---:|
| **C1** | Z1 Enterprise ↔ Z2 OT DMZ | The IT/OT boundary; all cross-domain traffic | **No DMZ exists; historian's 2nd NIC bypasses the firewall entirely (AO-1)** | **3** |
| **C2** | Z2 OT DMZ ↔ Z3 Site Operations | Brokered historian replication, staged patches, jump-host access | Absent (no DMZ to broker through) | **3** |
| **C3** | Z3 Site Operations ↔ Z4 Supervisory | Historian data collection, engineering access | Same flat segment; unsegmented (AO-2) | 2 |
| **C4** | Z4 Supervisory ↔ Z5 Process Control | SCADA/HMI supervision and control (Profinet / OPC UA) | Flat; no conduit enforcement | 2 |
| **C5** | Z4 Supervisory ↔ Z6 Chlorine Dosing | Dosing supervision and setpoint commands | Flat; no enforced separation around the highest-consequence zone | **3** |
| **C6** | Z7 Telemetry ↔ plant (4G cellular VPN) | Remote monitoring/control of distribution sites | VPN encrypted, but **terminates directly inside the flat plant LAN (AO-2)** | **3** |
| **C7** | Vendor remote access (Nortec) ↔ plant | SCADA support and PLC logic maintenance | **Shared VPN account, standing (always-on) access, no MFA (AO-3)** | **3** |

## Summary: where the debt concentrates

The largest SL-T-to-SL-A gaps are **Z2 (gap 3)**, the entirely absent OT DMZ, and the conduits crossing the IT/OT boundary (**C1, C2, C6, C7**, all target SL-T 3 against a current state near 0). This is consistent across the whole assessment: AVSA's dominant risk is **structural**, missing segmentation and brokering, rather than a collection of individual weak assets. That conclusion sets the priority order for remediation: build the boundary first, harden the endpoints second.

---

*Fictional scenario, no real client data. Part of the AVSA OT/ICS security assessment case study.*
