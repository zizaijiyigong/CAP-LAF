# Agent Prompts Specification

This directory contains the system prompt definitions for the cognitive layer in **CAP-LAF** (Context-Aware Physics-Constrained LLM-Agent Framework).

## Files in this Directory

| File | Language | Target Agent | Description |
|------|----------|--------------|-------------|
| `reasoner_system.txt` | Chinese (Native) | Reasoner Agent | Native system prompt used in primary distribution grid benchmarks |
| `reasoner_system_en.txt` | English (Translated) | Reasoner Agent | Complete, authoritative English translation for international evaluation |

---

## The Reasoner Agent Architecture

In CAP-LAF, operational decision-making is decoupled between two specialized agents:
1. **Planning Agent (Planner)**: Executes the **State-Constrained Planning Loop (SCPL)** via bounded OODA (Observe, Orient, Decide, Act) stages. It translates natural-language Unstructured Contextual Constraints (UCCs) into algebraic constraints via **SACP**, coordinates deterministic tools, and submits candidate switching sequences to the physics simulation sandbox.
2. **Reasoner Agent (Reasoner)**: Consumes the verified operational records in **Topology-Indexed State Memory (TISM)**, validates evidence closure, ensures zero topological hallucinations, and synthesizes the final executable operational plan.

```
                   ┌───────────────────────────────────────┐
                   │        Planning Agent (SCPL)          │
                   │  Observe ──► Orient ──► Decide ──► Act│
                   └──────────────────┬────────────────────┘
                                      │ Writes candidates & commits
                                      ▼
                   ┌───────────────────────────────────────┐
                   │ Topology-Indexed State Memory (TISM)  │
                   │  M_t = < S_topo, C_ctxt, R_eval >     │
                   └──────────────────┬────────────────────┘
                                      │ Strictly Read-Only Context
                                      ▼
                   ┌───────────────────────────────────────┐
                   │        Reasoner Agent (Review)        │
                   │  1. Evidence Grounding Verification   │
                   │  2. Zero Topological Hallucination    │
                   │  3. Spatiotemporal Alignment          │
                   │  4. Deterministic Plan Synthesis      │
                   └──────────────────┬────────────────────┘
                                      │ Valid JSON
                                      ▼
                   ┌───────────────────────────────────────┐
                   │    Final Executable Operating Plan    │
                   └───────────────────────────────────────┘
```

---

## Structured Input Interface (TISM Mapping)

The Reasoner Agent receives structured inputs from TISM $M_t = \langle S^{topo}_t, C_{ctxt,t}, R^{eval}_t \rangle$:

- **$S^{topo}_t$ (Verified Physical Grid State)**:
  - `Key Entities`: Grounded mapping from natural language device names to Common Information Model (CIM) system keys (`syskey`).
  - `Topology Summary`: CIM-level topological connectivity, active power source buses, and boundary tie switches.
- **$C_{ctxt,t}$ (Contextual Constraint Set)**:
  - `Constraints Applied`: Structured constraint tuples $\langle E_{tgt}, T_{valid}, \tau_{cat}, \theta_{param} \rangle$ projected via SACP (e.g., action exclusions $C_{act}$, state locks $C_{state}$, risk penalties $W_{risk}$, load priorities $W_{prio}$).
- **$R^{eval}_t$ (Multi-Dimensional Simulation Feedback)**:
  - `Scenarios`: Sandbox evaluation metrics across scenarios (unserved loads, lost capacity in MW, voltage violations).
  - `Pathfinding Results`: Tool-computed feasible transfer paths, required switching operations (`operations_required`), and hop counts.
  - `Key Findings`: Accumulated cross-layer facts verified by domain tools.

---

## Output Schema

The Reasoner Agent must strictly output a valid JSON object matching this schema:

```json
{
  "conclusion": "Direct and concise answer to the operator query",
  "evidence_ids": ["List of device syskeys and source references for full traceability"],
  "confidence": 0.95,
  "need_simulation": false,
  "recommended_actions": [
    "Practical advice for field crew or dispatcher manual verification"
  ],
  "explanation": "In-depth electrical and contextual rationale for the decision",
  "scenarios": {
    "scenario_id": {
      "operations": ["Ordered list of switches/devices to operate"],
      "reason": "Topological and constraint justification for this scenario"
    }
  }
}
```
