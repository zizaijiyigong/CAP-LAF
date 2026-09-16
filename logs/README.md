# Experimental Execution Trajectories (Case Logs)

This directory contains execution trajectory logs from the experimental validation in **Section IV (Case Studies)** of the paper:
> *"Context-Aware Physics-Constrained LLM Agents for Distribution Network Operation via Semantic-to-Algebraic Projection"*

---

## Directory Overview

```
logs/
├── README.md                  <- This guide
└── llm_calls/
    ├── outputs_case1_A.txt    <- Case 1 Scenario A: Implicit social cost quantification (Chinese)
    ├── outputs_case1_A_en.txt <- Case 1 Scenario A (English translation)
    ├── outputs_case1_B.txt    <- Case 1 Scenario B: Critical life-safety override (Chinese)
    ├── outputs_case1_B_en.txt <- Case 1 Scenario B (English translation)
    ├── outputs_case2.txt      <- Case 2: Geo-spatial exclusion zone isolation (Chinese)
    └── outputs_case2_en.txt   <- Case 2 (English translation)
```

---

## Experimental Setup

- **Testbed Network**: Grounded in an authentic 10-kV distribution network in China comprising **4 primary feeders**, **102 physical nodes** (including RMUs and busbars), **55 discrete switches**, and **54 distribution loads**.
- **Data Foundation**: Relational Common Information Model (CIM/IEC 61970–301) stored in MySQL; unstructured textual operational records (maintenance tickets, customer profiles, weather alerts) vectorized in ChromaDB.
- **Underlying LLM**: DeepSeek-V3.2 API, temperature set to 0.2 to enforce deterministic reasoning.

---

## Case Study Summary & Analytical Breakdown

### 1. Case 1: User-Profile Driven Dynamic Resource Allocation (Type I UCCs)
- **Feeder & Location**: Feeder 2 ('Dongshuang-1'), Node 44 ('Kangshun Branch Construction Point').
- **Base Disturbance**: Planned maintenance requires a 2-hour outage. Only **one Mobile Emergency Generator (MEG)** is available, capable of sustaining only one residential district.
- **Scenario A (`outputs_case1_A[_en].txt`)**:
  - *Context*: Customer profile query reveals user 'Yuanda-01' (Node 45) is a 【High-Sensitivity / Dispute-Prone】 user.
  - *SACP Projection*: Mapped to a soft constraint (load-shedding penalty $W_{prio}$).
  - *Decision*: Solver prioritizes Yuanda-01 for MEG hookup to mitigate social reputation risk.
- **Scenario B (`outputs_case1_B[_en].txt`)**:
  - *Context Shift*: Latest customer update reports a resident in 'Yuanda-02' (Node 46) is actively dependent on a medical ventilator.
  - *SACP Projection*: Mapped to a mandatory must-stay-energized hard constraint ($x_{i,t} = 1$).
  - *Decision Reversal*: The life-safety hard constraint strictly overrides the soft reputation constraint. The MEG is autonomously re-routed from Yuanda-01 to Yuanda-02.

### 2. Case 2: Geo-Spatial Accessibility under Physical Disruptions (Type III UCCs)
- **Feeder & Location**: Feeder 1 ('Dongnan 1'), Node 10 ('Ronghua Branch No.1').
- **Base Disturbance**: Cable severed by road collapse on Friday noon.
- **Contextual Restriction**: Municipal traffic control enforces a **500-meter No-Entry Zone** around the fault center. Field crews cannot enter the zone for manual switching operations.
- **Conventional Solver Failure**: Standard solvers select sectionalizers SW1-4 (switch_92_374, 126.4m) and SW1-3 (switch_92_381, 134.4m) because they minimize interrupted load (0.24 MW). However, both are trapped deep within the 500m exclusion perimeter, leading to field execution failure.
- **CAP-LAF Solution (`outputs_case2[_en].txt`)**:
  1. *GIS Calculation*: In the Orient stage, `calculate_geo_distance` identifies all switches within the 500m radius and injects action prohibitions ($C_{act}$).
  2. *Upstream Perimeter Expansion*: The solver searches farther upstream outside the exclusion zone, selecting SW1-2 (switch_92_380, 630.4m away).
  3. *Load Transfer*: Successfully transfers healthy downstream loads via tie switch switch_0_406 (Dongnan Xinnan RMU), while safely isolating the hazard area.


---

## Log Interpretation Format

Each trajectory log follows the structured multi-turn OODA schema:

```json
=== Round N ===
{
  "thought": "Phase: [Observe|Orient|Decide|Act]. Rationale and cognitive deduction...",
  "memory_commit": {
    "constraints_update": [...],  // SACP constraint lifecycle updates in TISM
    "workflow_update": {...},     // Current phase & stage-bounded pending actions
    "key_findings": [...]        // Verified electrical and contextual facts
  },
  "action": [
    {
      "tool": "tool_name",
      "args": { ... }
    }
  ]
}
```

The concluding round features the **Reasoner Agent's** verified plan synthesis, providing explicit `evidence_ids` for complete audibility.
