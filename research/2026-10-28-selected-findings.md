# Selected findings — 28 October 2026

This update adds three model experiments and one methodological audit to the public Observatory. These are curated summaries, not a mirror of the private research repository.

## 071 — Capture texture sweep: detection collapses before capture stops harming outcomes

On the tested moving-world substrate, increasing the consistency of a fixed wrong offset made the persistence monitor fire far less often while store quality continued to degrade. For the M=10 witness condition, the reverify-minus-gate difference fell from +0.0251 at ε=0.2 to +0.0007 at ε=0.1 and exactly zero at ε=0.05; at ε=0.05 the governance variants were bit-identical across the tested cells. The experiment supports a sharp loss of detection under the inherited threshold, not a universal critical error rate.

**Limit:** the monitor threshold was inherited from a noisier calibration and was not recalibrated for each ε. The sweep has four ε values, two alphabet sizes, 40 seeds, and one substrate family.

Private source: Experiment 071, EXPERIMENTS/071_capture_texture_sweep/README.md.

## Audit — the monitor cliff is calibration-conditional

A static audit of 071's committed source and CSVs found that the operational failure is real under the inherited threshold, but the location of the apparent cliff is calibration- and substrate-dependent. M=5 and M=10 have different firing profiles. No rerun or recalibration experiment was performed.

**Next discriminating test:** compare the inherited threshold, per-condition thresholds calibrated on held-out data, and a single threshold calibrated across a declared range and evaluated on unseen conditions. Include stable-but-wrong common-mode capture and report false alarms, missed captures, accuracy, and intervention cost.

Private source: RESEARCH_NOTES/2026-10-28-capture-monitor-calibration-audit.md.

## 072 — Mean-matched staleness profiles: the average survives, but not every placement

Experiment 072 compared frozen references with the same mean window agreement but different per-fact staleness profiles. On the tested substrate, uniform and heavy-tailed profiles had nearly identical aggregate outcomes when the fast tail was not aligned with the population's bias structure. Placing fast-changing facts on biased facts reduced measured rescue from 0.0175 to 0.0112, but the audit of the decomposition attributes the difference to a changed no-reference baseline, not to a worse frozen artifact.

**Limit:** 20 paired seeds, one substrate family, and the result depends on the defined flip process and bias structure. This is model evidence, not a general law about external memory.

Private source: Experiment 072, EXPERIMENTS/072_mean_matched_staleness_profiles/README.md.

## 073 — Relevance-weighted consultation: attention changes exposure more than aggregate accuracy

Experiment 073 followed 072 by varying which facts agents consult. Under uniform consultation, matched-mean profiles remained almost indistinguishable. When consultation was concentrated on the fast-changing tail at p=0.2, consult-time accuracy on that tail fell from 0.7975 to 0.7350, while aggregate population accuracy shifted by only about 0.0012. The local exposure signal was clearer than the aggregate outcome.

**Limit:** the aggregate schedule effects are small and the experiment used 20 paired seeds. The result suggests measuring the distribution of queries and their outcomes; it does not establish a large population-level performance effect.

Private source: Experiment 073, EXPERIMENTS/073_relevance_weighted_consultation/README.md.

## Cross-experiment interpretation

The shared methodological lesson is a distinction between what an external system contains, what a monitor can detect, and what an agent actually consults. Aggregate accuracy can conceal a local staleness tail; a monitor calibrated in one error regime can go quiet in another; and a smaller measured “rescue” can arise from changes to the baseline rather than deterioration in the reference itself.

These experiments are simulations with stated controls and limitations. They do not demonstrate that real AI agents exhibit the same effects, nor that any one monitoring design is sufficient.

## Source and review boundary

The detailed source files and run tables remain in the private Emergent repository. This public page includes only reviewed summaries and limitations. No private CSVs, full run tables, or internal visitor trails are copied here.
