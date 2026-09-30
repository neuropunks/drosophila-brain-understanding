# Understanding the Drosophila brain: a systems report of the "Fly" project

**Monograph (overview). Version 0.1 — 29.09.2026.**
**Project author and researcher: Andrey A. Smarygin, Independent researcher, Tyumen, Russia.**
*Document prepared with the participation of the digital assistant Vivi (orchestration of ~100 research subagents, verification, synthesis).*

---

## 0.1. What this document is

This is a systems monograph of the "Fly" project — the first (at the time of writing) attempt at a
**complete functional understanding** of an entire animal brain in a spiking connectome model. Not
"yet another simulation", but an inventory: what the substrate can do, what it has been proven
incapable of doing, and why — with numbers, controls and reproducible code.

Two complete Drosophila connectomes run in a single engine: **MaleCNS v1.0**
(the complete CNS of the male: brain + ventral nerve cord; 166,700 neurons,
25.6M connections) and **FlyWire v783** (the female brain; 138,639 neurons, 15.1M connections).
Every result is verified by spiking canons, null controls and, where possible,
cross-connectome replication.

## 0.2. Two metrics of understanding (an honest frame)

The project reports two numbers rather than one:

| Metric | Value | Definition |
|---|---|---|
| **Epistemic completeness L0–L6** | **~100%** | Every question posed has a verdict with a causal mechanism: a mechanism OR a proven boundary. The mechanism backlog is empty |
| **Functional reproducibility L0–L6** | **91.4%** | How quantitatively the substrate reproduces the biology. The deficit = proven boundaries (7–8%) + data limits of MaleCNS (3–4%) + calibration (1–2%) |

Fundamentally: **a proven boundary is understanding, not ignorance.** We know
that the wall stands, exactly where, and why (for example: the spiking release of learned
information is fundamentally incompatible with LIF homeostasis — six mechanisms
refuted with pre-registrations). A boundary is a coordinate on the map that
saves years for those who come after.

L7 (language/tokens) is deliberately **outside the metric of understanding** — it is a separate track
of the future project, not a deficit of this one.

## 0.3. Structure of the monograph

| Chapter | Content | File |
|---|---|---|
| **L0. Substrate** | Two connectomes, three-class edge layer, CUDA core, verification canons | `ch1_L0_L1.md` |
| **L1. Physiology** | LIF and bistability, SFA types, PIR, STD, gap junctions, APL, T1 inset, oscillators | `ch1_L0_L1.md` |
| **L2. Sensors** | Vision (loom, Giant Fiber), olfaction (KC code), hearing, taste (boundary), minor channels | `ch2_L2_L3.md` |
| **L3. Working memory** | EPG ring, 6 s bump, cross-connectome replication, DNa02, CX arbitration | `ch2_L2_L3.md` |
| **L4. Learning** | E9d breakthrough, LTM consolidation, release block, PSP channel, paradigm P1–P4, composition (boundary) | `ch3_L4_L5.md` |
| **L5. States** | Hunger gate, circadian rhythm, state vector, modulators, peptide map | `ch3_L4_L5.md` |
| **L6. Behaviour** | Motor decoder, CPG, flybench validation, navigation (boundaries) | `ch4_L6_methods.md` |
| **Methodology** | Pre-registration discipline, wave pattern, pitfall lessons | `ch4_L6_methods.md` |
| **Conclusion** | Final map "can/cannot", frontier F1–F3, publication track | `ch5_conclusion.md` |

Each chapter is built identically: **mechanisms with numbers → proven boundaries
with causes → summary table "phenomenon → status → numbers → source"**.
All numbers are tied to the project's primary reports (`docs/*.md`, `DEVLOG.md`)
and are reproducible with the harnesses (`tools/*.py`).

## 0.4. How this project differs from everything done before

1. **The only reproducible specific learning** in a full spiking
   connectome network (E9d). All public community attempts are negatives;
   we found and proved the common root (attractor bistability) and bypassed it with
   physiology, without touching the learning rule.
2. **Cross-connectome replication** (MaleCNS ↔ v783) — no work in the niche
   has reproduced results on a second independent connectome.
3. **Proven boundaries as a product**: seven fundamental walls of learning
   with pre-registrations and clean controls (the "Limits" paper of the series).
4. **Methodology**: waves of research subagents + independent critics
   before blinding is lifted + bit-for-bit invariants + null controls (N10
   degree-preserving) — an engineering discipline raised to a scientific standard.
5. **An honest metric**: two scales of understanding instead of one loud number;
   all negatives published together with the positives.

## 0.5. Campaign statistics (13–29.09.2026)

| Indicator | Value |
|---|---|
| Research subagents | ~100 (waves, GPU + CPU) |
| Reproduced experiments from the literature | 31+ |
| Own pre-registered experiments | 40+ (series E1–E10, F1, paradigm P1–P4, transitive v1/V2, N-targets) |
| GPU runs | ~1,000+ (validated by spiking canons) |
| Connectomes | 2 (replication R1–R5) |
| External benchmark | flybench: core 5/5 on both connectomes |
| Publications | No. 1 NT audit (Zenodo, DOI 10.5281/zenodo.22975837) + series in preparation |
| Project documents | 100+ in `docs/`, journal DEVLOG (103 entries) |

## 0.6. How to read the statuses

| Marker | Meaning |
|---|---|
| ✅ mechanism | Reproducibly works, numbers and controls in the source |
| ❌ boundary (proven) | Shown with pre-registration and controls to NOT work; the cause is known |
| 🟡 partial | Works in a limited regime/range; the limits of the regime are known |
| 🔴 data boundary | Cannot be determined from the MaleCNS/v783 dataset (external data required) |

---

*The monograph is a living document. Version 0.1 fixes the state of understanding as of
29.09.2026: the mechanism backlog L0–L6 is empty, the frontier is theory (F1/F2) and external
data (F3). The English version is being prepared for publication.*


---

# Understanding the Drosophila brain. Chapter L0 — Substrate (data and engine) and Chapter L1 — Physiology

**Project:** "Fly" (~/Рабочий стол/Муха/). **Date of compilation:** 29.09.2026.
**Purpose:** overview chapters of the master monograph "Understanding the Drosophila brain" (in Russian).
**Rule:** every number is provided with a source reference (file §section / DEVLOG §); disputed formulations were checked against primary sources — see "Verification of markers" at the end of the chapter.
**Cross-cutting law of the project:** *plasticity is strong, the representational carrier is the bottleneck; the working output of the substrate = the PSP channel* (`docs/understanding_final_map.md` §10, `docs/address_release_synthesis.md` §3).

Two metrics of understanding (decision by Andrey, 29.09.2026; L7 excluded from the metric of understanding):

| Metric | Value | Meaning |
|---|---|---|
| **Epistemic completeness L0–L6** | ≈100% | every question posed has a verdict with a causal mechanism (a mechanism OR a proven boundary); the mechanism backlog is empty |
| **Functional reproducibility L0–L6** | 91.4% | mean; the deficit = proven boundaries 7–8% + data limits of MaleCNS 3–4% + calibration 1–2% |

Source of metrics: `docs/understanding_final_map.md` (update 29.09.2026).

---

# Chapter L0. Substrate: data and engine

## 0.1. Two connectomes

The project works with two independent Drosophila connectomes — both are used in parallel: FlyWire v783 (the mature engine, records, validation) and MaleCNS v1.0 (the sensorimotor loop, behavioural evolution, "Fly 2.0").

| | **FlyWire v783** | **MaleCNS v1.0** |
|---|---|---|
| What | Female brain (FAFB) | Complete CNS of the male: brain + optic lobes + ventral nerve cord (VNC) |
| Neurons | **138,639** | **166,700** (official set = `superclass.notna()`; 166,483 with synapses) |
| Connections (aggregate) | **15,091,983** edges (≈15.1M) | **25,582,938** connections (from 124.2M raw synapses) |
| Source | FlyWire (Princeton/Codex) | Janelia/Google, Cell 2026, CC-BY |
| Role in the project | Brian2/GeNN reference, speed records, validation 16,978 | VNC = motor neurons → the fly can ACT; GA evolution, "Fly 2.0" |

Sources: `DEVLOG.md` §0; `understanding-roadmap.md` §1; `v783_infra_results.md` §E0/E1.

Annotation coverage of v783: **138,625** of 138,639 root ids (**99.99%**); without annotation — 14 (`v783_infra_results.md` §1, E0a/E0b).

**Data files.** v783: `data/2025_Connectivity_783.parquet` (100.8 MB) + `data/2025_Completeness_783.csv`. MaleCNS: `data/male-cns/` — `connectome-weights-male-cns-v1.0-minconf-0.5.feather` (1.05 GB), `body-annotations-…feather` (14 MB), `body-neurotransmitters-…feather` (43 MB), `syn-partners-…feather` (6.8 GB, 311.8M pairs, conf 100% ≥0.5), the resulting graph `malecns_connectivity.parquet` (107 MB, md5 `82cce6e8cadbb59210c0a5b191ba3619`). Unofficial segmentation fragments (88M ids) were filtered by `superclass.notna()`.

**Complete pools of v783:** KC 5,177 / MBON 96 / PAM 307 / PPL 24 / EPG 51 / LPLC2 210 (`v783_infra_results.md` §E1a).

Large pools of MaleCNS (annotations): `ol_intrinsic` 89,403; `cb_intrinsic` 32,164; `vnc_intrinsic` 13,161; `visual_projection` 9,201; `vnc_sensory` 6,370; `ol_sensory` (= photoreceptors R1–R8) 6,098; `cb_sensory` (= ORN + antennal mechano/thermo/hygro) 4,868; KC 4,064; ORN 2,635; gustatory 1,428; MBON 97; DAN 340 (PAM 316 + PPL 24; PPM 14 (a separate class, not DAN)); EPG 50; Δ7 42; APL 2. Source: `sfa_types.md` §2.

> **Naming pitfall (important):** `ol_sensory` in MaleCNS = optic lobe (photoreceptors R1–R8), **NOT olfaction**; olfaction = `ORN_*` in `cb_sensory`. Pools must be checked by `type`/`class`, not by superclass name (`sfa_types.md` §2.1; DEVLOG §26).

## 0.2. The three-class edge layer (chemical / electrical / modulatory)

Chemical edges carry a sign (exc/inh), while modulatory ones (dopamine/serotonin/octopamine) must be a **gate of plasticity, not drive**. The neurotransmitter audit of MaleCNS revealed that the original converter assigned the wrong sign because of the choice of NT column.

**Four bugs found by the audit (`docs/nt_audit.md`):**

| # | Bug | Numbers | Rule |
|---|---|---|---|
| 1 | **`predicted_nt` → KC = dopamine** | KC 4,064: `predicted_nt` = dopamine 4,058, `celltype_predicted_nt` = dopamine 4,062, `consensus_nt` = **acetylcholine 4,064** | The clean source of sign is only `consensus_nt`. A naive 3-class scheme on `predicted_nt` would give **885,611** outgoing edges KC → mod (the network loses associative memory), a total of 1,531,095 mod edges |
| 2 | **Histamine = EXC (biologically incorrect)** | In `consensus_nt` histamine = 7,891 neurons and **89,723 edges**; these are photoreceptors/lamina (R1-R6 3,377; T1 1,777) | Histamine is inhibitory (HisCl1/HisCl2, chloride channels) → **inh** |
| 3 | **PEN/PEG "GABAergic" — not confirmed** | All 60 neurons (PEN_a 20, PEN_b 22, PEG 18) = acetylcholine in all columns, including `ground_truth`; eLife 66039 (Hulse 2021) — PEN **excite** EPG | Do not change; CX ring neurons are GABAergic, but not PEN/PEG |
| 4 | **Modulatory classes were lost into the default +1** | dopamine 392 / serotonin 48 / octopamine 101 (consensus) travelled as excitatory; PAM→KC = **110,450** edges; all dopamine→KC = 129,132 | dopamine/serotonin/octopamine → **mod** (signed = 0, separate column `Modulatory`; the engine reads it as a gate) |

Other audit clarifications: the item about "13% sugar mis-glutamate" refers to **10 of 77 LB3-GRN (13.0%)**, not to the `gustatory` class (there 7.4%); 80% of the rows of the weights file have `body_post` outside the official set, the real connectome = 25,582,938 edges, not 151M.

**Result of the three-class converter (`tools/convert_malecns.py`, `docs/nt_converter_3class.md`):**

| Class | Neurons | Edges |
|---|---:|---:|
| exc | 106,897 | 15,333,399 |
| inh | 59,262 | 9,813,998 |
| mod | 541 | 435,541 |
| **sum** | **166,700** | **25,582,938** |

Column scheme: `Excitatory` 0/1, `Inhibitory` 0/1 (new), `Modulatory` 0/1 (new); `Excitatory x Connectivity` = +con exc, −con inh, **0 mod**. The legacy mode (`FLY_NT_SOURCE=predicted`, `FLY_NT_3CLASS=0`) is reproduced **bit-for-bit** (`frame.equals(backup) == True`); `tools/test_malecns.py` — **20/20 PASS**. Control numbers: PAM→KC 110,450 all `Modulatory==1`/`signed==0`; modulatory neurons→KC 136,347; histamine 7,891 neurons / 89,723 edges all `Inhibitory==1`.

> **Consequence:** the re-validation of the canons (§0.5) is not "just new numbers", but a correction: the old hot canon 979,182 was built on the bug histamine=EXC (photoreceptors excited the lamina); after the fix — **96,047** (DEVLOG §49).

Electrical connections (gap junctions) are the third class; their implementation and status are in Chapter L1 (§1.5) and `gap_impl_draft.md`. The gap table of FlyWire v783 is **absent** from the edge list (chemistry only) → the R4 leg of v783 (gap GF→TTMn) remains MaleCNS-only (`v783_replication_plan.md` §1.2).

## 0.3. The engine: the custom CUDA core "Fly"

The engine evolved from a fork of the Eon Systems benchmark (GeNN/Brian2) to its own core `tools/muha3.cu` (version 3.3), which became the main one. Architectural decisions:

| Element | Implementation | Source |
|---|---|---|
| Partitioning | **block-per-(part, fly)**; target-partition propagate, K=12 target ranges (no write conflicts); warp-per-source; batch with per-fly weights | DEVLOG §9, §10 |
| State | **16 bytes per neuron**: `v`, `g`, `acc` (float32) + `packed u32` `[rcnt|rsteps|flag|is_exc]`; 2 neurons/sector | DEVLOG §10 |
| Weight quantization | signed **w12**: packing `[col 20b][w12]` into u32 (weight — high 12 bits, mask `0xFFF00000u>>20`; column — low 20), per-row MAX-scale `scale0 = rowmax/2047`, clip **±2047**; ~102 MB | DEVLOG §12, §15; `tools/muha3.cu` (packed) |
| Determinism | integer fixed12.20 accumulator (commutative atomics), ordered-safe active-list | DEVLOG §11 |
| Lazy-decay | closed-form decay (LUT 2048, guard_coef 0.15822) for sleeping neurons; active neurons ~3,000/step, 90.9% of visits decay-only | DEVLOG §16 |
| Device step counter | fix of the root bug of CUDA graphs (baked t → periodic stimulus); t is taken from the device counter | DEVLOG §14 |
| Active physiology in the core | SFA (float `adapt[N]`), PIR, STD (`std_state`/`fly_set_std_map`), gap junctions (event A2a + current A2b), per-pool maps | DEVLOG §33, §48, §58; ch. L1 |

**Core pitfalls (documented, important for reproducibility):** the u8 counter `rcnt` overflowed on the 256th step without spikes (fix — clamp 255); the hot bit (bit22) fell into the spike flag byte → perpetual refractoriness (fix `flag & 1`); the quantization clip must cover the entire signed range (±2047 for /2047) — a half clip + renorm produced a silent "dead network" (DEVLOG §10, §11, §15).

**GeNN hub-split — the reference.** The GeNN ragged padding inflates the synapse table to `numPre × maxRowLength` (median 79 connections, top hub 9,783 → 1.36 billion cells, 99% zeros = 10.85 GB → OOM on 8 GB). Solution (`code/run_genn.py`, `GENN_HUB_SPLIT=1`): 976 neurons with fan-out>700 were moved into the `hub` population, synapses split into 4 groups reg/hub×reg/hub → ~1 GB VRAM. Result: v783 **11.89x** (CUDA Graphs + PERF1-4 + block256), validation PASS 16,978 deterministic spikes over t=1s. Launch requires `CUDA_PATH=/usr`. GeNN is deterministic at `GENN_SEED=12345`. Source: DEVLOG §1–4, §6; `genn_graph_probe.py`.

## 0.4. Speeds

All the numbers given are for a "live network" (after the "dead network" saga of 17.09.2026: a speed measurement must be taken in a single run together with spike counting). Environment: RTX 3070 Laptop 8 GB.

**GeNN (v783, reference):**

| Backend | Speed | Note |
|---|---:|---|
| GeNN hub-split + CUDA Graphs + PERF1-4 + block256 | **11.89x** | current GeNN record, validation PASS |
| GeNN hub-split + CUDA Graphs (k=10) | 5.98x | first graph result |
| GeNN hub-split, GENN_TIMING=0 | 4.94x | first launch on 8 GB |
| Eon GeNN (original) | OOM (10.85 GB) | Eon: 2.04x on RTX 4070 |

**The custom core "Fly 3.3" (MaleCNS, healthy environment, t=10s no-IO):**

| Mode | Speed | Note |
|---|---:|---|
| sparse graph, nparts=40 | **21.2x** | wave 3, lazy closed-form decay (cold environment gave 21.41x; healthy warm 21.19/21.16x) |
| sparse graph (eager, lazy=0) | 19.34x | for comparison: lazy in a degraded environment is faster than eager in a healthy one |
| sparse aggregate B=25 (farm) | **48.75x** (degraded) / 37.8x (healthy) | saturation ~B=16–25; a GA generation of 25 genomes × 10 s in ~3.4 s |
| hot graph (default) | **0.71x** | hot does not sleep — lazy is useless; mode adaptivity (sparse → active-list, hot → full-sweep) |

Sources: DEVLOG §1, §15, §16, §17, §49. Rival context: their per-fly 0.036x, aggregate 3.04x on our map — our per-fly dominates; for comparison, their "Ghost" is a different discipline (a farm of identical flies).

## 0.5. Verification canons

Any core edit is verified "bit-for-bit" on canonical runs. Current numbers (3-class data):

| Canon | Number | Check |
|---|---:|---|
| MaleCNS sparse_olf t=1s | **4,174** | graph ≡ direct |
| MaleCNS hot t=1s (default exp) | **96,047** | graph ≡ direct |
| v783 (muha3 direct) | **17,627** | graph ≡ direct; pre/post-merge identical |
| GeNN v783 `regress.py val` | **16,978** | VALIDATION PASS |
| 64-bit spike recording (6000-step test) | **5,999** (times up to 5999) | debt E5 closed |
| E9d learning (SFA-off reference) | 372,846 / 366,543 | bit-for-bit regression |

