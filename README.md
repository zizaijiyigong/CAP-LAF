# CAP-LAF: Context-Aware Physics-Constrained LLM Agents for Distribution Network Operation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Paper Under Review](https://img.shields.io/badge/Paper-Under%20Review-orange.svg)]()

This repository contains the official open-source artifact release for the research paper:

> **"Context-Aware Physics-Constrained LLM Agents for Distribution Network Operation via Semantic-to-Algebraic Projection"**

---

## 📢 Repository Status & Open-Source Disclosure

### 1. Peer Review Stage Availability
This manuscript is currently **under peer review**. In compliance with academic evaluation standards, this repository provides open-source access to the core methodology artifacts:
- **Agent Prompts (`prompts/`)**: The exact prompt architecture and operational rules governing the cognitive reasoning layer.
- **Representative Execution Logs (`logs/`)**: Complete, multi-turn agent decision traces (trajectories) covering representative operational scenarios evaluated in Section IV of the paper.

The full-scale software platform—including complete physics simulation sandboxes, relational CIM database schemas, ChromaDB vector databases, and deterministic power flow tool suites—will be fully released upon official publication of the paper.

### 2. Language Notice (Bilingual Support)
The real-world distribution network benchmark experiments in this paper were conducted on an operational 10-kV distribution network in China using authentic Chinese dispatch orders, weather warnings, and customer service profiles. To ensure full transparency and accessibility for international reviewers and researchers:
- **Original Chinese Files**: The native prompt designs and raw execution logs are retained (`*.txt`).
- **English Translations**: Complete, authoritative English translations of both system prompts (`*_en.txt`) and case execution traces (`*_en.txt`) are provided with identical round-by-round trajectory correspondence.


---

## 📂 Repository Structure

```
CAP-LAF/
├── LICENSE                        <- MIT License
├── README.md                      <- Main repository documentation (this file)
│
├── prompts/                       <- Agent prompt designs & operational specifications
│   ├── README.md                  <- Detailed guide to prompt design & TISM interface
│   ├── reasoner_system.txt        <- Reasoner Agent system prompt (Chinese native)
│   └── reasoner_system_en.txt     <- Reasoner Agent system prompt (English translated)
│
└── logs/                          <- Representative experimental execution traces
    ├── README.md                  <- Case study overview & experimental analysis
    └── llm_calls/
        ├── outputs_case1_A.txt    <- Case 1 Scenario A: Soft social priority (Chinese)
        ├── outputs_case1_A_en.txt <- Case 1 Scenario A (English translation)
        ├── outputs_case1_B.txt    <- Case 1 Scenario B: Ventilator life-safety override (Chinese)
        ├── outputs_case1_B_en.txt <- Case 1 Scenario B (English translation)
        ├── outputs_case2.txt      <- Case 2: Geo-spatial exclusion zone isolation (Chinese)
        └── outputs_case2_en.txt   <- Case 2 (English translation)
```

---

## 🧪 Case Studies in the Provided Logs

The logs in `logs/llm_calls/` demonstrate the exact reasoning and tool execution traces for the representative cases analyzed in Section IV of the paper:

| Case | Log Files | UCC Type | Operational Core & Findings |
|------|-----------|----------|-----------------------------|
| **Case 1 (Scenario A)** | `outputs_case1_A[_en].txt` | Type I (Dynamic Priority) | **Implicit Social Cost Quantification**: Under a single Mobile Emergency Generator (MEG) constraint, SACP identifies customer Yuanda-01 as a "high-sensitivity" user and assigns a soft load-shedding penalty, guiding the solver to prioritize Yuanda-01 over Yuanda-02 to mitigate reputation risk. |
| **Case 1 (Scenario B)** | `outputs_case1_B[_en].txt` | Type I (Dynamic Priority) | **Critical Life-Safety Override**: Upon receiving an updated work order indicating a ventilator-dependent resident in Yuanda-02, SACP binds Node 46 as a mandatory must-run hard constraint. The solver immediately executes a decision reversal, re-routing the MEG from Yuanda-01 to Yuanda-02. |
| **Case 2** | `outputs_case2[_en].txt` | Type III (Geo-spatial) | **Geo-Spatial Accessibility Reasoning**: A road collapse fault triggers a 500m traffic control zone. Traditional solvers select electrically optimal sectionalizers SW1-3 and SW1-4, which lie 126m–134m inside the hazard zone. SACP excludes these switches and autonomously expands the isolation boundary upstream to SW1-2 (630.4m away), producing a physically accessible and executable plan. |


---

## 🔍 How to Read the Trajectory Logs

Each log file records the multi-turn interaction between the Agent Layer and the Tool/Sandbox Layers:
- **`=== Round N ===`**: Denotes the sequential OODA iteration.
- **`thought`**: The internal chain-of-thought of the agent, declaring its current OODA phase (`Observe`, `Orient`, `Decide`, or `Act`) and operational rationale.
- **`memory_commit`**: Structured updates directly committed into TISM, including:
  - `constraints_update`: SACP projected constraints with lifecycle tracking (`identified` $	o$ `pending_injection` $	o$ `injected`).
  - `workflow_update`: Bounded stage management and pending actions.
  - `key_findings`: Persistent cross-layer facts verified by domain tools.
- **`action`**: Standardized JSON tool calls invoked by the agent (e.g., `sandbox_load_feeder`, `calculate_geo_distance`, `algo_find_path`, `verify_action_plan`).
- **Final Round**: The **Reasoner Agent** synthesizes the final executable decision in strict JSON format, featuring `conclusion`, `evidence_ids` for complete traceability, and step-by-step `scenarios` operations.


---

## ⚖️ License

This repository is licensed under the [MIT License](LICENSE).