The old canons of the `predicted_nt` era (5,123 / 979,182) are **obsolete** after the NT fix (DEVLOG §48, §49). Comparison rule: because of the non-deterministic order of `spike_out` (atomicAdd), compare the **sorted set `(time,id)`**, not the order (DEVLOG §48; `gap_impl_draft.md` §5, risk #9).

## 0.6. Boundaries of the L0 layer and the remainder

- The gap table of FlyWire v783 is absent from the edge list → the gap leg of v783 (GF→TTMn) is MaleCNS-only (`v783_replication_plan.md` §1.2).
- The pool of 54k gap candidates of MaleCNS is **over-inclusive** (necessary-only): discriminant validity 0, an external ground truth is needed (electrical edge list / innexin expression) (`gap54k_validation.md` §Verdict).
- Gap map: 24 curated + 20 predicted; peptide crosswalk v3 — 1,231 → **5,399** neurons (×4.4, 19+ peptides) (`gap_map.md`, `peptide_crosswalk_v3.md`).
- **L0 remainder: none** (the structure is known — L0 is closed, 100%).

## 0.7. Summary table L0

| Mechanism | Status | Key numbers | Source |
|---|:---:|---|---|
| FlyWire v783 | ✅ | 138,639 neurons / 15,091,983 edges | `v783_infra_results.md` |
| MaleCNS v1.0 | ✅ | 166,700 neurons / 25,582,938 connections / 124.2M synapses | DEVLOG §0 |
| Three-class layer | ✅ | 435,541 mod edges; exc/inh/mod = 106,897/59,262/541 neurons | `nt_converter_3class.md` |
| NT audit (4 bugs) | ✅ | KC consensus=ACh; histamine→inh 89,723 edges; PAM→KC 110,450 | `nt_audit.md` |
| Core "Fly 3.3" | ✅ | 16B state, w12 (±2047), lazy LUT 2048, integer-acc | DEVLOG §9–16 |
| GeNN hub-split | ✅ | 976 hubs, ~1 GB VRAM, v783 **11.89x** | DEVLOG §1–4 |
| Core speeds | ✅ | sparse 21.2x; farm 48.75x; hot 0.71x | DEVLOG §16, §17, §49 |
| Canons | ✅ | 4,174 / 96,047 / 17,627 / 16,978 | DEVLOG §49 |
| Peptide crosswalk | ✅ | 1,231 → 5,399 neurons (×4.4) | `peptide_crosswalk_v3.md` |
| Gap map | 🟡 | 24 curated + 20 predicted; 54k = T3 pool | `gap_map.md`, `gap54k_validation.md` |

---

# Chapter L1. Physiology: the mechanisms without which nothing works

Physiology is a set of rules that turn a static graph into a dynamical network. None of the mechanisms in this chapter is an "option": without them the network either goes into a perpetual attractor or stays silent. The layer is estimated at **92%** functional reproducibility (`understanding_final_map.md` §L1/FINAL); the estimate of epistemic completeness is ≈100% (every question is closed by a mechanism or a proven boundary).

## 1.1. The LIF model and its bistability

The base neuron model is leaky integrate-and-fire (LIF). Key parameters: `DT = 0.1 ms`, `tauMem = 20 ms`, `vRest = vReset = −52 mV`, `vThreshold = −45 mV` → **threshold gap 7 mV**; synaptic delay `tDelay = 1.8 ms`, ring `SLOTS = 19` (≈1.9 ms/synapse). Source: `sfa_types.md` §1, `e5_latency.md`.

**A fundamental property of wild-type — bistability.** Map E4 (24 points: `inh_gain ∈ {1.0,1.1,1.2,1.3,1.5,2.0} × rmax ∈ {10,20,40,80}`, seed 12345, POP=4) showed:

1. **Wild has no transient regime in any of the 24 points** — "flash and fade" is unattainable by a global E/I shift. This explains the community's learning negatives (the attractor masks the depression of a small fraction of edges) and is the root conclusion of the project (`docs/attractor-learning-blocker.md` §32).
2. **Coarse geometry:** `inh=1.0` always fires (peak 28–37k sp/segment, off 42–55k), `inh≥1.5` always stays silent (peak 100–2000); the zone 1.1–1.3 is a **ragged boundary** with stochastic bistability (one genome + parameters yields per-slot `['silence','ATTRACTOR','ATTRACTOR','silence']`).
3. A telling detail from §19: ignition at ~500–550 ms, transition 15k→55k/segment, does not decay after offset (~56k/segment ≈ 1.1M/s).

Source: `docs/e4_bistability.md`; DEVLOG §19, §24, §32.

## 1.2. SFA (spike-frequency adaptation)

**What it does.** A per-neuron trace `adapt[N]`: after a spike `a += sfa_d` (cap 10 mV); each step `a -= a·tf_sfa`; effective threshold `v_eff = v − a`. Steady-state adaptation `a_ss ≈ d·r·tau`; even a "weak" `d=0.10` is noticeable at high rates, while it barely affects sparse discharges. Implementation in the core (wave 4): a separate array `adapt[N]`, the neuron state is untouched; **`adapt == NULL` → bit-for-bit**. Core backup `.bak-presfa-20260920`. Source: `sfa_types.md` §1; DEVLOG §33.

**Why this is the key to learning.** A global `d=0.25 / tau=300` removed the perpetual attractor (27–29k → ~15k spikes/segment), stabilized the dynamics and gave **reproducible learning specificity**: E9d SPEC (all MBON) mean **1.50×**, (excitatory) **1.63×**, **8/8 seeds**; controls: no-reward shift 0.0000 / 0 changed, random-KC SPEC 0.74× (<1 — non-specific depression gives no A-effect). Source: DEVLOG §34; `learning-stage-report.md`.

**Transition to type-specific SFA.** The infrastructure evolved from a scalar to an array `sfa_d[N]` (and `sfa_tau[N]`) with four groups:

| Group | d (mV/spike) | tau (ms) | Neurons | % | What is included |
|---|---:|---:|---:|---:|---|
| **G0** no SFA | 0 | — | 3,545 | 2.1% | CX ring (EPG/Δ7/PEN/PEG/ER/PFN/TuBu/FB/PFL…), DAN, MBON, APL |
| **G1** weak | 0.05–0.10 | 250–400 | 29,644 | 17.8% | KC (0.08/300), other cb_intrinsic, endocrine, unknown |
| **G2** medium | 0.15–0.25 | 200–300 | 108,421 | 65.0% | ol_intrinsic (visual), vnc_intrinsic, visual_projection, DN/AN, ALPN |
| **G3** strong | 0.30–0.50 | 100–150 | 25,090 | 15.1% | motor neurons (0.40/150), photoreceptors R1–R8 (0.30/150), ORN (0.35/150), LPLC (0.40/120), T4/T5 (0.30/120), DNp01 (0.40/100) |

Design principle: **zero on the ring/readout** (SFA=0 — canon of working memory, the bump is alive 5–6 s), **medium on echo populations** (removes the attractor), **weak on KC** (preserve pattern separability). Falsifiable hypothesis: fine-SFA should simultaneously hold the bump and reproduce specificity. New E9d canon after the rebuild: **SPEC 1.73 [1.25–2.20], 6/6 seeds** (window 4–14, exc-MBON, fine-type SFA with the scalar FLY_SFA=0; G2≈0.20). Source: `sfa_types.md` §3–4; `e9d_recalibration.md` §5–6; `understanding_final_map.md` L1.

> A direct antithesis: SFA is a **suppressor of the "echo"** of recurrent internal populations, not a universal brake. It fixes learning, but kills the working bump (the bump is precisely sustained recurrent activity). Hence the type-specific design (`sfa_types.md` §1).

## 1.3. PIR (post-inhibitory rebound)

**What it does.** While the inhibitory conductance `−g > pir_thr`, `pir_a += (dv − pir_a)·build` accumulates (build-up); otherwise `pir_a` decays. In the integration `v += tf_mem·(g + pir_a − (v−v_rest))` — a rebound current with a steady-state shift `pir_a` mV. API: `fly_set_pir` / `fly_set_pir_map`; the `fly_run` signature was unchanged. **`pir_state == NULL` → bit-for-bit**. Source: `pir_cpg_results.md` §1; DEVLOG §64.

**Proof.** Half, drive A only, T1 gain30: with PIR off and dv=8, neuron B gives **0** spikes; with **dv=30** — **27** spikes ("firing" out of silence). Threshold −45 mV at v_rest −52 (gap 7 mV). Bit-for-bit canons (sparse 4,174 / hot 96,047 / v783 17,627). Source: `pir_cpg_results.md` §1.4.

**Role:** PIR is a load-bearing mechanism of the song-CPG and a mandatory prerequisite of the half-center (see §1.8).

## 1.4. STD (short-term depression)

**What it does.** A per-neuron presynaptic factor `d[i] ∈ (0,1]`: on a spike `d[i] *= (1−u)`; recovery is closed-form; the delivery of a chemical edge is scaled by `d[pre]`. Control: `fly_set_std(state, last, u, tf)` + `fly_set_std_map` (per-pool); `NULL/0 → bit-for-bit`. Source: `trace_std.md` §2; DEVLOG §58, §60.

**Results.**

| Check | Result |
|---|---|
| Bit-for-bit off | ✅ sparse 4,174 / hot 96,047 / WM pat / E9d element-wise |
| Habituation | ✅ response drops to ~1/3 at u=0.1–0.3; GF reflex 32→20 (off) vs 7→2–3 (on); u=0.5 — reflex fully suppressed |
| E9d canon | ✅ at **u≤0.005** (3/3 seeds); ❌ at u≥0.01 |
| WM bump | ✅ at **u=0.002** (pos 10/25); ❌ at u≥0.005 (pos40 drifts) |

**🔥 Critical regime (boundary).** The network operates at the threshold of attractor bistability. For a neuron with interval T: `d_ss ≈ a/(u+a)`, `a = 1−e^{−T·tf}`. At 100–200 Hz and `tau_D=500 ms`, `a≈0.001–0.002`, so even `u=0.02` gives `d_ss≈0.05–0.1` → the network stalls. **Practical range for the engine: u ≤ 0.005 (E9d), u ≤ 0.002 (WM)** — a systemic limitation, not a bug. Source: `trace_std.md` §2.4.

**Eligibility trace** (host-side, `dan_core.depress_trace`): trace-conditioning is alive with a CS→US pause up to **1000 ms** (shiftA 0.063 vs 0.009 without trace), but reduces E9d specificity (SPEC 0.929 < 1.5, changed ×3). VERDICT: PARTIAL (`trace_std.md` §1).

## 1.5. Gap junctions (electrical synapses)

A LIF model without gap is "physically no faster than 1.9 ms/synapse" (`tDelay=1.8 ms`, `SLOTS=19`) — this cannot reproduce sub-ms escape latency (biology ~0.6 ms). The gap line was closed in four waves: **wave 1** — event delivery (A2a); **wave 2** — scalability check on whole-brain pairs; **wave 3** — per-pair weights + indexing; **wave 4** — current gap (A2b) + AL/E9d panel.

**Wave 1 — A2a: event delivery in 1 step.** `gap_inject`: for each pair whose target is in partition `b`, checks `pre` in the current step's spike queue → `atomicAdd(gap_acc[post], gap_g·4096)` + hot bit + active-list. `gap_g` is calibrated as "one spike of pre brings post to threshold" (7 mV).

| Condition | seed | GF_R→TTMn_R | TTMn_L (control) |
|---|---:|---:|---:|
| A control | 12345 / 777 | 11.2 / 12.1 ms | 21.5 / 22.5 ms |
| **B gap g=8 mV** | 12345 / 777 | **0.1 / 0.1 ms** | 21.5 / 22.5 ms |

The threshold is between g=6 and g=8 (consistent with the 7 mV gap). Specificity: the non-target L-pair is untouched. `gap_pairs==NULL` — bit-for-bit with the pre-merge core (6,947 = 6,947, `pairs_equal=True`). Source: `gap_impl_draft.md` §3; DEVLOG §48.

**Wave 2 — scalability.** A single `gap_g` does not scale: GJ-14 MDN↔MDN PASS at g=3 (synchrony 0.03→0.93, latency 29→10 ms); GJ-12 KC↔KC top1% (5,284 pairs) is safe only at g≤3; GJ-10 eLN→PN (11,576 pairs) and PN↔PN (8,100) overload AL; GJ-13 DPM↔APL — FAIL (an event gap does not reproduce a modulatory connection). Wave verdict: **PARTIAL** (`docs/gap_wave2_results.md`).

**Wave 3 — per-pair weights (`gap_w`).** `fly_set_gap_w(const float*)` — the absolute weight of a pair in mV (parallel to `gap_pairs`); `NULL` → scalar `gap_g` bit-for-bit. Removes the systemic limitation of wave 2. Smoke test KC↔KC top1%: `pp_mixed` (strong 526 @8 / weak 4,758 @0.5) holds the KC pool at **1,432**, whereas a single g=8 blows it up to **2,804**. Gap indexing + stamp: GJ-10 **11,576 pairs** → time **35.0 s → 2.18 s (×16)** (root — linear scanning of the queue; stamp + parallel, bit-for-bit). Source: `gap_perpair.md` §3; `gap_indexing.md`.

**Wave 4 — A2b: current gap `I = g·(v_src − v_post)`.** `fly_gap_current` (agent No. 110): canons bit-for-bit, GF→TTMn at **g≈1.5** → 0.1 ms. In the AL loop, **synchrony and recruitment were dissociated** for the first time: `g=0.05` gives synchrony **×3.95** at **18/329 = 5.5%** recruitment, whereas A2a `lin1` pays 77–79% recruitment for the same synchrony. E9d does not drop learning (1.58/1.61 vs canon 1.60), **but** 3/6 seeds ≥1.4 and the run is outside the pre-registration (NTEST=1, fine-SFA off) → 🟡 partial. Source: `gap_w4_results.md`; `gap_a2b_results.md`.

**Gap loops (GF system).** GF loop: 107 pairs; JO→GF→TTMn **57.6 → 0.2 ms**; `EXCLUDE_CHEM` removes the duplicate (×5 inflation). Source: DEVLOG §64.

**Boundary:** the pool of 54k gap candidates of MaleCNS is over-inclusive (necessary-only), discriminant validity **0** (negative pairs also fire at 0.10 ms) → an external ground truth is needed (`gap54k_validation.md` §Verdict).

## 1.6. APL fix (KC sparsification)

**Problem.** APL = 2 neurons (bodyId 10540 R / 10977 L), GABA, `cb_intrinsic`; KC→APL 4,693 edges; APL→KC 4,633 edges, coverage **4,063/4,064 KC** — global feedback, as in the live animal (Lin et al. 2014). But with uniform weights the LIF loop is too weak: wild gives **100% KC active, overlap 100%** (in the live animal ~5%; community fix 5.4%/overlap 70%).

**Anatomical fix.** Amplification of existing APL→KC weights at assembly (without editing the core): `FLY_APL_GAIN`.

| Condition | KC active (3 odours) | Overlap Jaccard |
|---|---|---|
| APL-off ≡ wild | 100 / 100 / 100% | 100% |
| APL×2 | 28.4 / 29.4 / 26.9% | 88.9% |
| **APL×2.5** | **5.6 / 8.5 / 6.7%** | **65.4%** |
| APL×3 | 2.0 / 3.7 / 4.5% | 47.7% |
| ≥APL×5 | ~0–1.4% | 0% (silence) |

Working window **2.5–3**. Source: `apl_fix.md`; DEVLOG §27.

## 1.7. T1 inset and phase-gated T1 (taming the attractor tail)

**Task.** Wild is bistable; the post-stimulus tail (perpetual attractor) spoils all behavioural metrics. A host-side mechanism is needed that suppresses the tail by ≥50% while preserving the response, the WM bump and the E9d canon.

**The winner — T1 (global inhibitory reset).** `FLY_TAIL_MECH=T1 FLY_TAIL_T1_MODE=scale FLY_TAIL_T1_GAIN=3`: `row_scale` of inhibitory sources (consensus ∈ gaba/glutamate/histamine, **59,262 neurons** = 35.6% of the network) ×3 after stimulus offset. No memory/learning/RNG.

| Metric | Result |
|---|---|
| Tail (tail_ratio) | **0.256 / 0.286 / 0.286** (−72.4%) |
| Stimulus response (stim_ratio) | **1.000** (not lost) |
| WM bump | **sharpened**: late 0.77, conc 0.69 (vs 0.56 wild), act 17 (vs 20) |
| E9d | unchanged (the mechanism does not enter the 4–14 window) |

The mechanism is a **contrast amplifier**: it suppresses the diffuse attractor, while the focused bump survives. `gain≥4` breaks the bump. Wild tail ≈ **153,394 spikes**, 96% in the `other` region (not MBON/KC) → it is a whole-network paroxysm, not an MB readout. Bit-exact controls at neutral parameters. Reserve: `T2t_f5d5`. Source: `docs/attractor_taming.md`; DEVLOG §54.

**Unified physiology.** The sweep `FLY_TAIL_START × GAIN` removed the "E9d ↔ behaviour" conflict: the threshold is in **gain**, not in ts. Winner `T1 gain=2, start=12`: E9d SPEC **1.84** (5/6 >1.2) ✅, tail **0.43** ✅, E10 5/6 ✅, H2 7/8 ✅, WM holds ✅. Full boost (H2 8/8, E10 6/6) — only at gain=3, which kills E9d. Source: DEVLOG §55; `tail_sweep.md`.

**Phase-gated T1 — resolving the conflict.** `FLY_TAIL_PHASES=train:0,probe:3`: the mechanism is active only in test phases → E9d SPEC **2.04** (better than baseline) + tail **0.30** + H2 **8/8** (ts=12 g3) + E10 **6/6** (ts=10 g3). Source: DEVLOG §58 (`cx_wta_pgated.md`).

## 1.8. Oscillators: walking-CPG, song-CPG, half-center

**Walking-CPG (E1–E2–I1).** A published motif (Pugliese/Brunton/Tuthill 2025, 3 neurons) found 1-to-1 in MaleCNS: E1=IN17A001, E2=INXXX466, I1=IN16B036; loop E1→E2 2733 · E2→I1 445 · I1→E1 3705 (inh) · I1→E2 1261. It oscillates in a spiking LIF **without SFA/PIR** (pure E/I loop): **6/6 seeds**, 58 cycles/4 s, drift≈0; frequency is controlled by drive (**6.9 → 15.4 Hz** over 100→800 Hz, the Pugliese prediction confirmed); no-drive — complete silence. Source: DEVLOG §57, §59; `cpg_results.md`.

**Song-CPG (TN1a ↔ vPR9).** The first endogenous song rhythm: **36–37 Hz, 3/3 seeds**, drift 0.00–0.03, frequency monotonically controlled by `tau_sfa` (53.5 → 34.5 Hz). Ablation: **PIR is load-bearing** (without it 36 → 12.5 Hz), STD adds (36 → 28.5), SFA sets the base 11.5. Limitation: TN1a↔vPR9 are strictly **in phase** (co-activation, Δφ ≈ +26…+32°), wingMN is dead (rate ≈ 2). Source: `pir_cpg_results.md` §3; DEVLOG §64.

**Half-center (antiphase).** For a long time it was a negative: mutual inhibition A→B 1305 ≠ B→A 628 (asymmetry), release gave a slow anti-correlation (env r₀ down to −0.76), but there was no locked antiphase. **Closed 29.09.2026 (DEVLOG §101):** `C2-sym` (= symmetrization of loop weights, `FLY_CPG_BALANCE=sym`: B→A and A→B aligned to Σ|w8|=8480; `half_center_v2_design.md` §C2) + SFA τ=50 gives all criteria at **3/3 seeds** — envelope −0.45/−0.59/−0.86; raw 20 ms ≤−0.15 on all; alternation **0.5–2/s**. This is **stochastic** alternation (not a rigid cycle: there is no correlation on the 5-ms raw). Source: `pir_cpg_results.md` §2; `cpg_v2_results.md`; DEVLOG §101.

## 1.9. Additional physiological mechanisms (briefly)

| Mechanism | Key numbers | Source |
|---|---|---|
| Circadian oscillator (Van der Pol) | period **24.00 CT-h** (host) / 23.67–24.11 (network); PRC CT14 −1.8 / CT22 +1.8; LD 12:12 entrainment at T=23/25; sleep gate cos=1.0 | `circadian_results.md`; DEVLOG §64 |
| Evidence accumulator (N2, h∆K) | gradual persistent bump series8/strong8 = **1.80×**, rho **+0.81**, τ≈5s | `evidence_results.md`; DEVLOG §64 |
| DAN/PAM modulator | THR=5, gate 70/70; endogenous channel weak (external reward needed) | `pam_calibration.md`; DEVLOG §50 |
| E5 latencies | DNp01→MN **1.9 ms** (half ring-delay); LPLC2→GF 6.3 ms; GF→TTMn 2.7–2.9 ms vs biol. ~0.6 ms | `e5_latency.md` §1.3/§3 |
| per-compartment readout | comp 0.635 vs global 0.396 (+0.240, 8/8); comp>exc 24/24 seeds | `per_compartment_readout.md`; DEVLOG §84 |

> Circadian rhythm, the evidence accumulator and state (hunger/sleep/arousal) belong to layer L5, but are physically implemented as mechanisms of the dynamics — hence they are mentioned here.

## 1.10. Boundaries of the L1 layer (honestly)

1. **The core is physically no faster than 1.9 ms/synapse** (`tDelay=1.8 ms`, `SLOTS=19`) → sub-ms escape is not reproducible without a separate gap branch (`e5_latency.md` §4).
2. **The 54k gap pool is over-inclusive** (necessary-only); discriminant validity 0 → an external ground truth is needed (`gap54k_validation.md` §Verdict).
3. **A2b-AL-E9d** — outside the pre-registration (NTEST=1, fine-SFA off); requires a rerun with the canonical env (`gap_w4_results.md` §3/§6).
4. **Half-center** — stochastic alternation (not a rigid locked cycle); song — in phase, not antiphase; wingMN is dead (`pir_cpg_results.md` §2–3; DEVLOG §101).
5. **STD narrow range** u≤0.005 because of the critical regime (`trace_std.md` §2.4).

**L1 remainder:** external ground truth for the gap pool; rerun of A2b-E9d with the canonical env; σ-holding test; sub-ms branch (on request).

## 1.11. Summary table L1

| Mechanism | Status | Key numbers | Source |
|---|:---:|---|---|
| LIF + wild bistability | ✅ | 7 mV gap; no transient 24/24; zone 1.1–1.3 ragged | `e4_bistability.md` |
| Global SFA | ✅ | d=0.25/300; SPEC 1.50/1.63, 8/8 seeds (legacy canon; current: fine-type SFA with FLY_SFA=0) | DEVLOG §34 |
| Fine SFA (G0–G3) | ✅ | SPEC **1.73 [1.25–2.20], 6/6**; G0=0 / G1≈0.08 / G2=0.20 / G3=0.35 | `sfa_types.md`; `e9d_recalibration.md` |
| PIR | ✅ | rebound B 0→27 (dv=30); canons bit-for-bit | `pir_cpg_results.md` |
| STD | ✅ / 🟡 | habituation ~1/3; E9d u≤0.005 (3/3); WM u=0.002 | `trace_std.md` |
| Trace buffer (host) | 🟡 | conditioning up to 1000 ms; E9d 0.93 (<1.5) | `trace_std.md` §1 |
| Gap A2a (event) | ✅ | GF→TTMn **0.1 ms**; g=8 mV; control 11–12 ms | `gap_impl_draft.md` |
| Gap A2b (current) | 🟡 | g≈1.5 → 0.1 ms; sync ×3.95 at 5.5% recruitment; 3/6 seeds | `gap_w4_results.md` |
| Gap per-pair | ✅ | pp_mixed KC 1,432 vs single g=8 → 2,804; bit-for-bit | `gap_perpair.md` |
| Gap indexing | ✅ | GJ-10 11,576 pairs: 35.0 s → 2.18 s (×16) | `gap_indexing.md` |
| APL fix | ✅ | ×2.5 → KC 5.6–8.5%, overlap 65.4% (community 5.4%/70%) | `apl_fix.md` |
| T1 inset | ✅ | tail −72.4%; stim 1.000; bump sharpened; E9d unchanged | `attractor_taming.md` |
| Unified physiology (T1 g2 ts12) | ✅ | E9d 1.84; tail 0.43; H2 7/8; E10 5/6 | DEVLOG §55 |
| Phase-gated T1 | ✅ | E9d **2.04**; tail 0.30; H2 8/8; E10 6/6 | DEVLOG §58 |
| Walking-CPG (E1–E2–I1) | ✅ | 6/6 seeds; 6.9→15.4 Hz with drive | `cpg_results.md`; DEVLOG §59 |
| Song-CPG | ✅/🟡 | 36–37 Hz 3/3; PIR load-bearing; in phase, wingMN dead | `pir_cpg_results.md` |
| Half-center (antiphase) | ✅ | C2-sym+SFA τ50: env −0.45/−0.59/−0.86, alt 0.5–2/s, 3/3 | DEVLOG §101 |
| Circadian rhythm | ✅ | 24.00 CT-h; PRC ±1.8; sleep gate | `circadian_results.md` |
| Evidence accumulator | ✅ | 1.80×, rho +0.81, τ≈5s | `evidence_results.md` |

---

## Chapter summary

**L0 (structure) = 100%:** the connectome is fully typed (166,700 neurons / 25.6M connections MaleCNS; 138,639 / 15.1M v783), the three-class edge layer (exc/inh/mod) is correct after the NT audit, the "Fly 3.3" engine gives 21.2x sparse / 48.75x farm with full determinism, the verification canons are fixed (4,174 / 96,047 / 17,627 / 16,978).

**L1 (physiology) = 92%:** the network behaves as a live one within the known — type-specific SFA, PIR, STD, gap junctions (event + current + per-pair), APL fix, T1 inset/phase-gated T1, a full set of oscillators (walking/song/half). The boundaries are sub-ms latencies (bounded by the ring-delay 1.9 ms) and the external ground truth for the 54k gap pool.

The chapter's cross-cutting conclusion: **dynamics (L1) is a foundation without "options"; plasticity is strong, but the release of the learned delta runs into the physiology of excitability**, which is analysed in the following chapters (L3–L4).

---

### Verification of markers (resolved 29.09.2026)

All disputed numbers were checked against the project's primary sources:

- **v783 annotations 99.99%** — confirmed: 138,625 of 138,639 root ids (**99.99%**), without annotation — 14 (`v783_infra_results.md` §1, E0a/E0b). It was previously thought that the coverage in the source had not been checked — it has been checked.
- **w12 and the bit layout** — confirmed: an edge is packed as `[col 20b][w12]` (weight — high 12 bits: `(pe & 0xFFF00000u) >> 20`; column — low 20), `scale0 = rowmax/2047`, clip ±2047 (`tools/muha3.cu:537,809`; DEVLOG §15). The comment `[col24][w8]` in `muha3.cu:478` is obsolete (the name `w8` is legacy).
- **Eon GeNN 2.04x on RTX 4070** — a number from the comparison table of DEVLOG §1 (ours is OOM 10.85 GB); the Eon primary source is not cited in the project (the number is a project one, not external primary data).
- **Half-center "C2-sym"** — `C2-sym` = symmetrization of loop weights (`FLY_CPG_BALANCE=sym`, B→A and A→B aligned to Σ|w8|=8480; `half_center_v2_design.md` §C2); lock achieved with the addition of SFA τ=50 (DEVLOG §101).
- **Farm 48.75x** — confirmed: measured in the degraded environment on 17.09; the healthy honest estimate is **37.8x** at B=25 (DEVLOG §16; §15). Both numbers are given in §0.4.


---

# Chapter L2. Sensory channels: what the fly senses

> Overview chapter of the master monograph "Understanding the Drosophila brain". Layer L2 = innate sensory inputs and their instinctive responses.
> Sources: `docs/L2_sensors_map.md`, `docs/minor_channels.md`, `docs/minor_channels_results.md`, `docs/e7_olf.md`, `docs/e7_wind.md`, `docs/e6_escape_side.md`, `docs/e8_courtship.md`, `docs/e8b_results.md`, `docs/taste_final_n7.md`, `docs/apl_fix.md`, `docs/e3_ablation.md`, `docs/loom-task-design.md`, `docs/e9_lite_report.md`, `DEVLOG §18–28, §52–53, §86, §95, §99–101`.
> All numbers — from the listed documents, with reference (file §section / DEVLOG §); no unverifiable markers (disputed ones checked 29.09.2026).

The sensory layer answers the question: **which inputs actually reach behaviour and with what specificity.** In MaleCNS v1.0 (166,700 neurons, 25.6M connections) the inventory of sensory classes is complete: vision (visual 6,091), olfaction (olfactory 2,639), mechano-tactile (mechanosensory_tactile 2,558), mechanosensory (1,733), proprioception (1,454), taste (gustatory 1,428), hygro (66), thermo (25), hearing (auditory 114). Below — by channel: first the working mechanism with numbers, then the proven boundaries with causes, and at the end — a summary table.

Final assessment of the layer (29.09.2026): **L2 — ~90%** functional reproducibility (`understanding_final_map.md` §L2/FINAL; epistemic completeness ~100%: every channel has a verdict — a mechanism or a proven boundary). The sensory response survived the N10 degree-preserving null control — this is a property of the connectome, not of any Poisson noise (`n10_n6_results.md`; `L2_sensors_map.md` §6).

---

## 2.1. Vision: the loom instinct and escape

### Working mechanism

**Loom (looming threat) — full cycle P0–P4.** Goal: the network must form a *transient* escape response to a growing visual stimulus rather than perpetual activation.

- **Network ignition threshold — 2–5 Hz; saturation plateau — from ~20 Hz** (LPLC2 pool ×21). This recalibrated the loom profile: `r0 ≈ 1–2 Hz`, `rmax ≈ 20–50 Hz`, `τ ≈ 100 ms` (`loom-task-design.md` §2.2; DEVLOG §18).
- **GA fitness grew 0 → 2775 over 40 generations** × 16 individuals; fitness = ramp growth − penalty for non-decay (transient escape). Held-out (P4) passed: +469 (train) / +585 (seed 54321) / +538 (second profile τ=80, rmax=30), while wild gives 0 everywhere (`loom-task-design.md` §5; DEVLOG §20).
- **The champion genome — "louder input, quieter echo":** `visual_projection` ×1.82 (input louder) + `vnc_intrinsic` ×0.56 (echo loops quieter). Ablation E3 (27 subjects, seed 12345, POP 16) refined the load-bearing genes: KO `vnc_intrinsic` → fit −436, KO `cb_intrinsic` → 0.0 (perpetual attractor), KO `visual_centrifugal` → −262. The "louder input" hypothesis is **partially refuted**: KO `visual_projection` gives ramp 3274→1888 (this is amplitude), but the transient is carried by the "quiet echo". Separately, a **harmful gene** was found: KO `descending_neuron` (×1.23) gives fit **893.8 versus 382** for the champion — that is, reverting the gene without evolution beats the champion (`e3_ablation.md`; DEVLOG §23).
- **The escape side is read ipsilaterally on the Giant Fiber (DNp01).** The coarse somaSide mass of DN/MN is negative (E6/E6b — weak "inversions" <0.1 migrate across metrics, = noise), but a targeted DNp01 readout gives a clear inversion: ramp asym −0.354/+0.163 (seed 12345) and −0.412/+0.183 (seed 54321), **strength 0.26–0.30**, i.e. the left LPLC2 pool → the left GF more active (`e6_escape_side.md` §E6d).
- **Gap escape pathway:** GF_R→TTMn_R via gap junction — **0.1 ms** versus 11–13 ms of the chemical pathway; the independent L-pair GF_L→TTMn_L is untouched (21.5 ms) (`circuit-map.md` §A; `gap_impl_draft.md`).
- **Eligibility trace works in vision (N14):** a trace window τ=1000 ms gives a shift 0.000 → **0.217** — the plasticity-tag mechanism is not MB-specific (`n13141819_results.md` §N14).

### Proven boundaries
- **Sub-ms escape is unattainable through the chemical pathway:** the core is physically no faster than ~1.9 ms/synapse (`tDelay=1.8 ms`, `SLOTS=19`) — a separate gap branch is needed (`e5_latency.md` §4).
- **Wild-type is bistable:** with uncalibrated input, loom ignites a perpetual attractor (no offset response); the transient is a property of the evolved genome, not of wild (E4; `e7_olf.md` §caveats).

---

## 2.2. Olfaction: ORN → PN → KC → MBON → DN

### Working mechanism
- **The full chain ignites:** on stimulation of the ORN pool (step 0 → 200 Hz), baseline 0 → **KC 447,692 / MBON 20,089 / DN 11,648** spikes per slot-second. Latency ~1 segment (≈50 ms), saturation ~250 ms, flat plateau. Held-out POP=16/seed 54321 reproduces almost bit-for-bit (spread <1%) (`e7_olf.md`).
- **APL fix for KC sparsification (anatomical, without editing the core):** APL = 2 GABA neurons, `APL→KC` = 4,633 edges with coverage **4,063/4,064 KC**. At `FLY_APL_GAIN=2.5` the active KC fraction is **5.6 / 8.5 / 6.7%** (for three odours), overlap Jaccard **65.4%** — versus 100%/100% for wild and comparable to/better than the best community result (their fix: 5.4% sparseness, overlap ~70%). Working window 2.5–3.0; ≥×5 KC go silent (memory is dead) (`apl_fix.md`).
- **Innate KC onset code:** `AC_onset` (onset window) = **0.85–0.99** — the symbol dictionary is ready; the onset detector (rising edge) on real ORN spikes is equivalent to an ideal schedule (`address_release_synthesis.md` §3; `evidence_results.md` §4).
- **First pair validation (KC separability):** at `APL×3.5` and a 100 Hz stimulus, the Jaccard of the early window (seg 4) drops to **0.079** — odour patterns are separable; over the full window (200–700 ms) the attractor merges the codes (Jaccard 0.47 at APL×3.5; 0.58 is APL×2.5) (`e9_lite_report.md` §E9b).

### Proven boundaries
- **The "separability ↔ strength" trade-off:** the full window — hundreds of KC, but merged codes; the early window — a separable (J=0.079) but tiny (7–40 KC) depression, invisible in MBON (`e9_lite_report.md` §E9b). The solution was found later through the k-WTA hybrid and per-compartment readout (layer L4).
- **No offset response in wild:** after the odour is turned off, activity does not fall but rises — the same attractor phenomenon of E4 (`e7_olf.md`).
- **The KC pool in the previous canon was mislabeled:** `ol_sensory` in MaleCNS = the **optic lobe** (R1–R8 photoreceptors), not olfaction; the real ORN_* (2,635) live in `cb_sensory`. A trial run on the photoreceptor pool gave KC/MBON/DN = 0 ("the eye does not smell") — a specificity control (`e7_olf.md` §deviation).

---

## 2.3. Hearing: Johnston's organ, song and pC1

### Working mechanism
- **The temporal pulse code reaches the wing motor neuron:** on stimulation with a pulse song (pulse_JO_B), the period-7 rhythm is significant in 3/3 seeds: AMMC FFT 25.1–38.4, relay_SAD 89.2–125.9, wing_motor 9.6–19.1 (null threshold p95 ≈ 4). Pattern separation pulse vs sine = **0.82–1.08, 3/3 seeds** (`e8b_results.md` §1.1, §4).
- **Latency 7–10 ms** (≈1.5 segment) — matches E5 (GF 6.3 ms); therefore the rhythm must be measured with phase correction, otherwise the on/off contrast is negative (`e8b_results.md` §0, §1.3).
- **pC1 is reachable through the physiological JO pathway:** at **SFA=0 + d4200/r800** pC1 > 0 in **3/3 seeds** (peaks up to 93–140 spikes) — the "silence of pC1" was SFA suppression and weak drive, the attenuation is localized and reversible (`L2_sensors_map.md` §9; DEVLOG §101).
- **rollback_dn is NOT deaf to rhythm:** a significant period-7 rhythm is present in AMMC/SAD/wing and even fru_high (FFT 14.2 at SFA=0) — the "deafness" of E8 (42 spikes) was a low count, not a loss of the temporal code (`e8b_results.md` §5).

### Proven boundaries
- **Baseline attenuation of pC1 in the canon:** pC1 = 0 in all 27 runs of E8b (SFA=0.25) — the path `JO→relay→pC1` is too weak over 900 ms; pC1 has only 4 direct edges from JO with 57,578 inputs (E/I = 1.37) (`e8b_results.md` §6.1; `L2_sensors_map.md` §4.2).
- **The fru_* aggregate is an unreliable rhythmic readout** (1/3 seeds): 2,478 neurons average the signal; one needs to look at direct fru+ targets of JO (140) (`e8b_results.md` §6.3).
- **SFA is a conditional fix:** it suppresses the attractor fire of high drive (×4–10), but does not amplify the rhythm of weak JO_B (`e8b_results.md` §3).
- **MaleCNS itself is male:** the song is its own program, not an auditory input; the behavioural courtship circuit is not awakened at baseline drive (`e8_courtship.md`).

---

## 2.4. Taste: relative valence exists, absolute is a boundary

### Working mechanism
- **Relative valence coding:** under the SFA=0.25 canon the network in **6/8 seeds** reliably separates two subpools (sugar pharyngeal vs labellar) into opposite valence poles: |Δval| = 55–66, |Δratio| ≈ 0.82–0.87, |Δr4| ≈ 2.4. The A/A control is ideal — SS≡SB and BS≡BB **bit-for-bit** (0.0) (`taste_final_n7.md` §1.3–1.4).
- **On v783 — directed and specific sugar valence 8/8:** S vs shuffle/P/LS differ 8/8; behavioural MN9 — 8/8 (reproduces flybench task05) (`L2_sensors_map.md` §5.4; DEVLOG §99).
- **Sugar vs labellar are distinguishable by the valence output:** AVG/ATT = **0.29** (sugar pharyngeal pool) versus **0.66** (labellar); approach S-first +78.5 vs B-first +32.0 (`minor_channels_results.md` §4).

### Proven boundaries
- **Absolute polarity (sugar→approach) is not reproduced:** among the 6 discriminating seeds — exactly **3 approach / 3 avoid (50/50)**; the sign is set by the noise realization (the symmetry-breaking bit of the attractor), not by the stimulus. The concordant flip of raw, ratio and attractor-invariant R4/R5 confirms that this is not metric noise (`taste_final_n7.md` §1.4–1.6).
- **Causes:** attractor bistability; MaleCNS has no labelled sugar/bitter GRN (`receptorType`: only ppk/IR, Gr = 0); the rate anchor is insufficient (none of the 6 hunger-anchor arms PASS) (`taste_final_n7.md` §1.6; `minor_channels_results.md` §5).
- **The bitter bit does not flip on v783 either** → the boundary is not connectome-specific; the carrier = valence potentiation/PSP (`L2_sensors_map.md` §5.4; DEVLOG §99).
- **The "arm=label" bug was found and fixed twice:** first in `minor2.py` (hygro ≡ thermo bit-for-bit), then in the taste-v783 protocol (`L2_sensors_map.md` §3.4; DEVLOG §95, §99) — lesson: an arm must stimulate its pool, not be a label.

What would close the question (from the documents): the sign of the valence weight in `dan_core` (potentiate attraction / deprecate aversion), a per-pool modulator of attraction-MBON (NPF), a phase-gated attractor reset (`taste_final_n7.md` §5.3).

---

## 2.5. Wind: the pulse as an ignition trigger

- **Working mechanism:** the `wind_gravity` pool = 475 neurons (Johnston's organ, `cb_sensory`, types `JO-*`, nerve AN). The network is silent before the stimulus (baseline 0). A 100 ms pulse (200 Hz × 21) gives a **bimodal** response: ~60% of slots — ignition into a stable attractor (DN 492–616 sp/50 ms, MN 400–430), ~40% — a weak transient. Ignition: 2/4 (50%) POP=4 and **10/16 = 62.5%** POP=16-holdout (`e7_wind.md`).
- **Meaning:** "escape from wind" in wild is hardwired as **threshold ignition**, not a controlled transient; this is a direct demonstration of the E4 bistability (boundary inh 1.1–1.3, stochastic per slot).
- **Boundary:** baseline=0 makes the "peak/base" metric trivial — the correct formulation is "ignition probability"; the dose-response is not mapped (`e7_wind.md` §caveats).

---

## 2.6. Gravity and proprioception: work ipsilaterally

- **Gravity (haltere):** pool 400 (haltere 205 + campaniform 195). flight-MN response L 484 / R 494 versus random **217 ± 102 (z = 2.6 / 2.7)**; L/R index **+0.082 (L-stim) vs −0.063 (R-stim)** — ipsilateral at all three frequencies; dose-response monotone (L 293→350→484). Path haltere → vnc_intrinsic/ascending → steering/power MN (DLM/DVM/hg/b1-b3) (`minor_channels_results.md` §1).
- **Proprioception (chordotonal leg):** baseline strictly 0; L-stim → leg_ext_L **50.3**, leg_flex_L 25.3 versus contralateral 0.3–3.3 — **ratio 25–50×**; R-stim mirrored; ipsilateral. **Boundary:** flexors and extensors co-activate (not the classical reciprocal reflex) — a signed reflex requires a first-hop inhibitory interneuron, which the current topology lacks (`minor_channels_results.md` §2).

---

## 2.7. Hygro/thermo and the mechano-tactile channel

- **Humidity/temperature — partially.** The wiring exists (silence=0 → any stimulus ignites `lLN1_bc` ≈2300), but there is no mass selectivity: lLN1_bc thermo/hygro ≈ **1.00** (cross-asymmetry −5 = noise). The only readable targeted channel is `vp1m+VP2_lvPN2`: **thermo/hygro = 1.23 → 1.28 → 1.38** monotone with frequency (VP1 = thermo glomerulus), while hygro — plateau (`minor_channels_results.md` §3; `minor_channels.md` §3).
- **Mechano-tactile — works (wiring + motor).** Pool `mechanosensory_tactile` 2,558; stimulus 150 Hz → `ascending` **14,344**, `vnc_motor` 5,674, `descending` 4,438, `leg_ext` 239, `flight_mn` 821 sp/50 ms; **3/3 seeds** (spread <3%), silence control **0**. Modal purity: the AL pathway (lLN1_bc/vp1m/lpn11) = **0**. Specificity vs an equal-sized random: ascending ×2.95, vnc_motor ×2.18, leg_ext ×3.87, dlm ×8.70 (`L2_sensors_map.md` §3.2).
  - **Boundary:** there is no dedicated tactile labelled-line/topotaxis readout — the response is recorded as masses; latency is bounded by segment resolution (the first 50 ms).

---

## 2.8. Nociception: a data boundary

A scan of the MaleCNS and v783 annotations over the fields class/subclass/type/superclass/supertype/synonyms/receptorType: `ppk1=0, ppk26=0, class IV=0, multidendritic=0, TRPA1=0, nocicep=0`. The only `ppk` — `putative_ppk23` 269 / `putative_ppk25` 257 (taste/pheromone). **There are no true nociceptors (class IV md, Ppk1/TRPA1) in MaleCNS v1.0 — a boundary of the dataset, not of physiology.** The proxy arm (`--arm noci`) tests contact mechanosensation and is labelled `NOCI_PROXY` (`L2_sensors_map.md` §3.3).

---

## 2.9. Overall picture of L2

Of the 9 main channels (final status, 29.09.2026):

| Channel | Status | Key number |
|---|:---:|---|
| Olfaction | ✅ | KC 447,692 / MBON 20,089 / DN 11,648 spikes/s at baseline 0; held-out <1% |
| Vision (loom/escape) | ✅ | GA 0→2775; GF side 0.26–0.30; gap GF→TTMn 0.1 ms |
| Hearing | ✅/🟡 | rhythm→wing_motor 3/3; pC1 opened at SFA=0+d4200/r800 (3/3, up to 140 sp) |
| Taste | 🟡 | rel. valence 6/8; absolute 50/50 (physiology boundary) |
| Wind | ✅ | ignition 10/16 = 62.5% (POP16) |
| Gravity | ✅ | flight-MN L 484 / R 494 vs random 217±102 (z=2.6/2.7) |
| Proprioception | ✅ | leg_ext_L 50.3 vs 0.3–3.3, ratio 25–50× |
| Hygro | 🟡 | wiring exists (lLN1_bc ≈2300); no mass selectivity (≈1.00) |
| Thermo | 🟡 | vp1m+VP2: 1.23→1.28→1.38 with frequency |
| Mechano-tactile | ✅ | ascending 14,344; 3/3 seeds; control 0 |
| Nociception | ❌ | 0 matches in all fields — data boundary |

**Lessons of the layer (formulated during the work):**
1. **Pools must be checked by annotations, not by superclass name.** Classic: `ol_sensory` = vision (photoreceptors), olfaction = `ORN_*` in `cb_sensory` (`e7_olf.md`).
2. **A mass readout saturates with the attractor** — look for specificity in a targeted way (ALPN, not ALLN; GF, not somaSide mass).
3. **Arm bug:** label ≠ stimulus; found twice (hygro/thermo; taste-v783) (`L2_sensors_map.md` §3.4, §5).
4. **Silent failure:** SFA in `f1a_common.FlySimA.run_seg` was turned off despite a declared physiology of 0.25 — the "SFA" tag in the JSON turned out to be a phantom (`taste_final_n7.md` §0).
5. **N10 null control:** sensory pathways (olfaction/vision/wind/gravity) disintegrate in degree-preserving nulls — the "sensory response" = a property of the connectome (`L2_sensors_map.md` §6).

---

## Summary table L2: phenomenon → status → numbers → source

| Phenomenon | Status | Numbers | Source |
|---|:---:|---|---|
| Loom ignition threshold/plateau | ✅ | 2–5 Hz threshold / ~20 Hz plateau | `loom-task-design.md` §2.2; DEVLOG §18 |
| Loom GA fitness and held-out | ✅ | 0→2775 / 40 generations; held-out +469/+585/+538 | `loom-task-design.md` §5; DEVLOG §20 |
| Champion genome (load-bearing genes) | ✅ | `vnc_intrinsic` KO → −436; `descending_neuron` KO → 893.8 vs 382 | `e3_ablation.md`; DEVLOG §23 |
| Escape side (GF) | ✅ | ramp asym ±0.26–0.30, 2 seeds | `e6_escape_side.md` §E6d |
| Gap escape GF→TTMn | ✅ | 0.1 ms (chem. 11–13) | `gap_impl_draft.md`; `circuit-map.md` §A |
| Eligibility trace in vision (N14) | ✅ | shift 0.000→0.217 (τ=1000) | `n13141819_results.md` §N14 |
| Olfactory chain | ✅ | KC 447,692 / MBON 20,089 / DN 11,648; held-out <1% | `e7_olf.md` |
| APL KC sparsification | ✅ | 5.6/8.5/6.7% active, overlap 65.4% at ×2.5 | `apl_fix.md` |
| KC onset code (innate) | ✅ | AC_onset 0.85–0.99 (onset window 4,5); AC≥0.85 at N=6 | `stable_readout_gpu_results.md` §TL;DR/B1; `address_release_synthesis.md` §3 |
| Hearing: rhythm→wing_motor | ✅ | FFT 9.6–19.1, 3/3 seeds; sep 0.82–1.08 | `e8b_results.md` §1.1, §4 |
| Hearing: pC1 | ✅ | SFA=0+d4200/r800 → 3/3 seeds, up to 140 sp | `L2_sensors_map.md` §9 |
| Taste: relative valence | ✅ | 6/8 seeds into opposite poles; A/A=0 | `taste_final_n7.md` §1.4 |
| Taste: absolute valence | ❌ | 50/50 (3 approach / 3 avoid) | `taste_final_n7.md` §1.6 |
| Taste-v783: sugar valence | ✅ | 8/8 directed; MN9 8/8 | DEVLOG §99; `L2_sensors_map.md` §5.4 |
| Wind: ignition | ✅ | 10/16 = 62.5% (POP16) | `e7_wind.md` |
| Gravity | ✅ | L 484 / R 494 vs random 217±102 (z=2.6/2.7) | `minor_channels_results.md` §1 |
| Proprioception | ✅ | ratio 25–50×; flex/ext co-activation | `minor_channels_results.md` §2 |
| Hygro/thermo | 🟡 | vp1m+VP2 thermo/hygro 1.23→1.38 | `minor_channels_results.md` §3 |
| Mechano-tactile | ✅ | ascending 14,344; 3/3 seeds; control 0 | `L2_sensors_map.md` §3.2 |
| Nociception | ❌ | 0 annotation matches | `L2_sensors_map.md` §3.3 |
| N10 null of sensory | ✅ | sensory pathways disintegrate in nulls | `n10_n6_results.md`; `L2_sensors_map.md` §6 |

---
---

# Chapter L3. Working memory and the central complex

> Layer L3 = the dynamic carrier of information and the central complex (CX). Question: can the network **hold** information in activity, **read** it and **control** it — and what is the motor output.
> Sources: `docs/working-memory-report.md`, `docs/wm_v783_results.md`, `docs/wm_alignment.md`, `docs/wm_wells_map.md`, `docs/wm_stability.md`, `docs/wm_rotation.md`, `docs/wm_steering.md`, `docs/cx_wta_pgated.md`, `docs/cx_arbiter.md`, `docs/evidence_results.md`, `docs/evidence_accumulator_design.md`, `docs/n378_results.md`, `docs/n13141819_results.md`, `DEVLOG §38–44, §96–98, §100`.
> All numbers — from the listed documents, with reference (file §section / DEVLOG §); no unverifiable markers (disputed ones checked 29.09.2026).

Final assessment of the layer (29.09.2026): **L3 — ~97%** (`understanding_final_map.md` §L3/FINAL). Working memory is built and closed on MaleCNS, replicated on v783 with 100% persistence; the motor output reads the held information.

---

## 3.1. The EPG ring: ordering

- **Spectral embedding (v1):** 50 EPG ordered by a proximity graph (Δ7/PEN/PEG + direct EPG→EPG). Validation: neighbours/random pairs = **3.2×**, neighbours/half-ring = 7.0×, closure last–first of the same order — **the ring is closed** (DEVLOG §38).
- **Orphan fix (v2, canon `data/epg_ring_order_v2.json`):** the connectome orphans (26, 30, 45, 46; poorly reconstructed — the max partner is 3–4× below the median) tore apart strong pairs (for example 44→47, weight 25,602). Embedding without the orphans (46 neurons) + post-hoc insertion of the 4 orphans at their strongest partners restored the chains `43→44→47→48`; mean neighbour weight 25,767 (clean-46: 28,819). Both "unfixable" failures of the sector 40–49 were healed (DEVLOG §39–40).
- **Ordering rule (for future topologies):** exclude poorly reconstructed nodes from the spectral embedding, insert them post-hoc at their strongest partners (DEVLOG §40).
- **Ring non-uniformity measured** (`wm_alignment.md` §1): the mediated ±3 loop varies by **7.3×** (33k → 232k), ±1 by **10×**, the inhibitory input ER+PEN by **51×**; the holes — orphans 5, 13, 14, 45 and their neighbours 12, 44.

---

## 3.2. The bump: holding, specificity, readout

### Working mechanism
- **The bump works and is positionally specific.** Stimulation of 3 adjacent EPG (150 Hz, 200–500 ms) → a bump; examples: [10–12] → core 10–12, [25–27] → 25–27. **Holding 6 s** (target ≥2 s, exceeded ×3; plateau attractor, no decay; at SFA=0 — 100% core stability, seed-robust) (`working-memory-report.md`; `wm_stability.md`).
- **Readout by argmax:** 7/8 positions ≤2 (median 0.5); the centroid is worse (mean 7.0) — the asymmetric halo pulls the mean (DEVLOG §38.1).
- **Well map:** **47/50 positions hold** the bump under the v2/SFA=0 canon; 3 failures (11–13 — the orphan-seam zone) (`wm_wells_map.md`; DEVLOG §42).
- **Alignment:** `EPG_NORM=1 + INH_NORM=1 + ONLY_UP=1` → **10/10** tested positions, including the former failures 11/12/13 and the wells 0/37; validation **22/22 unique positions, 2 seeds**, 0 failures. `ONLY_UP=1` (fac = max(1, med/mass)) is mandatory: full normalization fixes one seam but breaks positions at the second (`wm_alignment.md` §5–6).
- **Determinism confirmed bitwise** (repeat = identical) (DEVLOG §39).

### Battle recipe of the step (MaleCNS)
```
Order: data/epg_ring_order_v2.json
Mode: PEN_NEG=0.6 + REC_GAIN=8 + E2D7=3 + SFA=0
Optional: EPG_NORM=1 + INH_NORM=1 + ONLY_UP=1
Stimulus: 3 adjacent EPG, 150 Hz, 200–500 ms → bump at the position
```

### Proven boundaries
- **Free rotation** (integration of angular velocity without landmarks) not achieved: no angular-velocity mechanism (CL inputs); the ring = a set of local wells of different depth, not a uniform line attractor. Deliberately deferred — Andrey has an idea for its use (`working-memory-report.md`; DEVLOG §42.1).
- **SFA blurs the bubble** — for memory SFA=0 is better (SFA is a tool of learning, not a dogma of memory) (DEVLOG §39).

---

## 3.3. Cross-connectome replication (FlyWire v783)

- **The v783 ring:** `epg_ring_v783.json`, `ring_clean` = **39 EPG** (12 orphans excluded from 51). Full `REC_GAIN` curve 8/9/10/11/12: gain=8 holds 19/39, **gain≥9 — 39/39**; the persistence threshold is between 8 and 9 (`wm_v783_results.md` §2).
- **Persistence ↔ positionality trade-off:** gain=9..12 give 39/39 holding, but positional accuracy falls (25→26→24→22 of 39); drift into **structural wells** (5 basins), not toward the orphan seams (§4).
- **Calibration by alignment:** full normalization (`ONLY_UP=0`, attacks the wells directly) + gain=10 → **hold 39/39, pos 33/39 (85%), mean_err 1.60** (base: 26/39, 2.37). Diagnosis: the drift is basin-based; on v783 only-up = no-op (the ring is structurally 3× more even than MaleCNS) (§8; DEVLOG §96).
- **R3 final:** per-position gain {9,10,11,12} (map {2→9, 3→12, 4→11, 5→11, 6→9, 10→9, 11→12, 29→12, 30→11}) **+ ONLY_UP=0** → **39/39 positional accuracy on BOTH seeds** (mean_err 1.17 / 1.03; hold 39/39, ≥3.75 s) (§9–11; DEVLOG §99).
- **R2 (N10-null on v783):** degree-preserving nulls collapse — real hold **4673/4637** spikes, null = **0/0** (stimulus response ×10 weaker). Topological constraint of working memory confirmed on a second connectome (§8.3; DEVLOG §96).
- **Drag on v783:** short/medium trajectories PASS (frac≤3 = 1.0), the long 15-step one — a boundary (0.714, lag at the start because of an attractor well); slowing/amplification do not help (§12; DEVLOG §101).

**Conclusion:** the EPG ring is a working dynamic carrier on two connectomes; different connectomes require different calibrations (v783 is more even than MaleCNS, but its wells are stronger) — an important fact for cross-connectome transferability (frontier F2).

---

## 3.4. Motor output: compass → steering (EPG→PFL3→DNa02)

- **The path found in the connectome:** EPG → PFL3 (113 edges) → DNa02 (**24 edges = the bottleneck**) — the classic steering vertex circuit. DNa02 = 2 steering neurons (graph indices; bodyId 523769/10360) (`circuit-map.md` §A; DEVLOG §41).
- **Tuning curve:** DNa02_L is modulated by the bump position, a bell-shaped curve with a peak (L−R up to **48 spikes**); over 8 positions the after-window L−R: +2/+20/**+48**/+38/+22/+24/+17/+15 (DEVLOG §41).
- **The push-pull is contralateral:** the connection is strictly contralateral (DNa02_L←PFL3_R, DNa02_R←PFL3_L); **preferred L ≈ position 16, R ≈ 40–44 (opposite, ≈ half a ring)**. In wild R is subthreshold (0–6 spikes); an amplification of PFL3→DNa02 ×3–5 opens a clean bell 40–44 (`wm_steering.md`; DEVLOG §44).
- **On v783 — reproduced:** DNa02 L≈16 / R≈40 (19/38–43 spikes) (19/38–43 spikes) — as on MaleCNS (DEVLOG §100).

**Significance:** the position held in the dynamics is read by the steering neurons — the first bridge "memory → action".

---

## 3.5. CX arbitration and multiplexing

- **The ring = winner-take-all for conflict resolution.** Two equal clusters (A=9–11, B=34–36) → one localized bump: the dominant component holds 88–99% of spikes, `WTA≥0.7` in **7/8 (87.5%)**. The swap control (A=35, B=10): the winner is position 35, **8/8, WTA=0.993** → the choice is determined by the **ring position**, not by the pool label/start order (`cx_wta_pgated.md` §1.2–1.3).
- **But it is not a strength comparator:** a ramp of the weak cluster's strength up to +50% switches the choice — no, instead of switching there is two-humped co-activation (WTA falls 0.93→0.29). The landscape is asymmetric, position 35 is the dominant well (§1.3).
- **Two bumps:** distant ones (10:25, 5:20) → WTA; adjacent ones (10:13) → merge. Multibump is not supported → **sequences = multiplexing** (DEVLOG §100).
- **The bump width of the substrate ~18–21 slots** (not ≤5, as the literal design criterion assumed) — the criterion was redefined for the substrate (§1.3).

**Caveat:** `cx_arbiter.md` (design, 20.09) formulated the CX as a conflict arbiter; the live test (`cx_wta_pgated.md`) refined its nature — WTA by position, not by strength.

---

## 3.6. Hysteresis, evidence and state

- **N19: the pre-stimulus state predicts the choice — 24/24.** `p_pre=L → L` (8/8), `p_pre=R → R` (8/8); the hysteresis of the bump predicts the subsequent choice (`n13141819_results.md` §N19; DEVLOG §82).
- **Evidence accumulator (N2 inverted):** a host-side saturating accumulator `A` (τ_A ≈ 5 s) on the onset detector of real ORN_DM1 spikes, injection of `g_FB·A` into the real pools `h∆K ∪ PFG` via `fly_set_stim_map()`. Result: `series8/strong8` = **1.80×** (N2 was 0.96–1.03), ramp rho = **+0.81** (N2 EPG: −0.45), threshold decision detector (latency series8=6 / strong8=None), persistence **τ ≈ 5 s**, all 3/3 seeds. Controls: `nofb`=0.99×, `noev`=0.99×, `sched` bit-for-bit = `pool`, baseline=0, noise does not accumulate (`evidence_results.md`).
  - **Boundaries:** P7 (`leakoff` ratio 1.86 instead of ≤1.1) showed that the ratio is determined by the **number of encounters**, not by the saturation of `A`; `A` is an invented host-side variable (a proxy), not core dynamics; the bit-for-bit N2 is limited by core drift (there is no map leakage — proven by a fresh run) (`evidence_results.md` §6, §10).
- **Hysteresis as the carrier of the choice** is consistent with the WTA landscape and the structural wells: the choice is not an instantaneous argmax, but a state of the network.

---

## 3.7. Proven boundaries of L3

1. **Erase / sequences:** the multistability of the ring — a new distant stimulus creates a second bump, the old one does not go out (coexistence of 0,1,2 and 6,7 with equal amplitude). M1 time-multiplexing without erase does not work; onset reading mitigates (10/10 on 2/4 seeds), but does not solve it (`token_ring_results.md` §2; DEVLOG §91).
2. **Free rotation:** no angular-velocity mechanism (CL inputs); the drag mode (a running stimulus) leads the bump, but without input the bump relaxes to its "home" well (`wm_rotation.md`; DEVLOG §42, §39).
3. **The v783 drift is basin-based:** 5 structural wells, not orphan seams; it is treated by per-position gain, but remains a geometric limit (`wm_v783_results.md` §4, §10).
4. **Capacity of a single ring:** the ring encodes **10 symbols** (single 10/10, 4/4 seeds), geometric limit ~15 (spacing ≥3); 50 symbols = 29–34 collisions (`token_ring_results.md` §1; `token_ring50_results.md` §2). This is already step L7 (behind the gate), but it shows the physical limit of the carrier.

---

## Summary table L3: phenomenon → status → numbers → source

| Phenomenon | Status | Numbers | Source |
|---|:---:|---|---|
| Ring ordering (neighbours) | ✅ | neighbours/random 3.2×; ring closed | DEVLOG §38; `ring_order.json` |
| Orphan fix v2 | ✅ | junction 43-44-47 restored; core 21/22/23 | DEVLOG §40; `epg_ring_order_v2.json` |
| Bump: holding | ✅ | 6 s (margin ×3); SFA=0 — 100% stability | `working-memory-report.md`; `wm_stability.md` |
| Bump: positional specificity | ✅ | [10-12]→10-12; [25-27]→25-27 | DEVLOG §38 |
| Readout argmax | ✅ | 7/8 ≤2 (median 0.5) | `working-memory-report.md` |
| Well map | ✅ | 47/50 hold (v2/SFA=0) | `wm_wells_map.md`; DEVLOG §42 |
| Alignment (only-up) | ✅ | 10/10 test, 22/22 validation, 2 seeds | `wm_alignment.md` §5–6 |
| v783 ring: holding | ✅ | gain≥9 → 39/39; gain=8 → 19/39 | `wm_v783_results.md` §2 |
| v783 ring: R3 final | ✅ | 39/39 pos. on both seeds; mean_err 1.17/1.03 | `wm_v783_results.md` §11; DEVLOG §99 |
| N10-null on v783 (R2) | ✅ | real 4673/4637 vs null 0/0 | `wm_v783_results.md` §8.3 |
| Motor output DNa02 | ✅ | preferred L≈16 / R≈40–44; L−R up to 48 sp; 24 edges | `wm_steering.md`; DEVLOG §41, §44 |
| DNa02 on v783 | ✅ | L≈16 / R≈40 (19/38–43 sp) | DEVLOG §100 |
| CX WTA (two clusters) | 🟡 | WTA 7/8 (87.5%); swap 8/8, 0.993; strength does not switch | `cx_wta_pgated.md` §1.2–1.3 |
| Two bumps → multiplex | ❌ | distant WTA, adjacent merge | DEVLOG §100 |
| Hysteresis → choice (N19) | ✅ | 24/24 (pre=L→L 8/8, pre=R→R 8/8) | `n13141819_results.md` §N19 |
| Evidence accumulator (N2) | ✅ | 1.80×; rho +0.81; τ≈5 s; 3/3 seeds; nofb/noev 0.99 | `evidence_results.md` |
| Drag rotation (MaleCNS) | ✅ | bump is led 25→36→35→42→47 | `wm_rotation.md`; DEVLOG §42 |
| Drag v783 (long) | 🟡 | short/medium 1.0; long 0.714 | `wm_v783_results.md` §12 |
| Free rotation | ❌ | no angular-velocity mechanism (CL) | `working-memory-report.md` §Limitations |
| Erase/sequences | ❌ | multi plain 1–3/10; ring multistable | `token_ring_results.md` §2 |

---

## Summary of L2–L3

- **L2 (~90%):** 9 main channels; working cleanly — olfaction, vision (loom/escape), wind, gravity, proprioception, mechano-tactile; hearing closed (pC1 reachable); thermo/hygro — partially (a targeted channel); taste — relative valence exists, absolute = a proven boundary of physiology; nociception — a data boundary. The sensory response is topologically constrained (N10).
- **L3 (~97%):** the dynamic carrier is built — the EPG ring with a bump ≥6 s, positionally specific and readable; the DNa02 motor push-pull is closed on both connectomes; CX = a positional WTA arbiter, not a strength comparator; hysteresis predicts the choice; the evidence accumulator inverts N2. Open boundaries: erase (for sequences), free rotation, basin drift on v783 — all documented as boundaries, not gaps of non-understanding.


---

# Chapter 3. Learning and internal states (L4–L5)

**Part of the master monograph "Understanding the Drosophila brain". Overview chapter.**
**Assembly date:** 29.09.2026. **Substrate:** MaleCNS v1.0 (166,700 neurons / 25.6M connections), replicates — FlyWire v783 (138,639 neurons / 15,091,983 edges).
**Source rule:** every number — with a reference `file §section` or `DEVLOG §`. Where the source does not give a number or there is a discrepancy between documents — the marker **[VERIFY]**.
**Cross-cutting law of the chapter** (`docs/understanding_final_map.md` §0/§"Consolidated law"): **plasticity is strong; the bottleneck is the carrier of the representation and its *spiking* release; the working output of the substrate is the PSP channel.**

---

# L4. Learning and memory: what the substrate can learn

## L4.0. Summary

Associative "odour → reward" learning in the substrate is **reproducible and specific**, starting from the E9d breakthrough (20.09.2026). KC→MBON depression by the DAN rule (the community formula of Fly's Table) gives a specificity of **SPEC 1.73 [1.25; 2.20], 6/6 seeds** with clean controls; the memory is stored in the weights (AC 0.72–0.78), consolidated (LTM v2.5, ratio 1.737) and erased (Rac1, decay 1.0 → 0.0000). One association is written **one-shot** (N=1), a context tag is written into the plasticity address.

But everything that is learned is **not released into the spiking activity of MBON**: the learned delta drowns in the intrinsic homeostasis of the population dynamics. This is the **release block** — a proven boundary of the LIF physiology of the substrate, not a defect of the rule. Seven independent release mechanisms were refuted with pre-registrations; the working output is the **PSP channel** (`AC_drive` 0.97–0.99), legitimate in fly biology (graded, non-spiking neurons).

Two lines run into one boundary: **composition/transitivity** (L4) and **state specificity** (L5) are closed as proven boundaries — KC→MBON depression is an operation on the overlap of KC codes, and "directionality" does not arise in it under any postsynaptic addressing.

## L4.1. Methodology of measuring learning

Before listing the mechanisms, let us fix the toolkit — without it the numbers are unreadable (metric pitfalls cost the project several false "canons").

**Protocol-canon E9d** (`docs/e9d_recalibration.md` §5):

```
Odours:     A = ORN_DM1 (reward), B = ORN_VA1v (control)
Training:   η = 0.5, TRAIN_N = 10 segments (KC→MBON depression)
SFA:        type-specific fine §3 (FLY_SFA_TYPES=1, FLY_SFA_TYPES_FINE=1, FLY_SFA=0)
APL fix:    FLY_APL_GAIN = 3.5 (KC sparsification to 5.6–8.5% active, overlap 65%)
Metric:     SPEC = ratioM = mean_r shAe_r / mean_r shBe_r
            shXe_r = ‖post−pre‖/‖pre‖, window (4,14) = 200–700 ms, only 50 excitatory MBON
Realizations: NTEST = 6
```

**Key methodological facts:**

| Fact | Number | Source |
|---|---|---|
| A window outside the attractor tail is mandatory | full window (0,18) gives 0.26–2.66 on 8 seeds; the stimulus window (4,14) — stable | `e9d_recalibration.md` §3 |
| `mean of ratios` explodes as `shBe→0` | up to 5·10¹⁰ → use `ratioM` (ratio of means) | `e9d_recalibration.md` §8 (pitfall 1) |
| NTEST < 6 is unstable | NTEST=2 on seed 12345 gives 0.28 vs 2.14 at NTEST=6 | `e9d_recalibration.md` §8 (pitfall 2) |
| The old "canon" on (4,8) was a denominator artefact | 7/8 by mean-of-ratios vs 1/8 by ratioM | `e9d_recalibration.md` §8 (pitfall 3) |
| Fine-SFA preserves the working-memory bump, global SFA destroys it | bump ≥3 s as with SFA=0 under fine; under global 0.25 centroid 47 — fire | `e9d_recalibration.md` §6.1 |

**Type-specific SFA map (fine §3):** G0 = 0 (CX/DAN/MBON/APL/TuBu), G1 (KC) = 0.08/τ300, G2 (echo) = 0.20/250, G3 (sensory/motor) = 0.35/150 (`docs/sfa_types.md` §3; `understanding_final_map.md` §L1). The carrier of the effect is not the η step but the physiology itself: under fine, specificity is reproducible **at all η ∈ {0.05…0.5}** (`e9d_recalibration.md` §7).

> **Reconciliation of SPEC aggregates (the discrepancy is confirmed by the sources).** The three numbers come from **different protocols** and must not be mixed. (1) `learning-stage-report.md` §"Numbers (8 seeds)": mean SPEC all **1.50** / exc **1.63** on 8 seeds (the "breakthrough" recipe of 20.09, global SFA 0.25, window (4,8)); (2) `e9d_recalibration.md` §5: the new canon **1.73 [1.25; 2.20] on 6 seeds** (fine-SFA §3, window (4,14), `ratioM`); (3) `address_release_synthesis.md` §8 item 1 directly records the discrepancy — a recomputation from the saved `e9d_recal` JSON under a different aggregation gives `spec_exc ≈ 0.90–0.96`. For comparisons, **recompute from the raw patterns with an explicit protocol**; give canonical references with an indication of the seed/window/SFA map.

## L4.2. The E9d breakthrough: reproducible specific learning

**Mechanics.** DAN depresses KC→MBON: in the reward segments, all edges from active KC are multiplied by (1−η); requantize of 61,210 plastic edges (~0.001 s). The core is untouched except for SFA wave 4 (`learning-stage-report.md` §"Winning regime").

**Working recipe (20.09):** `FLY_APL_GAIN=3.5`, `FLY_SFA=0.25`, `FLY_SFA_TAU=300`, `FLY_DAN_ETA=0.5`, `FLY_E9_TRAIN_N=10`, odours A=DM1 (rewarded), B=VA1v (control) (`learning-stage-report.md`).

**Numbers:**

| Metric | Value | Source |
|---|---|---|
| SPEC (fine-SFA, window 4,14, 6 seeds) | **1.73 [1.25; 2.20], 6/6** | `e9d_recalibration.md` §5 |
| SPEC (recipe of 20.09, 8 seeds) | all **1.50**, exc **1.63** | `learning-stage-report.md` §Numbers |
| no-reward control | **0.000**, changed=0 | `e9d_recalibration.md` §5; `learning-stage-report.md` |
| random-KC control | **0.99 [0.69; 1.49]**, 1/6 >1.2; active>random 6/6, **p=0.031** | `e9d_recalibration.md` §5 |
| Dose–response η | specificity at all η ≥ 0.05 (16 runs); no monotonic curve | `learning-stage-report.md` §Remainder |
| Recipe validation by pair Jaccard | reliable at J ≤ 0.3 (DM1/VA1v 8/8); at J=0.37 — "hit or miss" | `learning-stage-report.md` §Remainder |
| Aversive plasticity (PPL1) | target_mask PPL1→attraction-MBON; specificity **6/6** | `aversive_design.md`; DEVLOG §53 |
| Rac1 forgetting (`FLY_E9_DECAY`) | 0.25→0.077, 0.5→0.066, **1.0→0.0000** | `learning-stage-report.md` §Remainder |
| Eligibility trace | P=500 ms shift 0.000→**0.217** (τ=1000); also works in vision | `n13141819_results.md` §N14; `understanding_final_map.md` §L4 |

**Why SFA was needed.** Before it, learning "drowned": `attractor-learning-blocker.md` showed that under bistable dynamics, subtle synaptic changes do not govern the output at all. The reference experiment E9c-final (4 seeds, η=0.5×10, DM1/VA1v): the SPEC-ratio jumps randomly **0.58–3.31×**, ΔA ±16% in both directions (`attractor-learning-blocker.md` §4). The DAN mechanics were meanwhile ideal: no-reward shift=0, random-KC 7× weaker (`attractor-learning-blocker.md`).

## L4.3. Attractor bistability — the root of the community negatives

**Thesis.** In a spiking network with attractor bistability, the output is governed by which "well" the network falls into, not by a subtle input; any learning rule is non-falsifiable while the dynamics are bistable (`attractor-learning-blocker.md` §Thesis).

**Evidence base:**

| Experiment | Result | Source |
|---|---|---|
| E4: 24 points (inh_gain × rmax) | **no transient in any of them** — only an "eternal attractor" or silence; the boundary — a ragged zone inh 1.1–1.3 | `attractor-learning-blocker.md` §1 |
| inh=1.0 / ≥1.5 | always fires (42–55k spikes/segment) / always silent | ibid. |
| E7 wind: 100 ms pulse | **bimodal trigger** of ignition (50% POP4 / 62.5% POP16 flare into the attractor) | ibid. §2 |
| E9c: learning | SPEC 0.58–3.31×, ΔA ±16% — not reproducible | ibid. §4 |
| Formula of the problem | `|signal| (≈1–16%) ≲ |attractor noise| (up to 15%)` | ibid. §4 |

**Reconciliation with the community** (`attractor-learning-blocker.md` §"Reconciliation"): alzweidi, fly-brain-bench, fly-brain-poker, DOOMFLY/Stonkfly/FlyTV — all negatives are explained by a single cause; the only success (Fly's Table) — a static PN→KC→MBON network without spiking recurrent dynamics, k-WTA in software. **Their success = the absence of our problem.**

**N10 null control (topological constraint).** Degree-preserving nulls (`tools/n10_null.py`) — a connectome with preserved degrees/weights but shuffled connections:

| Probe | Real | Null | Source |
|---|---|---|---|
| E9d learning | SPEC reproducible | **0.000** (3/3 realizations) | `n10_n6_results.md` §0 |
| WM bump (hold) | 4673/4637 spikes | **0/0** (collapse) | `wm_v783_results.md` §8.3; `n10_n6_results.md` |
| flybench core | 1.000 | **0.500** | `n10_n6_results.md` §1c |
| CPG (E1–E2–I1) | rhythm | **0 spikes**, rhythmic_votes 0/3 | `n10_n6_results.md` §1d |

**Conclusion:** the functions studied are **constrained by the topology of the connectome**, and not by trivial activity or a random network. This is one of the strongest scientific results of the project (negative controls on all mechanisms).

## L4.4. Consolidation and long-term memory (LTM)

**Stage path** (`ltm_v25_results.md`, DEVLOG §72–74):

| Version | What | Verdict |
|---|---|---|
| LTM v1 | `w_fast + d_slow`, 8/8 unit | ❌ GPU: runaway, `w_pl<0`, sign inversion n_neg 237–284/seed | DEVLOG §72 |
| LTM v2 | protected share of the depression `D_prot/D_lab/D_seen`, `w_pl ≥ 0` by construction, idempotent consolidation | 🟡 mechanism ✅, magnitude ❌: ratio 1.178 (A saturation at η=0.5) | DEVLOG §73 |
| **LTM v2.5** | unsaturated regime `η=0.1`, `IF_ROUNDS=3` | ✅ **PASS** | `ltm_v25_results.md` |

**LTM v2.5 — pre-registration (8 seeds):**

| # | Criterion | Result | Verdict |
|---|---|---|---|
| P1 | `ratio(A∩B) ≥ 1.5`, ≥6/8 seeds | mean **1.737**, med 1.711, **7/8 ≥1.5**, sign 8/8 (p=0.0039) | ✅ |
| P2 | `n_neg=0`, `w_pl≥0` | everywhere | ✅ |
| P3 | `spec_exc` retest ≥ 0.9× OFF | 1.006 | ✅ |
| P4 | edge addressing (matched random-B) | 1.038 (4/4) | ✅ |
| P5 | no-reward clean | changed=0, a_surv=0 | ✅ |

**Meaning:** consolidation **protects** the learned association from being overwritten by new experience (A survives after interference B) — this is a mechanism of long-term memory, not merely a "freezing of weights". The fact is **systematically below theory** (fact 1.737 vs theory 2.150 = 0.81×; effective protection share `c_eff≈0.52`) — a refinement of the carrier, not a regression (`ltm_v25_results.md` §4).

**Extinction:** Rac1 forgetting with a decay parameter — 0.25 → 0.077, 0.5 → 0.066, **1.0 → 0.0000** (complete erasure) (`learning-stage-report.md` §Remainder).

**Limitations of LTM:** consolidation does not create **composition** and does not help the **behavioural** readout (consolidation H2 v2 ❌ — the same attractor blocker, DEVLOG §52).

## L4.5. Three levels of representation and the release block

**Separated levels** (`address_release_synthesis.md` §7; DEVLOG §72):

| Level | What is read | Metric | Value | Status |
|---|---|---|---|---|
| 1. Innate representability | onset KC code / raw MBON | AC_onset | **0.85–0.99** | ✅ (symbol alphabet) |
| 2. Memory in the weights | Δw KC→MBON | AC_weights | **0.72–0.78** (chance 0.25), cos seed +0.53 | ✅ stored |
| 2b. Consolidation (LTM) | protected share of the depression | ratio | **1.737** | ✅ |
| 3a. PSP (linear input) | `Σ w·a` per-segment | AC_drive | **0.969–0.990**, no-reward 0.250 | ✅ available |
| 3b. Spiking output | MBON shift | AC_shift | **0.31** (canon) / 0.40 (align) / 0.55–0.64 (best pool) | ❌ blocked |

**The main fact — the funnel gap between 3a and 3b:** the learned delta is lost (`corr(input, output)=0.014–0.067`, `‖pred‖/‖obs‖ ~10⁴–10⁵`), even though the **innate** code passes at 3b (raw post-ON 0.84–0.99). Short formula: **weights 0.72–0.78 → PSP 0.97–0.99 → spikes 0.31–0.64.**

### Seven release mechanisms — all refuted

The blocker is localised in the **intrinsic homeostasis of exc-MBON** (its own set-point; 6–7 of 50 active, inter-odour cos **0.988**) — not in the synapses and not in the amplitude (`address_release_synthesis.md` §3).

| # | Mechanism / hypothesis | Key number | Verdict | Source |
|---|---|---|---|---|
| 1 | Write/read mismatch (depression on the plateau, identity at onset) | onset-only KC 0.709 → 0.028 (fixed), no release | ✅ not the root | `address_release_results.md` §6 |
| 2 | Amplitude of Δw (gain κ) | κ∈{2,4,8} non-monotonic: 0.396/0.365/0.339/0.406 | ❌ not a lever | `address_release_v2_results.md` §2 |
| 3 | Removal of inhibitory loops (MBON→MBON 9,181 syn, lateral 46,413, APL 542) | sil_lateral 0.406 / sil_all 0.240 (base 0.396); APL removal → **×28–32 spikes** | ❌ FAIL/kill | `address_release_v2_results.md` §5 |
| 4 | Per-MBON fine-SFA | best 0.432 at base 0.375; exc vs inh p=0.388 | ❌ FAIL | `address_release_v3_results.md` §3 |
| 5 | Carrier targeting (MBON01/11) | carrier_pot 0.547 < base 0.635 (p=0.028 worse); shuf 0.667 | ❌ refuted | `address_release_v4_results.md` §3 |
| 6 | per-MBON `vThreshold` (release_v4) | S1 ✅ (ac_ab 8/8, mean 0.979), **S2 ❌** (ac_bc 0.76 at chance 0.5 — untrained odours are separated equally well) | ❌ FAIL-specific | `release_v4_results.md`; `address_release_synthesis.md` §11.1 |
| 7 | Temporal window | scan of all 171 windows: no window passes the strict criterion (max 5/8); the specific component is transient and small (asym(4,6) +0.385, sign 8/8 p=0.008) | ❌ no specificity windows | `release_window_map.md`; `address_release_synthesis.md` §11.2 |

**Biological interpretation** (`address_release_synthesis.md` §4): the block may not be a defect of the model but a simplification of our readout — a living fly reads learning **compartmentally and contextually**, not as a scalar sum over 97 MBON. Homeostasis = protection of the network from having its behaviour overwritten by every association. **A testable form:** if, after switching to a per-compartment readout, release specificity reproduces on ≥8 seeds — the block was an artefact of the global readout; if not — homeostasis is fundamental and the output is built through PSP/DN.

## L4.6. The PSP channel — a legitimate output of the substrate

**Fact.** The learned association is present in the postsynaptic input of MBON (`Σ w·a`): `AC_drive = 0.969–0.990` (all97/exc50), 0.844 (act6), no-reward 0.250 (`address_release_synthesis.md` §5a/§7; `targeted_readout_results.md` §4).

**Andrey's decision (29.09.2026):** **Option A — the PSP channel is adopted** as the working output of the substrate for the token stage, with the directive to "finish understanding WITHOUT CHANGING the essence of the project" (preserve scientific character) (MEMORY, 29.09.2026).

**Mandatory honest labelling:** PSP is *not spiking output* (`address_release_synthesis.md` §6). It is a linear functional of the weights, not a proof that the network uses the address in its dynamics; but it is immediately available and reads precisely the **learned** association (unlike the innate onset). Biologically justified: Drosophila has a mass of graded non-spiking neurons, and PSP is an observable quantity.

## L4.7. Per-compartment readout: the correct reading frame

**Confirmation of the hypothesis of §4.** The MB compartment annotation **was in the data**: the field `instance` of the form `MBON11(y1pedc>a/B)_L`, filled for **97/97** MBON, **32 compartments**, 37 types (attraction 26 types/71 bodies, aversion 11/26) (`per_compartment_readout.md` §0/§1). The earlier F1 A3 claim "no annotation" is outdated (it tested `type`, not `instance`).

| readout (spikes, onset) | Axes | AC | Source |
|---|---|---|---|
| `shiftonset_exc` (global baseline) | 50 | 0.396 | `per_compartment_readout.md` §4.2 |
| `valence_shiftonset` (attraction/aversion) | 2 | 0.458 | ibid. |
| **`comp_shiftonset_sum`** | **32 compartments** | **0.635 ± 0.035** | ibid. |
| 100 random groupings (control) | 32 | mean 0.538 ± 0.027, max 0.604 | ibid. |
| `w_comp_mean` (weight space) | 32 | **0.969** (no-reward 0.250) | ibid. §4.5 |

**Result:** per-compartment readout = **+0.240** over the global one (8/8 seeds, p<10⁻⁴), above **all** 100 random groupings. But this is still **readout, not release**, and it **does not reach the release threshold of 0.75** (`per_compartment_readout.md` §4.7, §0 item 9). Honest caveat: ≈60% of the gain comes from the coarsening of the groups itself (0.396→0.538), and only ~0.10 from the biological layout.

## L4.8. Paradigm P1–P4: answers to Andrey's four questions

| # | Question | Verdict | Key numbers | Source |
|---|---|---|---|---|
| **P1** | Capacity | ✅ **≈2 "words"** | N*=2 (SPEC 6/8, AC 8/8); N=4 — failure (1/8, 0/8). Bottleneck = stochastic MBON readout, **not** DAN weights. Structural prescreen of odours is forbidden (r=−0.136) | `paradigm_p1_results.md` §0; DEVLOG §71 |
| **P2a** | Self-organization | ❌ **none** | ρ=0.94 = its own cross-seed, ΔNMI=0, 8/8; positive control (weights shifted by reward do not move the RSM) | MEMORY 25.09; DEVLOG §79 |
| **P2b** | BCM/Hebbian without a rule | ❌ **no categories** | the rule works (Σ|dw| up to 25.5 (52–72 edges)), but `dNMI_w ≈ 0` (25–38% trivial seeds) | `p2b_results.md` §7 |
| **P3** | Semantics on MBON | ❌ **no metric one** | Mantel r=−0.089/+0.003, 0/8; but 1D interpolation B→A exists (ρ 0.64–0.78, 6–8/8) | `paradigm_p3_results.md` §0 |
| **P4** | Speed of acquisition | ✅ **one-shot** | SPEC flat N=1→10 (1.93→1.60), knee=1, 6/8 >1.2; but the random-KC canon is not zero (3.69, 7/8) — specificity at N=1 is not verified | `paradigm_p4_results.md` §0 |

**Cross-cutting conclusion of the paradigm** (DEVLOG §71): plasticity is strong (one-shot, address-specific), the bottleneck is the **carrier of the representation and the behavioural threshold**, not the readout and not the weights. The symbol dictionary is ready (level 1); "word→meaning" is stored (level 2); the task is to release the address into activity (level 3).

## L4.9. Composition and transitivity: a proven boundary (the V2 class)

**Mechanical theorem** (`transitive_inference_design.md` §1.0): any current plasticity is a **per-KC multiplier** `w_ij ← w_ij·g_i`, where `g_i` depends only on the presynaptic KC. Corollary: learning is symmetric and determined only by the overlap of KC; directionality (A→B ≠ B→A) is not encoded. The theorem makes the experiment a sharp test rather than guesswork.

**Three independent addressing schemes — all negative:**

| Level of addressing | Result | Source |
|---|---|---|
| v1 ungated (all KC→MBON) | ❌ theorem: MCI chain = nochain = model = **+0.994**; noreward = 0 | `transitive_v2_results.md` §5; DEVLOG §100 |
| Odour signature of compartments | ❌ data-kill: the signatures do not exist (cos 0.89–0.9998, C has zero differential compartments) | `transitive_v2_results.md` §1 |
| Anatomical lobe-split (V2-val) | ❌ P0 FAIL: excess(chain)−excess(nochain) mean **+79.8k**, CI [+34.8k; +122.7k] — significantly **against**; 1/8 | `transitive_v2_results.md` §3 |

**V2-val kills — all clean:** noreward changed=0 (8/8); reverse≡chain bit-for-bit (8/8 — determinism); dep_αβ(chain)≡dep_αβ(nochain) bit-for-bit; dep(calyx)=0 (8/8) (`transitive_v2_results.md` §2).

**Fundamental conclusion:** KC→MBON depression is an **operation on the overlap of KC codes**; "directionality" does not arise from any addressing by postsynaptic target. Composition requires a mechanism of a different class (post-gated plasticity OR recurrent dynamics that read the weights non-trivially) — both branches were closed earlier (release block, P2a/P2b). L4 composition is fixed as a **proven boundary**, not a mechanism (DEVLOG §103).

## L4.10. Summary table L4: phenomenon → status → numbers → source

| Phenomenon | Status | Numbers | Source |
|---|:---:|---|---|
| DAN depression KC→MBON (fine-SFA canon) | ✅ | SPEC 1.73 [1.25; 2.20], 6/6; no-reward 0.000; random 0.99 | `e9d_recalibration.md` §5 |
| Old canon (global SFA 0.25) | ❌ | 0.97 [0.65; 1.20], 1/8 | `e9d_recalibration.md` §3 |
| Aversive plasticity (PPL1) | ✅ | target_mask; specificity 6/6 | DEVLOG §53 |
| Rac1 forgetting | ✅ | decay 1.0 → 0.0000 | `learning-stage-report.md` |
| Eligibility trace | ✅ | shift 0.000→0.217 (τ=1000); also works in vision | `n13141819_results.md` §N14 |
| Potentiation (attraction) | 🟡 | unit 7/7; E9p 6/6 at η≤0.02; confound ratioM | DEVLOG §68 |
| Consolidation LTM v2.5 | ✅ | ratio **1.737**, 7/8≥1.5, p=0.0039 | `ltm_v25_results.md` §0 |
| Attractor bistability | boundary | E4: no transient in 24 points; zone inh 1.1–1.3 | `attractor-learning-blocker.md` |
| N10-null (topology) | ✅ | E9d null 0.000 (3/3); WM 0/0; flybench 0.500; CPG 0 | `n10_n6_results.md` |
| Memory in the weights | ✅ | AC_weights 0.72–0.78 (chance 0.25) | DEVLOG §72 |
| Spiking release of the address | ❌ | AC_shift 0.31–0.64; corr(in,out)=0.014–0.067 | `address_release_synthesis.md` §1 |
| Release mechanisms (7) | ❌ | all refuted with pre-registrations | `address_release_synthesis.md` §2/§11 |
| PSP channel | ✅ | AC_drive 0.969–0.990; no-reward 0.250 | `address_release_synthesis.md` §5a |
| Per-compartment readout | ✅ | comp 0.635 vs exc 0.396 (+0.240, 8/8) | `per_compartment_readout.md` §4.2 |
| Capacity (P1) | ✅ | ≈2 words; N*=2; bottleneck = readout | `paradigm_p1_results.md` §0 |
| Self-organization (P2a/P2b) | ❌ | ΔNMI≈0; rule moves weights (Σ|dw| up to 25.5, 52–72 edges), no categories | `p2b_results.md` §7 |
| Semantics (P3) | ❌ | Mantel ≈0, 0/8; 1D interpolation exists | `paradigm_p3_results.md` §0 |
| One-shot (P4) | ✅ | knee=1, 6/8; random confound | `paradigm_p4_results.md` §0 |
| Composition/transitivity (V2) | ❌ boundary | MCI +0.994; V2-val P0 FAIL | `transitive_v2_results.md` |

## L4.11. Boundaries of L4 and remainder

**Proven boundaries (causes, not "unknowns"):**
1. **Address-release blocker** — fundamental: the intrinsic homeostasis of exc-MBON quenches the learned delta; removing all inhibition produces a ×28–32 fire, but the address does not come out (`address_release_synthesis.md` §3).
2. **Composition** — KC→MBON depression is an operation on the overlap; directionality does not arise under any postsynaptic addressing (`transitive_v2_results.md`).
3. **Self-organization** without a plasticity rule is absent (P2a/P2b); the only output is PSP.
4. **Learning on v783 is connectome-specific:** H1′ NOT accepted; random-KC ≥ active (1.63 vs 1.40) (`v783_e2e3_results.md` §3–5).
5. **Potentiation** — a narrow window η ≤ 0.02; headroom confounds ratioM.

**Remainder:** the PSP decision (adopted 29.09) → the token stage; potentiation quorum; R1 on v783 with the k-WTA recipe (H1′).

---

# L5. Internal states: hunger, sleep, clock, modulators

## L5.0. Summary

Internal states in the substrate **exist and physically change the network**: the hunger gate switches learning, the endogenous circadian oscillator sets a period of 24.00 CT-h with light entrainment, OA/5-HT move the activity regime, and the dopamine layer addresses valence. The state tag **is written into the plasticity address** (context-tag P1 24/24, Jaccard 0.19–0.49).

But **state-dependent retrieval does not reproduce**: the state-specific effects turn out to be **generic** (KC excitability) rather than tied to the trained odour; the spiking release of the learned address does not work. The diffuse carrier of the NPF projection (1.2% of the weight onto MBON) does not reach the olfactory readout. **Transfer of the V2 verdict:** targeted plasticity does not cure the generic effect — state specificity is a boundary, not a mechanism.

## L5.1. The hunger gate of learning: a switch, not a knob

**Mechanism.** The gate via `eta_eff = eta·(0.1 + 0.9·hunger)` — a hungry fly learns, a sated one does not (NPF analogue) (DEVLOG §56; `e9_hunger.md`).

**v3 — sNPF⁺ KC, threshold via `fly_set_vt_map`** (`hunger_gate_v3_results.md`):

| Criterion | Result | Verdict |
|---|---|---|
| P1: SPEC_hungry > SPEC_sated ×1.25 | **7/8** (ratios 1.04–2.51) | ✅ |
| P2: the effect is not in α′β′ (sNPF⁻) | **2/8** (α′β′ gives +27%) | ❌ |
| P4: the address changes | Jaccard(H,S) 0.113–0.159, 8/8 | ✅ |
| P7: SPEC_rand < SPEC_snpf ×0.9 | **4/8** (rand 1.445 vs snpf 1.489) | ❌ |
| Diagnosis | **Pearson(kc_pre, SPEC) = 0.948** — the effect depends linearly on the number of recruited KC, not on the subtype | — |
| Side positive | **threshold shift of KC releases the learned readout: AC 0.75→1.00** — the release block is bypassed by excitability | `hunger_gate_v3_results.md` §6 |

**v4 — paracrine arm (sNPF-R⁺ interneurons)** (`hunger_gate_v4_results.md`):

| Stage | Result |
|---|---|
| Original v4 (κ=5) | Pool specificity on non-degenerate seeds (P1 6/6, ratios 1.66–4.04), but **P6 fires** (κ=5 spoils the innate code: onsetraw 0.792); the metric is degenerate at `shBe=0` |
| κ-map (v4b, κ∈{1,2,3,5}) | κ=1: code intact (P6 7/8), but no effect (P1/P2 3/8); κ=2: P1 6/7, but **P2 2/7 (target≈rand)** → GENERIC; κ=3: code blurred; κ=5: metric artefact |
| **Outcome** | **at no κ is there specificity** — the paracrine arm is closed (3rd generic negative) |

**Conclusion L5.1** (`hunger_gate_v3_results.md` §10): the hypothesis "sNPF⁺ KC — a specific substrate of the hunger gate" is not confirmed; the effect = **generic arousal/excitation**, available to any sufficiently large pool. The hunger gate is a **switch, not a knob**: with sufficient depression, the MBON counter hits the floor, and a further decrease of the gate does not change the readout (`statevec_results.md` §2).

## L5.2. Endogenous circadian rhythm (the D5 rematch)

**Implementation** (`circadian_results.md`; the core is untouched, `fly_set_stim_map`): a **van der Pol** oscillator (μ=0.5, ω=0.302198, RK4), a `CircadianClock` with isochronous phase and a PRC, tied to the real s/l-LNv pools.

| Block | Result | Source |
|---|---|---|
| C1: free run | period **24.00 CT-h** (host) / 23.67–24.11 (network), r_ratio=1.0, AC 0.64; g=0 control flat | `circadian_results.md` §2 |
| C2: PRC | CT14 **−1.80 h** (delay), CT22 **+1.80** (advance), CT6 0.00 | §3.1 |
| C2: entrainment LD 12:12 | T=23 and T=25 entrained (slope 0.000 h/day); phase angles +1.72/−1.13 h | §3.2 |
| C3: sleep gate | night n_changed=**0**, day 243–334, cos(day2,night2)=**1.000** | §4 |
| C3 control `FLY_CIR=0` | bit-for-bit to the original | §4.1 |
| C4: coupling to pools | s-LNv peak CM=**CT1**; l-LNv **bimodal** (CT1 + CT8–9), 3/3; light input confirmed | §5 |
| D5 ("no endogenous rhythm") | **REVERSED** | §6 |

**Key pitfall** (`circadian_results.md` §1.1): `atan2(−y/ω, x)` gives a non-uniform phase → the phase PRC loses stability (entrainment "thrashed" for ~14 days). The fix is an **isochronous phase** `φ = 2π·(t/T)`. Biological context: there is no molecular oscillator (per/tim/clk) in the connectome → host-side by definition.

**Boundaries:** PRC edges (CT16 −3.78, CT18 +5.53) are an artefact of the phase feedback; the strict phase-angle threshold <1 h is not met (a biologically normal phase angle of entrainment); s/l-LNv is a proxy (see L5.3).

## L5.3. Internal state vector (E / NPF / Dilp / φ)

**Implementation** `tools/statevec.py` (host-side, the core is not modified):

```
E     — energy         dE/dt    = -k_met·(1+act) + food·k_food
NPF   — hunger drive   dNPF/dt  = (NPF0−NPF)/tau_N − alpha·Dilp + beta·(1−E)
Dilp  — satiety        dDilp/dt = (Dilp0−Dilp)/tau_I + gamma·E − delta·dh44
phi   — phase of day   gate eta_eff = eta·(0.1+0.9·clamp(NPF))·sleep_gate(phi)
```

**Results** (`statevec_results.md`):

| Block | Verdict | Numbers |
|---|---|---|
| Implementation + bit-for-bit `FLY_NP_GATE=0` | ✅ | SPEC_exc 1.959546 = canon; changed 243 |
| E-NP1 (satiety closes learning) | ⚠️ PARTIAL | criteria 1,2,5 ✅; criterion 4 ❌ (1/3 exc) |
| E-NP2 (clock sleep gate) | ✅ PASS | night n_changed=0, day 243/254; cos=1.000 |
| DH44→IPC (inhibition of Dilp) | ✅ PASS | dh44=1.0 fully suppresses Dilp; NPF high |
| **s/l-LNv as clock neurons** | ❌ **not confirmed** | `superclass` = visual_projection / ol_intrinsic; 89% of the outgoing projection into the optic lobe; φ — external host clock |

**Key fact — "a switch, not a knob":** `SPEC_exc` for the feedback and nofeedback groups coincide (1.960/1.960; 2.210/2.210), although η_eff for feedback falls threefold (0.452→0.084) and for nofeedback is constant. Cause: the MBON counter hits the floor. The metric `ratioM` measures the specificity of the readout, not the "strength of learning" — it cannot test "a sated fly learns more weakly" (`statevec_results.md` §2). In the weight sense, a sated fly does learn more weakly: depression 0.0023 vs 0.0037 (−38%) (§5).

**Boundaries:** `ratioM` is not monotonic in η (fine-SFA: 0.05→1.91 > 0.5→1.73) — the criterion "a sated fly learns more weakly" is untestable with this metric; s/l-LNv — the annotation lies (visual, not clock).

## L5.4. Modulators: OA, 5-HT, dopamine

### OA / 5-HT (M-state, `tools/mod2.py`)

Mechanics: per-segment `A_g` (mean Hz of the group) → `bias[j] = Σ_g κ_g·M[g,j]·A_norm` → an additive shift of the threshold of target pools (reuse of the `adapt` carrier). `FLY_MOD2=0` → `adapt=NULL`, max|Δ|=0 (bit-for-bit).

| Experiment | Result | Source |
|---|---|---|
| E10-OA (arousal) | ✅ **PASS**: exit from silence ×10³–×10⁷, **3/3 seeds**; random control |Δ|≤3.4% | `mod2_results.md` §3 |
| E11-5HT (state) | 🟡 PARTIAL: signs opposite to OA (3/3); 5-HT↓ ≥15% only 2/3 seeds; feeding pool 5-HT **−27%** / OA **+356%** | `mod2_results.md` §4 |
| OA-VPM4 (local gate) | 🟡 unstable across seeds (−42/+2283/+5228%) | ibid. |
| N8: OA gate / 5-HT | OA non-specific (p=0.56); 5-HT silences PN dose-dependently, but CV grows | `n378_results.md` §N8 |

**Boundaries:** attractor bistability dominates in the active regime (random control noisy −70%); the FP codegen of the carrier gives a divergence `adapt=NULL↔zeros` of up to 40,894 spikes at HZ=20 (hence all MOD2=1 conditions pass the carrier).

### The dopamine layer

**Anatomy** (`dan_anatomy.md`): DAN **396 bodies** (~0.24% of 166,700), **44 named types** (+4 without a type); PAM cluster **316 bodies / 15 types**; PPL — 11 paired types (22 bodies); DA→MBON 3,423 edges/46,958 synapses. KC→MBON: **61,210 edges / 463,640 synapses**.

**The winner's community formula (Fly's Table):** `gain -= lr·a_kc·da_mbon` per-compartment, lr=0.05, decay to 1; dopamine-cut control ✓ (`dan_community_methods.md` §2.2–2.3). Their success — compartmental dopamine, not a global scalar.

**The "dopamine = sign +1" pitfall** (`nt_audit.md` §4.2): in the legacy converter, dopamine fell into the default **+1** → `PAM→KC = 110,450 edges` flooded KC with excitation; all dopaminergic→KC = 129,132; dopamine out = 241,694. The fix (3-class): `dopamine → MOD`, `signed=0`, `Modulatory=1` for PAM→KC; **435,541** modulatory edges, 541 modulatory neurons, `consensus_nt` (not `predicted_nt`; the naive `predicted_nt` gave 1,531,095 mod-edges — a catastrophe) (`nt_converter_3class.md`; `nt_audit.md` §5). The PAM modulator is implemented (241k edges zeroed under `FLY_DAN_PAM_MOD`), but the endogenous channel is weak — it needs calibration (`pam_modulator.md`).

> **Refinement of the dopamine-layer numbers.** The task statement gives "DAN 396: PAM ~300/PPL 16"; according to the primary source (`dan_anatomy.md` §1/§1.1; `nt_audit.md` §4.2) the exact values are: **DAN 396 bodies** (44 named types + 4 without a type), **PAM cluster 316 bodies / 15 types**, **PPL 11 paired types** (PPL101–108, PPL201/202/204; 2 bodies/type = 22 bodies; `nt_audit.md` — 24 bodies, of which 22 dopamine + 2 unclear). "PPL 16" is not confirmed by the source; use **316 / 11 types**.

## L5.5. Peptide crosswalk v3

**Result** (`peptide_crosswalk_v3.md`): the adult Allen-2026 atlas + GEO GSE296540 (190,624 cells) expanded the map from **1,231 → 5,399 neurons** (×4.4), peptides 19 → 20; positive controls Nässel 2022 — **20/20**.

| Peptide | Class | Neurons | Source |
|---|---|---|---|
| **sNPF** | KCγ (`KCg*`) | **1,557** | `peptide_crosswalk_v3.md` §3 |
| **sNPF** | KCa/b (`KCab*`) | **1,810** | ibid. |
| sNPF total | — | 3,754 (×9.7 vs v2) | ibid. §"Summary" |
| AstC / CCHa1 / CCHa2 / natalisin | adult detection exists | 8.12 / 0.64 / 2.53 / 0.84 % of cells | ibid. §0 |

**Significance:** the hunger peptide **sNPF is expressed in the KC themselves (γ and αβ)** — directly in the learning layer. However, the sNPF receptor on KC is **not enriched** in the Allen-2026 atlas (the receptor is on interneurons, not on KC), which explains the generic result of the hunger gate v3/v4 (`hunger_gate_v3_results.md` §10).

**Boundary:** linking AstC/CCHa1/2/natalisin to specific bodyIds runs into the **absence of a public cluster→type table** (metadata only in ShinyCell applications) (`peptide_crosswalk_v3.md` §0).

## L5.6. State-gated retrieval, context tag, sgr3: addressing does not cure

**This line was closed in three passes.** In all cases the state tag is written but not released:

| Experiment | What was tested | Result | Source |
|---|---|---|---|
| SGR v2 (NPF, `vt_map`) | state × test retrieval | ❌ P1 fail 4/8 (SD≈noise); the NPF carrier is diffuse: on MBON **12 targets, 1.2% of the weight** | `state_gated_retrieval_results.md` §0/§2 |
| Context tag v1 | tag in the address | ✅ P1 **24/24**, Jaccard 0.19–0.49; ❌ P2 release 2/8 | `context_tag_results.md` §0 |
| Context tag v2 (symmetric) | remove the one-sidedness | ❌ P1sym **0/8**, P2 2/8 | `context_tag_v2_design.md` §3; DEVLOG §95 |
| **sgr3 (PSP-drive)** | state effect on PSP | 🟡 **P1 8/8** (+18k…+70k, dose), **P2 fail** (not odour-specific) — generic | `understanding_final_map.md` §"Update 29.09"; `sgr_v3_design.md` |

**The main conclusion of L5** (`transitive_v2_results.md` §6; DEVLOG §103): the same mechanism as in V2 — targeted plasticity. **The generic effect of sgr3/hunger-v4 is not cured by addressing** — a transfer of the V2 verdict. The context gate works **only for acquisition** (hunger gate ×1.27), **not for retrieval** (`address_release_synthesis.md` §4.4).

**Boundaries:** the match×state interaction is **not symmetric**: stable for `acq=hungry` (8/8, +10), negative for `sated` (the untrained drive confounds the metric) (`context_tag_results.md` §5.1); the diffuse NPF threshold bias **masks** the address rather than releasing it (AC_comp 0.969→0.812, `state_gated_retrieval_results.md` §4.4).

## L5.7. Summary table L5: phenomenon → status → numbers → source

| Phenomenon | Status | Numbers | Source |
|---|:---:|---|---|
| Hunger gate (switch) | ✅ | 3/3 seeds ×1.27; `eta_eff = eta·(0.1+0.9·H)` | `statevec_results.md` §2; DEVLOG §56 |
| Hunger gate v3 (sNPF⁺ KC) | 🟡 generic | P1 7/8; Pearson(kc_pre,SPEC)=**0.948**; AC 0.75→1.00 | `hunger_gate_v3_results.md` |
| Hunger gate v4 (paracrine) | ❌ closed | pool-specific, but P2 generic (2/7 at κ=2); κ-map 1/2/3/5 — no specificity | `hunger_gate_v4_results.md` §11 |
| Circadian rhythm (van der Pol) | ✅ | period **24.00 CT-h**; PRC ±1.8; entrainment LD 12:12 | `circadian_results.md` |
| Sleep gate | ✅ | night n_changed=0; day 243–334; cos=1.0 | `circadian_results.md` §4 |
| State vector E/NPF/Dilp/φ | ✅ | bit-for-bit; E-NP2 PASS; E-NP1 PARTIAL | `statevec_results.md` |
| s/l-LNv as clock neurons | ❌ | superclass = visual_projection / ol_intrinsic | `statevec_results.md` §3 |
| OA (arousal) | ✅ | ×10³–×10⁷, 3/3 seeds | `mod2_results.md` §3 |
| 5-HT (state) | 🟡 | signs opposite to OA; ≥15%↓ 2/3 seeds | `mod2_results.md` §4 |
| Dopamine (DAN) | ✅ anatomy | 396 bodies; PAM 316/15 types; KC→MBON 61,210 edges | `dan_anatomy.md` |
| Dopamine "sign +1" (pitfall) | ✅ fix | PAM→KC 110,450; mod 435,541; signed=0 | `nt_audit.md` §4.2; `nt_converter_3class.md` |
| Peptide crosswalk v3 | ✅ | 1,231→5,399 neurons; sNPF→KCγ 1,557 + KCαβ 1,810 | `peptide_crosswalk_v3.md` |
| State-gated retrieval | ❌ | P1 fail 4/8; NPF→MBON 1.2% of the weight | `state_gated_retrieval_results.md` §0 |
| Context tag (address) | ✅ / ❌ release | P1 24/24 (J 0.19–0.49); P2 release 2/8 | `context_tag_results.md` §0 |
| sgr3 (PSP state) | 🟡 generic | P1 8/8 (+18k…+70k); P2 fail | `understanding_final_map.md` §"Update 29.09" |

## L5.8. Boundaries of L5 and remainder

**Proven boundaries:**
1. **State specificity** — generic excitability, not a tie to the trained odour; addressing does not cure (V2 transfer).
2. **The NPF carrier** is diffuse (1.2% of the weight onto MBON) — the threshold bias masks the address, does not release it.
3. **The context gate** works only for acquisition, not for retrieval.
4. **s/l-LNv** — the annotation lies: visual, not clock; φ — external host clock.

**Remainder:** PSP reading of the interaction (sgr3 on the adopted PSP channel); symmetric context (reduced κ); state-specific context tag; export of the ShinyCell metadata for linking AstC/CCHa/natalisin.

---

# Overall summary L4–L5

| Layer | Score (epistemic / functional) | Main carrier | Key result | Boundaries |
|---|:---:|---|---|---|
| **L4 Learning** | 88% / (boundary proven) | KC→MBON weights | SPEC 1.73 6/6; LTM 1.737; PSP 0.97 | address-release (spikes); composition/V2 |
| **L5 States** | 77% / (boundary proven) | hunger/sleep/arousal | hunger gate; OA/5-HT; circadian 24.00 CT-h | state-gated retrieval (generic) |

**Two cross-cutting boundaries unite the chapters:** (1) the **release block** — the learned delta is not released into MBON spikes (intrinsic homeostasis); (2) the **operation on the overlap** — KC→MBON depression does not encode directionality, so neither composition (L4) nor state specificity (L5) arises from addressing. The working output in both cases is the **PSP channel**.

*All numbers are taken from the listed reports/DEVLOG; no new "facts" were introduced. The [VERIFY] markers were resolved against the primary source on 29.09.2026: the SPEC aggregates were separated by protocol (`learning-stage-report.md` 1.50/8 seeds vs `e9d_recalibration.md` §5 1.73/6 seeds vs the recomputation 0.90–0.96); the DAN numbers were refined (PAM 316/15 types, PPL 11 types).*


---

# Understanding the Drosophila brain. Chapter L6 — Behaviour: from spikes to action, and the chapter "Research methodology"

**Project:** "Fly" (~/Рабочий стол/Муха/). **Compilation date:** 29.09.2026.
**Purpose:** overview chapters of the master monograph "Understanding the Drosophila brain" (in Russian). Chapter L6 answers the question "what the network does", the chapter "Methodology" — "why these numbers can be believed".
**Rule:** every number is taken from the indicated report/project data; where there is no source there is a `[VERIFY]` marker.
**Cross-cutting law of the project:** *plasticity is strong, the carrier of the representation is the bottleneck; the working output of the substrate = the PSP channel* (`docs/understanding_final_map.md` §10; `docs/address_release_synthesis.md` §3).

Final score of layer L6 (29.09.2026): **84%** functional reproducibility, epistemic completeness ≈100% (every behavioural question is closed by a mechanism or a proven boundary). The two metrics of understanding (Andrey's decision, 29.09.2026; L7 is excluded from the metric of understanding) — see the chapter "Methodology" §M.5.

---

# Chapter L6. Behaviour: from spikes to action

Behaviour is everything the network does after the sensors (L2) have gathered the input, memory (L3) has held the state, learning (L4) has changed the weights, and the states (L5) have modulated the dynamics. Layer L6 decomposes into four independently testable blocks: **motor output** (a spike-to-movement decoder), **rhythmicity** (central pattern generators), **external validation** (comparison with someone else's benchmark) and **behavioural readout** (how to read the learned in the spikes at all, in spite of the attractor). The overall outcome is honest: the motor decoder, the rhythmicity and the external benchmark are working mechanisms; goal-directed navigation and absolute valence are proven boundaries that ran into the absence of a carrier, not into a metric.

## 6.1. The motor decoder: the "spikes → movement" layer

**Task.** To connect spiking activity to the effector apparatus: command (descending neuron, DN) → execution (motor neuron, MN) → muscle group. This is the first layer where activity is read as *action*, not as a "response to a stimulus".

**Anatomical framework** (`tools/motor_pools_build.py` → `data/motor_pools.json`; `motor_decoder.md` §1):

| Element | Number | Comment |
|---|---|---|
| Motor class (`vnc_motor` + `cb_motor`) | **815 neurons** | 11 subgroups (Leg front/hind/mid, wing, proboscis, neck, haltere, antenna, abdomen, rostrum, metathorax) |
| Direct DN→MN output | **448 of 480** DN types, **23,405 edges** | but direct edges are rare and weak — the main wiring goes through 2 hops of local interneurons |
| Command map (top) | DNa02 119, DNa01 68, DNb01 59, DNp01(GF) **3**, MDN 11 | DNa02 — steering |

> **Annotation pitfall:** TTMn (the tergotrochanter jump muscle) is labelled with subclass `wm` (wing) — the wing pool by subclass is contaminated by jump MN, and the decoder subtracts TTMn from `wing`.

**Decoder** (`tools/motor_decode.py`; `motor_decoder.md` §2) — pure numpy functions, 5 metrics, selftest **7/7 PASS**:

| Metric | Formula | Meaning |
|---|---|---|
| `turn_index(L,R)` | `(L−R)/(L+R+ε)` | sign and strength of turning (push-pull DNa02/DNb01) |
| `locomotion(v,base)` | normalisation to the base regime + threshold | moving / still |
| `quiescence(pre,post)` | `(pre−post)/(pre+ε)` | relative freezing |
| `escape_burst(esc,ctrl)` | `z(max esc) − z(max ctrl)` | escape burst bypassing general excitation |
| `wing_rhythm(times,total)` | FFT dominant + peak/mean | wing / song rhythm |

**Calibration on known runs (`motor_decoder.md` §3) — 3 channels are read:**

| Run | Expectation | Fact | Verdict |
|---|---|---|---|
| **wm-steer** (canon v2, bump) | turning follows position | `turn_dn` pos16 **+0.90**, pos44 **−1.00**; motor correlates with DN (`r=−0.99`, contralateral) | ✅ turning is read |
| **loom** (LPLC2, 100 Hz) | escape burst TTMn | TTMn = **20/23/21** (3 seeds); random DN = 0/0/0 | ✅ escape |
| **GF** (DNp01) | escape burst TTMn | TTMn = **10/13/11**; random = 0 | ✅ specificity |
| **silence** (baseline) | still | total = 0 for all seeds; `moving_frac`<0.5 | ✅ silence = still |

TTMn is a pure escape indicator: only GF and loom produce a burst, zero on a random pool and DNp09. **Rematches of battery F1 (`motor_decoder.md` §4):** B5 (DNa02 silencing → loss of turning) — **NOT confirmed**: the motor push-pull falls only from 0.234 to 0.187 (−20%), the correlation −0.98 is preserved; DNa02 has only 119 direct MN edges, the rotator asymmetry is formed by other DNa/DNg. A4 (MDN → walking) — **PARTIAL**: the decoder distinguishes walking from silence (baseline 0 → stim 441), but MDN adds only +13% over random, and the direction is not resolved. A6 (DNp09 → freezing) — escape ✅ clean, freezing weak (2/3 seeds, mean +0.19 vs control +0.12).

**Conclusion of the block.** The decoder is built, calibrated and reveals movement in three channels (turning, escape, still). The three "failures" of the battery turned out to be failures of the **hypotheses, not of the metrics**: DNa02 does not monopolistically control turning, MDN drowns in the general ignition of the attractor, DNp09 does not give reliable freezing (`motor_decoder.md` §6).

## 6.2. Working-memory output: compass → steering wheel (DNa02)

Working memory (L3) holds the EPG ring bump; layer L6 answers the question of **whether this memory comes out into the motor**. The path has been found and verified (`docs/wm_steering.md`; DEVLOG §41; `understanding_final_map.md` L3/L6):

- **Chain:** EPG → PFL3 (**113 edges**) → DNa02 (**24 edges**) — a compact bottleneck of the motor output of working memory.
- **Motor push-pull:** preferred direction **DNa02 L ≈ 16 / R ≈ 40–44**; L−R spikes up to **48**; the L side is strong, R is subthreshold in the wild type.
- **Cross-connectome replication (v783):** DNa02 **L≈16 / R≈40** — exactly as on MaleCNS (`understanding_final_map.md` update 29.09; DEVLOG §100).
- **Coordinate reading:** the DNa02 readout in the `wm_ring` harness gives a tuning curve over bump position — the first motor output from working memory (`wm_steering.md`).
- **R steering wheel:** the averaged curve of L peaks 0/16/48 is noisy, R is subthreshold; a PFL3 gain of **×3–5** opens a bell at 40–44; the push-pull L16↔R44 ≈ half a ring (`understanding_final_map.md` L3).

**Boundary.** DNa02_R is subthreshold in the wild type; gain opens the preference, but that is already an external calibration. Free rotation of the bump (without drag) is not implemented — there is no angular-velocity mechanism (CL inputs) (L3, `working-memory-report.md` §Limitations).

## 6.3. CPG oscillators: walking, half-center, song

Rhythmicity is the third missing mechanism of L4/L5 (battery F1: A7 gives a burst, not a rhythm). Of the three Drosophila motifs (half-center / recurrent E + feedback-I / endogenous burster) we lack only intrinsic bursting.

### Walking-CPG (E1–E2–I1) ✅

The published motif (Pugliese/Brunton/Tuthill 2025, 3 neurons) was found **1-to-1** in MaleCNS: E1=IN17A001, E2=INXXX466, I1=IN16B036; loop E1→E2 2733 · E2→I1 445 · I1→E1 3705 (inh) · I1→E2 1261 (`cpg_results.md` §2).

- Oscillates in spiking LIF **without SFA/PIR** — a pure E/I loop: **6/6 seeds**, 58 cycles in 4 s, drift≈0.
- The frequency is controlled by the drive: **6.9 → 15.4 Hz** as the drive goes 100→800 Hz — the Pugliese prediction confirmed.
- Controls: no-drive — complete silence; random-DN — silence (the motif is specific).
- Mechanism: E1/E2 are co-activated (recurrent excitation), I1 turns on with a delay of −15…−51° and inhibits in return (`cpg_results.md` §2.3).
- Pitfall: SFA does not set the period (a sweep of τ_sfa is flat) — the frequency is set by the E/I loop (τ_syn=5 + τ_mem=20 + tDelay 1.8 ms), not by adaptation.

### Song-CPG (TN1a ↔ vPR9) 🟡

The first endogenous song rhythm: **36–37 Hz, 3/3 seeds**, drift 0.00–0.03, in the target band (`pir_cpg_results.md` §3). The frequency is monotonically controlled by `τ_sfa` (53.5 → 34.5 Hz). Ablation: **PIR is load-bearing** (without it 36 → 12.5 Hz), STD adds (36 → 28.5), SFA sets the base 11.5 — a synergy of three mechanisms. **Limitation:** TN1a↔vPR9 are strictly **in phase** (co-activation, Δφ ≈ +26…+32°, r₀ +0.65…+0.73), wingMN is dead (rate ≈ 2). This is a rhythm generator, but not an anti-phase feedback oscillation and without an output to the song motor.

### Half-center (anti-phase) ✅ — closed 29.09.2026

It was long a negative: the mutual inhibition IN13A001↔IN19A001 is **asymmetric ×2.0** (Σ raw A→B = −2294, B→A = −1130), release gave a slow anti-correlation, but there was no locked anti-phase (`cpg_results.md` §4; `pir_cpg_results.md` §2).

**Closed by the final sweep (DEVLOG §100–101; `docs/half_center_v2_design.md`):** `C2-sym + SFA τ=50` gives all criteria on **3/3 seeds** — envelope r₀ **−0.45/−0.59/−0.86**; raw 20 ms ≤ −0.15 in all; alternation **0.5–2/s**. This is a **stochastic** alternation (not a rigid cycle: on 5-ms raw data there are no correlations). Mechanism: rebalancing the w8 edges of A↔B (`FLY_CPG_BALANCE=sym`) + PIR + SFA.

### CPG outcome

| Motif | Status | Key numbers | Recipe | Boundary |
|---|:---:|---|---|---|
| Walking (E1–E2–I1) | ✅ | 6/6 seeds; 6.9→15.4 Hz by drive | drive DNg100 200 Hz, without SFA | — |
| Song (TN1a↔vPR9) | 🟡 | 36–37 Hz 3/3; PIR load-bearing | `STIM_MAP=drive:3`, PIR 15, STD 0.1, SFA 0.5 τ150 | in phase; wingMN dead |
| Half-center | ✅ | env −0.45/−0.59/−0.86; alt 0.5–2/s, 3/3 | C2-sym + SFA τ50 (rebalance A↔B) | stochastic, not a rigid cycle |

Sources: `cpg_results.md`, `pir_cpg_results.md`, `docs/half_center_v2_design.md`, DEVLOG §57/§59/§64/§100/§101.

## 6.4. External validation: flybench on two connectomes

flybench is a community benchmark of reflexes (34 tasks, YAML + docs). Our engine = flybench-LIF at `gain=1.0` (a common `wScale`), so the gain axis is reproduced directly and the comparison is honest (`flybench_results.md` §0/§8).

### MaleCNS (29/34 feasible, `flybench_results.md`)

| Metric | Value | Community reference |
|---|---|---|
| **Core tier** (5 tasks) | **5/5 = 1.000** @ gain 0.65 | 0.57 |
| **Max active** | **4.7%** | **23.3%** |
| Hard tier | 0.602 | 0.56 |
| All tier | 0.671; checks 92/136 | — |

Our engine holds the full core at a network **~5× sparser** — a consequence of the three-class NT layer (modulatory edges zeroed, histamine→INH) (`flybench_results.md` §2). **Task 19** (`gf_to_muscle_latency`) is pre-registered by flybench as a failure of any chemical point-neuron ("names a missing mechanism, not a wrong parameter"): without gap — 11.0 ms ❌, but with our gap **GF→TTMn 0.10 ms** ✅ (target ≤1.5) — the task goes 1/4 → 3/4 (`flybench_results.md` §5). The APL fix ×2.5 gives **8/8** on task 26 (KC sparseness) against 5/8 without it. Independent community FINDINGS are reproduced: the LC→DN matrix 14/16 (false LC6/LPLC1→MDN), PN broadcast 92%, size principle ρ=−0.90 "largest first" (`flybench_results.md` §7).

**5 N/A — objective reasons:** tasks 32/33/34 require a flyvis front-end and/or a MuJoCo/FlyGym body; task 30 — `dataset_only: flywire783` (MaleCNS lacks the female oviDN/pC1 chain); task 31 — no Foxglove CB0890 (`flybench_results.md` §1/§9).

### FlyWire v783 — cross-connectome replication (R5, `flybench_v783_results.md`)

| Metric | Value |
|---|---|
| **Core 5/5** | **PASS** (as on MaleCNS) |
| Full run (24 ACTIVE × 2 seeds) | **86/108 checks (79.6%)**; 11/24 tasks fully |
| Unique v783 wins | task 30 (egg-laying, female) ACTIVE; task 31 (Foxglove) runnable for the first time |
| 6 DEGRADED | TTMn/DLMn, high-salt, vnc_motor, EPG-wedges — brain-only/female of v783, not a benchmark failure |

FULL PASS on v783: 01 stability, 02 sugar, 03 bitter, 04 looming, 05 taste, 09 crosstalk, 10 looming DN, 11 flash≠loom, 12 return-to-rest, 14 robustness, **26 MB-APL sparseness 8/8** (`flybench_v783_results.md` §Results). Task 30/31 — calibration boundaries (3/4, 4/6; gain 0.5/0.8 does not improve).

**Conclusion of the block.** flybench is integrated end-to-end on two independent connectomes; the core tier is passed fully on both; two tasks marked by the community as "requires a missing mechanism" are closed (19 — gap). Limitations: no flyvis/MuJoCo (5 tasks), task 29 without ring calibration, absolute rates depend on the gain/NT converter.

## 6.5. Navigation: the differential reference and the `corr(A,B)` lesson

Goal-directed movement toward an odour is the crown of behaviour, and here the project ran into the attractor wall. The history of v1–v5 is honestly negative but instructive.

**Hypothesis v3** (`navigation_v3_results.md`): replace scalar valence with a **differential reference** — two odours A (rewarded, E9d) and B (neutral reference), reading `D = R_A − R_B` pairwise by kernel-seed, so that the common attractor mode is subtracted. Three independent levels gave a consistent negative:

1. **Scalar:** the learned effect `dLN ≤ 8` spikes out of ~170 (≤5%), the sign flips, `|t| ≤ 1.8` — indistinguishable from noise.
2. **Pattern projection** (circular, maximum SNR): `|t| ≤ 1.76`; phase-gated T1 does not help.
3. **The v3 loop** walks perfectly (learned/naive/swap 20/20, random 0/20), but **learned ≡ naive bit-for-bit** (McNemar 0/0, p=1.0). A sweep of the valence gain: even **±15%** shifts the outcome by ≤2/300 — the choice is determined by the random phase of the scan, not by valence.

**Key finding (a new project rule):** the premise "a common mode in A and B" is **false** — the A and B responses **anti-correlate** (on exc `corr ≈ −0.75`), `sd(A−B) > sd(A)`, that is, **subtraction amplifies the noise rather than cancelling the mode**. Mechanism: A and B compete for one attractor (the implementation in which A has ignited the network inhibits the response to B). Hence the rule: **before a differential readout, check `corr(A,B)` and `sd(A−B)` vs `sd(A)`; the differential saves you only under a positive correlation** (`navigation_v3_results.md` §6.2).

**Root of the negative** (`navigation_v3_results.md` §5): E9d learning is a **redistribution of the pattern** (a 6.6% shift in 288 of 61,210 edges), not a change of scalar valence; any pooled/differential readout does not see it. In addition, the `exc`/`mbonsum` classification is degenerate — a binary (saturation at rate≈2), there is no value gradient. v5 confirmed: the threshold `wA ≈ 2–3` against potentiation 8.5% — a carrier deficit of **×10–20** (`understanding_final_map.md` L6; DEVLOG §69).

**Conclusion of the block.** Navigation is a **proven boundary**: it is not the readout but the **carrier** (attraction potentiation / valence-specific plasticity). The tool `tools/nav_e2c.py` + `nav_e2c_analyze.py` is a reproducible bench for any future carrier.

## 6.6. Behavioural readout: the T1 inset and breaching the attractor wall

A separate line of L6 — how to read the **learned** in the spikes at all, when the wild-type network is bistable and the post-stimulus attractor (the "tail") masks the behavioural signal. The wall was breached in three waves.

**T1 inset** (`FLY_TAIL_MECH=T1`, host-side, without modifying the core): `row_scale` of inhibitory sources ×3 after the stimulus ends (59,262 neurons = 35.6% of the network). It suppresses the diffuse attractor, the focused bump survives — a **contrast amplifier**.

| Metric (T1 gain 3) | Result |
|---|---|
| Tail (tail_ratio) | 0.256/0.286/0.286 (**−72.4%**) |
| Stimulus response (stim_ratio) | **1.000** (not lost) |
| WM bump | **sharpened** (late 0.77, conc 0.69 vs 0.56 wild) |
| E9d | unchanged (the mechanism does not enter the window 4–14) |

Source: `docs/attractor_taming.md`; DEVLOG §54. The wild-type tail ≈ 153,394 spikes, 96% in the `other` region (not MBON/KC) → it is a whole-network paroxysm, not an MB readout.

**T1 × R5/R3 synthesis** (`t1_r5_synthesis.md`): the T1 winner (`ts=14`) is **orthogonal** to the readout window `(4,14)` — its action begins exactly where the window ends, so T1@14 gives **bit-for-bit** the same E10/H2 (a no-op by construction). **Shifting the trigger to `ts=10`** gave what they could not give separately: **H2 R5 → 8/8** (control N `L=0.0000`) and **E10 R3 → 6/6** (no-shock exactly 0). The price: T1@10 breaks E9d specificity (SPEC 0.156/0.201, `changed` ×7) — this is a regime change of the physiology, not a free victory.

**Sweep `FLY_TAIL_START × GAIN` — a unified physiology** (`tail_sweep.md`): the conflict "E9d ↔ behaviour" turned out to be a threshold in **gain**, not in `ts`. At `gain ≤ 2` E9d specificity is reproducible (SPEC 1.5–2.9), at `gain ≥ 3` it breaks (SPEC 0.8–1.2). **The winner `ts=12, gain=2`:**

| Criterion | Threshold | ts=12 g2 |
|---|---|---|
| E9d SPEC (6 seeds, window 4–14) | ≥1.5 and ≥4/6>1.2 | **1.840, 5/6** ✅ |
| Tail on/off | ≤0.5 | **0.426** ✅ |
| E10 R3 DID<0 | ≥5/6 | **5/6** ✅ |
| H2 R5 RI(C)>RI(B) | ≥7/8 | **7/8** (wRI 8/8) ✅ |
| WM bump ≥2 s | holds | **holds** ✅ |

**Phase-gated T1** (`FLY_TAIL_PHASES=train:0,probe:3`) removes the conflict definitively: the mechanism is active only in the test phases → E9d SPEC **2.04** (better than the base) + tail **0.30** + H2 **8/8** + E10 **6/6** (DEVLOG §58). There is no single configuration for everything (H2 and E10 share `ts`), but the base is phase-gated `ts=12 g3`.

**Lessons of the block:** the attractor is **not additive noise** (a z-score is useless); **4-seed conclusions = artefacts, minimum 8** (`t1_r5_synthesis.md` §4.4/§6). All three pillars of learning (E9d 6/6, E10, H2) are now read behaviourally.

## 6.7. Proven boundaries of L6

1. **Body and muscles (FlyGym / MuJoCo / flyvis front-end).** 5 flybench tasks are N/A: without a body there is nothing to measure `takeoff` with, without flyvis there is no optic lobe. This is a boundary of what is modelled, not of the physiology (`flybench_results.md` §1).
2. **Navigation.** The A/B responses anti-correlate (−0.75) → the differential amplifies the noise; carrier deficit ×10–20 (`navigation_v3_results.md`).
3. **Absolute valence.** Taste-v783: the sugar valence is directional 8/8, but the bitter bit does not flip on the second connectome either → the boundary is **not connectome-specific**, the carrier = valence potentiation/PSP (`understanding_final_map.md` update; DEVLOG §99).
4. **Free rotation of the WM bump** — there is no angular-velocity mechanism (`working-memory-report.md` §Limitations).
5. **Task 30/31 on v783** — the final calibration boundary (3/4, 4/6).

## 6.8. Summary table L6

| Mechanism | Status | Key number | Report |
|---|:---:|---|---|
| flybench core (MaleCNS) | ✅ | 5/5 = 1.000; max active 4.7% (ref 23.3%) | `flybench_results.md` §2 |
| flybench hard/all | 🟡 | hard 0.602, all 0.671, checks 92/136 | `flybench_results.md` §3 |
| flybench-v783 core (R5) | ✅ | 5/5 PASS; full 86/108 (79.6%) | `flybench_v783_results.md` |
| CPG walking (E1–E2–I1) | ✅ | 6/6 seeds; 6.9→15.4 Hz by drive | `cpg_results.md` |
| CPG song | 🟡 | 36–37 Hz 3/3 (in phase); wingMN dead | `pir_cpg_results.md` §3 |
| CPG half-center | ✅ | env −0.45/−0.59/−0.86; alt 0.5–2/s, 3/3 | `half_center_v2_design.md`; DEVLOG §101 |
| Motor decoder | 🟡 | selftest 7/7; turn/escape/still ✅; B5/A4/A6 — failures of hypotheses | `motor_decoder.md` §6 |
| Escape GF/loom→TTMn | ✅ | TTMn 20/23/21 (loom), 10/13/11 (GF); random=0 | `motor_decoder.md` §3 |
| DNa02 (WM→motor) | ✅ | L≈16 / R≈40–44; L−R up to 48 sp; EPG→PFL3(113)→DNa02(24) | `wm_steering.md`; DEVLOG §41 |
| N19 pre-stimulus → choice | ✅ | p_pre=L→L 8/8, p_pre=R→R 8/8 | `n13141819_results.md` §N19 |
| N18 cue-conflict | 🟡 | threshold arbitration w≈0.71, not reliability-weighting | `n13141819_results.md` §N18 |
| N3 FC2 goal memory | ❌ | FC2 silent under the compass (0 spikes) | `n378_results.md` §N3 |
| Navigation (v1–v5) | ❌ | loop 0.67 vs random 0.00, but learning does not enter the loop | `navigation_v5_results.md` |
| Behavioural readout T1 | ✅ | tail −72.4%; stim 1.000; H2 8/8, E10 6/6 | `t1_r5_synthesis.md` |
| Unified physiology T1 ts12 g2 | ✅ | E9d 1.84 5/6; tail 0.43; H2 7/8; E10 5/6; WM holds | `tail_sweep.md` |
| Phase-gated T1 | ✅ | E9d 2.04; tail 0.30; H2 8/8; E10 6/6 | DEVLOG §58 |

---

# Chapter "Research methodology"

This is a unique part of the monograph: not "what we learned about the fly" but **why these numbers can be believed**. The discipline grew out of the root conclusion of the project (L1): the network is bistable, plasticity is masked by the attractor, and therefore "the code is written ≠ it works" — every result must pass a live test, a null control and pre-registration. Methodology is also a result of the project, and it is verifiable (`verification_battery.md`; `publication_outline.md` §J).

## M.1. The research cycle

Every experiment passes through a fixed cycle:

**question → hypothesis → pre-registration (sha256) → run → PASS/FAIL verdict → map update.**

- **Pre-registration** — the metric, window, number of seeds, threshold and kill criterion are fixed **before** the run. Example: the V2 design (`transitive_v2_design.md` §5/§7) fixes `P1 (rA_γ chain > nochain, paired ≥6/8)`, `P5 (kill of the harness: noreward changed=0, 8/8, else the series is annulled)` and `sha256` hashes of the code (`61d4e22b…` → after the edit `8a3d3f57…`).
- **Honest negatives = a result.** Half of the project's lines are closed by negatives: taste (absolute valence), navigation (v1–v5), transitive composition (V2), P2b (self-organization), spacing, state-gated retrieval. A negative with a diagnosis ("why exactly the wall stands") is worth more than a vague positive.
- **A proven boundary ≠ ignorance.** A boundary is a verdict with a mechanism-cause; the mechanism backlog is empty (see §M.5).

**Formulation of the project:** "Truth is what is signed (apt status, documentation, one's own 'done'); the truth is what actually is. Formally verified ≠ true" (MEMORY, philosophical core 18.09.2026; `verification_battery.md` §0).

## M.2. Verification discipline

| Tool | What it protects | Worked example |
|---|---|---|
| **Bit-for-bit invariants (canons)** | any edit of the core must not change the reference runs | sparse 4,174 / hot 96,047 / v783 17,627 / GeNN 16,978; 64-bit recording — 5,999; `NULL` handles (SFA/PIR/STD/gap/stim) → bit-for-bit |
| **N10 null control (degree-preserving)** | a result is a property of the **topology**, not of any noise | E9d SPEC 1.887 → **0.000**; WM bump → **0**; flybench core 1.000 → **0.500**; CPG → **silence**; on v783 hold 4673/4637 → **0/0** (DEVLOG §60/§96) |
| **Independent seeds (minimum 8)** | the conclusion is not an artefact of a small sample | the lesson "4-seed conclusions = artefacts" (H2 R5 2/2 on the pilot → 4/6 on 6 seeds); V2-val 8 seeds |
| **Harness kill criteria** | the series is annulled if the control is not clean | V2: `noreward changed=0` (8/8), `reverse ≡ chain` bit-for-bit (determinism), `dep calyx=0` |
| **Honest reliability marker** | separate the primary source from the secondary | ✅ verified personally · 📄 primary by an agent · 🟡 secondary · ❓ hypothesis · ❌ refuted |

**Core-comparison rule:** because of the non-deterministic order of `spike_out` (atomicAdd), compare the **sorted set `(time,id)`**, not the order; a full state hash is non-deterministic (float atomics) → validate only by spike counts with a fixed seed (`SKILL.md` §Pitfalls).

## M.3. The wave working pattern and the role of the independent critic

Research is conducted in **waves of subagents** (model `deepseek/deepseek-flash`) through a streaming swarm orchestrator in tmux; the main session holds synthesis and verification. The key principle (law #65): **material → critic → verification → delivery**.

**The canonical case — V2 (`transitive_v2_design.md` §8).** Two independent critics (code + protocol) worked **during** the GPU run, before the blinding was lifted from the results. They caught **two protocol blockers**:

1. **A1:** `reverse_v2 ≡ chain_v2` bit-for-bit (compound is order-independent, gated by pair index) → criterion P4 is guaranteed to be meaningless. Solution: P4 removed, `reverse_v2` redefined as **K-det** (determinism-harness kill).
2. **A2:** `ovAD > ovAB` (A∩D=19.5–21.5 vs A∩B=18.2–19.2 active KC) → a pure overlap model predicts `chain ≤ nochain`, the sign of the "honest prediction" in §5 was wrong. Fixed: PASS P1 = **an anomaly against the overlap sign**.

Additionally, 8 findings were closed (A3–A10): P1≡P2 algebraically (demoted to secondary), the 6/8 threshold is weak (p=0.145) → primary **7/8** (p=0.035), the decisive criterion **P0 model-excess** was added (channel-aware overlap model), and the `K-alpha`/`K-cal` kills were added. **All corrections were fixed by an ADDENDUM with new hashes BEFORE analysis.** Outcome: V2 closed with an honest negative (`P0: chain−nochain 1/8, mean +79.8k, CI [+34.8k,+122.7k] — significantly against`), but the **protocol became strong, not weak** — criticism before the results saved a repeat GPU run.

**The K-alpha incident (caught and fixed before the verdict):** the first version of the kill criterion falsely checked `tA_αβ` (the network level, where recurrence is ±3% by construction) instead of `dep_αβ` (the edge level). Lesson: **check kill criteria at the level where the effect is expected**, not one level up.

**A second class of cases — the verifier after the analyst.** N-wave: verdict #87 was partially overturned by #89 — a post-hoc finding (carriers) turned out to be a property of the regime (Spearman ~0 between regimes) rather than a robust mechanism. Rule: **post-hoc findings require cross-regime verification BEFORE the next experiment is built on them.**

## M.4. Pitfall lessons

The table of cured errors is mandatory reading before any edit (DEVLOG §Pitfalls; `SKILL.md` §Pitfalls).

| Pitfall | Lesson |
|---|---|
| **Clip ±127 at scale /2047 + renorm (17.09)** | = a dead network (small weights ×16, hot 4.4k spikes). The clip is the full range of the scale (±2047), renorm is not needed. **"Weight mass preserved" ≠ "the network is alive"** |
| **Speed measured on a dead network** | validate by spike count and measure speed in **one run**; a count from one harness + speed from another = annulled records |
| **Baked t in a CUDA graph** | replay runs `t=0..k-1`: stimulus/LCG/rings by a time parameter are FORBIDDEN — only **device counters** (a periodic stimulus gave ×2.3 spikes!) |
| **Configuration bugs masquerade as the core** | the silent `FLY_DATA` fallback ran tests on v783 instead of MaleCNS (272 spikes instead of 31,214); the fix = `RuntimeError` + pool validation |
| **`arm=label` (×2)** | minor2: `hygro ≡ thermo` bit-for-bit — the "arm" was only a string label; taste-v783 — the same bug. Lesson: check that an arm actually changes the weights, not just the header |
| `except:pass` / silent fallbacks | hide not only the error but also the diagnostics; replace with logging + an explicit fallback |
| u8 counter overflow | clamp at 255 (freezing of neurons at step 256, −43% spikes) |
| hot bit in the flag byte | `flag & 1`, not `flag > 0` (eternal refractoriness) |
| Canon on buggy data | after the NT fix hot 979,182 → 96,047: the new number ≠ a regression — **find the root first** (10/21 stimulus channels were photoreceptors) |
| Micro-samples | conclusions only from **≥6–8 seeds**; a 2-seed pilot gave a false "PASS" |
| Runaway configs | `noreward+symmetric` started a run that idled for 6 h — config timeouts are needed |

## M.5. The L0–L6 scale of understanding: two metrics

By Andrey's decision (29.09.2026) L7 (language/tokens) is **excluded** from the metric of understanding — it is a track of the future project, not a deficit of understanding. Understanding is described by **two separate metrics**:

| Metric | Value | Meaning |
|---|---|---|
| **Epistemic completeness L0–L6** | **≈100%** | every question posed has a verdict with a mechanism-cause (a mechanism OR a proven boundary); the mechanism backlog is empty |
| **Functional reproducibility L0–L6** | **91.4%** | the mean; the deficit = proven boundaries 7–8% + data boundaries of MaleCNS 3–4% + calibration 1–2% |

It was earlier called "~87%" — that was a conflation of two scales; when reporting, name both separately (`understanding_final_map.md` update 29.09).

**What a proven boundary vs ignorance is.**

- **A proven boundary** — a verdict with a mechanism-cause: "there is no directed composition under any addressing of KC→MBON depression (ungated / odour signature / lobe-split)"; "absolute valence is not connectome-specific"; "gamma oscillations and sleep — no clock-gene data". The mechanism backlog is thereby **empty**.
- **Ignorance** — a question without an answer that requires new data/mechanisms (an external ground truth for the 54k gap pool; a body/FlyGym).

**The open frontier of understanding (not a backlog but theory):**

- **F1 structure→function:** from the connectome one **cannot predict** the learnability of a pair (P1: r=−0.136) — there is no predictive theory of topology→dynamics; a corpus of runs has been accumulated.
- **F2 cross-connectome transferability:** one-shot exists on MaleCNS, not on v783 (H1′ not accepted) — there is no mechanistic "why".
- **F3 data boundaries:** gap literature, scRNAseq peptides — closed by external data (`completeness-plan.md`), this is not a lack of understanding.

## M.6. Project statistics

| Indicator | Value | Source |
|---|---|---|
| Subagent task numbers | **110+** (task numbering up to No. 114, DEVLOG §91; waves chain2–chain31; final session 28–29.09 — chain9–chain31) | DEVLOG §102–§104 |
| Reproduced experiments from the literature | **31+** | DEVLOG §55; `publication_outline.md` |
| Verification battery F1 | **closed 25/25** (10 ✅, 6 🟡, 9 ❌-boundaries) | `verification_battery.md`; DEVLOG §60 |
| Connectomes | **2** (MaleCNS v1.0 166,700 / 25.6M; FlyWire v783 138,639 / 15.1M) | `understanding_final_map.md` L0 |
| External benchmark | flybench: core 5/5 on both connectomes | `flybench_results.md`, `flybench_v783_results.md` |
| Null control | N10 PASS (4/4 key results topologically constrained) | DEVLOG §60/§96 |
| Publication No. 1 | NT audit of MaleCNS, **Zenodo DOI 10.5281/zenodo.22975837** (all versions: 10.5281/zenodo.22975836) | MEMORY; DEVLOG §62/§87 |

## M.7. Publication track

**Paper No. 1 (published, "quiet" strategy).** "Neurotransmitter sign and source-column errors in Drosophila connectome simulation pipelines: an audit of MaleCNS v1.0". Author — Andrey A. Smarygin (Independent researcher, Tyumen). Pipeline: bioRxiv rejected (it requires an **organisational** affiliation) → publication on **Zenodo** with an instant DOI. The publication cycle is complete: the Zenodo paper ↔ the open-source repo `github.com/neuropunks/malecns-nt-audit` (related_identifiers: `isSupplementedBy`). Stealth: the paper does not disclose the project (an audit of isolated utilities, never the core/engine).

**Paper No. 2 (the crown, in preparation).** Learning + the attractor root + N10; ready after the v783 replication and pre-registration. Outline `publication_outline.md`: 10 claims (A–J) (A ⚔️ the attractor = the root of the community negatives; B 🥇 the first specific learning in a spiking recurrent network SPEC 1.73 [1.25–2.20] 6/6; C 🥇 gap 0.1 ms; D 🥇 three-class NT layer; E 🥇 WM bump 6 s) and a "matrix of what to finish".

**Lesson of method (the project's memory):** do not trust the formulation of a task on faith — verification of the primary source twice exposed confabulations in the initial premises. Truth is a form with a signature; the truth is a verifiable fact.

---

## Outcome of the chapters

**L6 (behaviour) = 84%:** the motor decoder is built and calibrated (turn/escape/still; selftest 7/7); working memory comes out into the motor (DNa02 L≈16/R≈40–44, replicated on v783); all three CPG motifs are closed (walking 6/6, half-center 3/3, song 36–37 Hz); the external benchmark flybench gives core 5/5 on **two** connectomes at ×5 community sparsity; the attractor wall is breached (T1 + R5/R3, phase-gated). Boundaries — body/flyvis (5 tasks N/A), navigation (A/B anti-correlation), absolute valence (carrier = PSP/potentiation).

**Methodology** — the second most significant result of the project after the mechanisms themselves. The cycle "question → pre-registration (sha256) → run → verdict → map", bit-for-bit canons, the N10 null control, a minimum of 8 seeds, kill criteria and — most importantly — **an independent critic during the run, before the blinding is lifted** (case V2: 2 blockers caught, ADDENDUM before analysis). The two metrics of understanding (epistemic completeness ≈100% vs functional reproducibility 91.4%) prevent conflating "we know why" and "we can reproduce". Precisely this discipline makes the 31+ reproduced experiments and the N10 control verifiable knowledge, rather than a set of pretty numbers.

---

### Resolution of the `[VERIFY]` markers (29.09.2026)

The former markers were lifted after reconciliation with the primary source; each — with a source:

- **"~100 subagents"** → **110+** task numbers: the numbering reaches No. 114 (agent No. 114 — "Hello world" on the ring, DEVLOG §91); the final session 28–29.09 — 30+ subagents in waves chain9–chain24 (DEVLOG §102), then chain25–26 (V2-val, §103) and chain27–29 (T2, §104); the overall wave range **chain2–chain31**.
- **Half-center "C2-sym"** → `FLY_CPG_BALANCE=sym`: rebalancing of the w8 edges of A↔B (Σ|w8| of both directions = √(S_AB·S_BA), `docs/half_center_v2_design.md` §0.1/§5); the final winner `C2-sym + SFA τ=50` (+PIR) — DEVLOG §101.
- **Functional reproducibility 91.4%** → the mean of the per-layer scores of the final on 29.09: L0 100, L1 95, L2 97, L3 99, L4 88, L5 77, L6 84 → **640/7 = 91.43%** (`understanding_final_map.md` §"Final percentages (29.09)").
- **flybench-v783 "11/24 tasks fully"** → confirmed: **86/108 checks (79.6%), 11/24 full**; the formal list of 11 items is present (`flybench_v783_results.md` §"Results of the full run (29.09.2026…)", line "FULL PASS (11)").
- **"31+ reproduced experiments"** → DEVLOG §55: "Total reproduced experiments over the project: ~31"; `publication_outline.md`: "31+ reproduced experiment".


---

# Conclusion of the monograph: the final map of the substrate

**Version 0.1 — 29.09.2026.**

## 5.1. What the substrate CAN do (proven by mechanisms)

| Capability | Key fact | Chapter |
|---|---|---|
| One-shot specific learning | N=1 suffices; SPEC 1.73 (6/6); controls no-reward=0, random=0.74–0.99 | L4 |
| Storage of ≥6 associations in the weights | AC_weights 0.72–0.78; the "2-word ceiling" was the ceiling of the readout, not of memory | L4 |
| Consolidation (LTM) | Protection from interference ratio 1.737 by pre-registration; idempotency | L4 |
| Persistent working memory | EPG bump 6 s; 39/39 positions on 2 calibration seeds and 90–100% persistence on 6 held-out (2–4 seed-fragile wells), motor output DNa02 | L3 |
| Ideal PSP input | AC_drive 0.97 — a legitimate channel for reading the substrate (adopted by the decision of 29.09) | L4 |
| Innate onset code | 6 odours, AC≥0.85 — semantics at the KC level, not MBON | L2/L4 |
| Sensorimotor cycle | 8–9/9 channels; loom instinct P0–P4 complete; Giant Fiber escape ipsilateral | L2 |
| Rhythmicity | Walking-CPG 6/6, song 36–37 Hz, half-center alternation (for the first time) | L1/L6 |
| Electrical synapses | Gap junctions in the engine, GF→TTMn 0.1 ms, flybench task 19 fail→pass | L1 |
| Endogenous clock | van der Pol 24.00 CT-h, PRC, entrainment LD 12:12, sleep gate | L5 |
| External benchmark | flybench core 5/5 on both connectomes at ×5 community sparsity | L6 |

## 5.2. What the substrate CANNOT do (proven boundaries)

| Boundary | Mechanism-cause | Chapter |
|---|---|---|
| Spiking release of the learned address | Homeostatic MBON set-point: 6–7 mechanisms refuted, a fundamental boundary of LIF homeostasis | L4 |
| Composition (transitive inference) | KC→MBON depression = an operation on the overlap of codes; it does not arise under any of the three addressing schemes | L4 |
| Semantics on the MBON readout | Mantel≈0; there is no metric structure of "similarity" | L4 |
| Self-organization without a rule | ΔNMI=0 (8/8); the weights do not move the RSM without plasticity | L4 |
| Absolute valence of taste | A coin flip of the attractor; an internal anchor is needed (hunger/NPF) | L2/L5 |
| Specificity of state effects | Hunger/state — generic excitability, not address-specific modulation | L5 |
| Cross-connectome transferability of learning | MaleCNS learns one-shot, v783 — does not (H1′ not accepted); F2 open | L4 |
| Free rotation of the bump | The ring = local wells, not a line attractor; drag leads it, free rotation does not | L3 |
| Body and muscles | A data boundary: no FlyGym/flyvis front-end | L6 |

## 5.3. The open frontier (what has remained genuinely not understood)

| # | Frontier | Essence | Path |
|---|---|---|---|
| **F1** | Structure → function | From the connectome one CANNOT predict the learnability of a pair (r=−0.136) — there is no predictive theory of topology→dynamics | Search for predictors on the accumulated corpus of runs |
| **F2** | Cross-connectome transferability | Why MaleCNS learns, v783 — does not: there is no mechanistic answer | Comparative analysis (SFA types, KC sparseness, attractor landscape) |
| **F3** | Data boundaries | The gap atlas is predictive, not validated; peptides without bodyId linking; no body | External data (completeness-plan.md) |

This is not a backlog — it is the next level: the transition from phenomenology
("what the substrate does") to theory ("why this structure does this").

## 5.4. Publication track

| # | Work | Status |
|---|---|---|
| 1 | NT audit of MaleCNS | ✅ Published (Zenodo, DOI 10.5281/zenodo.22975837) + code github.com/neuropunks/malecns-nt-audit |
| 2 | Working memory (cross-connectome replication + DNa02 + null) | Draft `pub/wm_paper.md` (verified) |
| 3 | Crown: learning + attractor root | After the T2 completion runs (SFA ablation, held-out) |
| 4 | Limits of learning (7 boundaries) | Draft `pub/limits_paper.md` (verified) |
| 5 | Monograph (this document) | v0.1 RU; EN version for Zenodo |
| 6+ | CPG/rhythms, gap junctions, speed | As the completion runs are done |

## 5.5. Final word

The project began with the viral news of a "loaded fly brain" and the question:
*what can this thing actually do?* The answer, assembled over 17 days of campaign:

**It can do more than anyone has shown** (learning, memory, rhythms, clocks,
sensorimotor cycle — with reproducibility and on two connectomes).
**It fundamentally cannot** — and we know exactly where and why (release into spikes,
composition, semantics on the readout, absolute valence).

The map is complete. The boundaries are charted. The frontier is theory.

*Andrey A. Smarygin & Vivi. Tyumen, September 2026.*
