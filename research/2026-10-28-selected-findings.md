# Selected findings — 28 October 2026 (update 2)

This update extends the public Observatory from Experiment 073 to selected results through Experiment 083. It is a curated summary, not a mirror of the private research repository. All results below are from simulations; they are not direct evidence about real-world AI agents.

## 075–077 — Freshness-gated attention detects freezing, within a boundary

Experiment 075 found that disagreement-triggered attention could increase consultation as an external reference became stale: in the long-frozen condition, realized consultation rose from 0.30 to 0.39 even though consulted policies had negative rescue. Experiment 076 tested ground-truth-free volatility signals. In its tested substrate, aggregate freshness separated live, partly stale, and long-frozen references; the aggregate gate fired roughly three generations after freezing and repaired the long-frozen failure, with a small cost in live and partly stale conditions. Per-fact freshness was less reliable for slow-changing facts.

Experiment 077 swept the freeze boundary to map where the freshness gate's benefit paid for its cost. These are finite simulated conditions, not a universal threshold.

**Boundary:** volatility detects change, not truth. A reference that keeps changing can still be wrong.

## 078–079 — Noise can spoof freshness; a second artifact helps, but not at the world's tempo

Experiment 078 replaced the frozen reference with continuous random writes. The one-sided freshness gate remained fooled: late answer accuracy was 0.8111 with the gate versus 0.8213 for the no-reference baseline in the tested cell.

Experiment 079 compared the reference's change rate with the shared store's change rate. This cross-artifact band caught high-rate noise spoofing: at spoof rate 0.5, accuracy recovered to 0.8214 versus 0.8102 for the one-sided gate and 0.8213 for the no-reference baseline. But spoof rates around the world's own tempo (0.1–0.2 in this substrate) remained inside the band and were not reliably detected.

**Boundary:** self-calibration can detect stopping or excess change; matching the right tempo does not establish that the values track reality.

## 080 — Value disagreement catches a tempo-matched liar, but is symmetric about who is wrong

Experiment 080 added a self-calibrated disagreement ceiling. Against a tempo-matched liar, the gate raised late answer accuracy from 0.8097 to 0.8201, near the no-reference baseline of 0.8213. It also caught a store-complement adversary in the tested conditions.

The mirror-image test exposed the cost: when the store was captured while the reference remained honest, the same value-disagreement gate rejected the honest reference and destroyed most of its rescue benefit. Disagreement identifies a conflict, not which side is trustworthy.

**Limit:** this is evidence from a specified toy substrate and calibration choice, not a general solution to reference validation.

## 081–082A — A disagreement detector can notice capture, then forgive it

Experiment 081 extended the horizon and introduced gradual capture. Gradual attacks delayed detection rather than evading it outright in the tested conditions. But because the baseline accumulated all prior disagreement, the gate eventually reopened after corrupted behavior became established. The detector was therefore transient: it reacted to novelty, not to wrongness as such.

The baseline-length × slack sweep in the first private Experiment 082 found that shorter baselines could accelerate reopening. Tighter slack extended the closed interval; in one tested condition the gate stayed closed through the 60-generation horizon, also continuing to reject the honest reference's rescue. The predicted false-closure cost of shorter baselines did not appear in the tested live condition.

**Boundary:** the warning's duration is a governance parameter, with a direct trade-off between continued protection and recovery from legitimate change.

## 082B — The calibration trace is itself a shared memory

A separate, deliberately minimal gate-level simulation tested attacks on the disagreement history used for calibration (8 conditions × 20 seeds). Rewriting only three old calibration entries per generation prevented the gate from firing in all 20 seeds. Rewriting the full history shortened the protection window.

An immutable anchor consisting of the first ten healthy entries, weighted at 30%, restored detection in all tested attack conditions and prevented reopening within the horizon. Under a legitimate regime change, however, it delayed reopening by about six generations.

**Interpretation:** a detector inherits every integrity limit of the evidence it calibrates against. Protecting the calibration trace helps in this toy model, but raises the next governance question: who controls the anchor?

**Identifier note:** the private repository has two distinct experiments numbered 082. This public Observatory labels them **082A** (baseline-length/slack surface) and **082B** (calibration-trace write authority) to avoid conflating their results.

## 083 — A silent mimic exists, but its damage is bounded by redundancy in this model

Experiment 083 tested a reference that echoes the shared store with a one-generation lag, adds noise at a controlled rate, and thereby keeps its disagreement and volatility signals inside the tested gates' silent regions. At low rates, every tested gate stayed silent. The mimic erased the honest reference's measured benefit, but did not lower answer accuracy below the no-reference baseline in the tested voting design: the echoed value was redundant with a store read the reader already possessed.

At a higher mimic rate, a change-rate leak became visible. The experiment's capture-amplifier prediction failed; the mimic was neutral rather than an amplifier when the store was captured.

**Limit:** this is one committed probe on a narrow toy substrate. It does not show that undetectable memory is harmless in general; the bound depends on the reader's voting rule and available redundant information.

## Cross-experiment interpretation

The sequence exposes three distinct questions that should not be collapsed:

1. **Is the artifact changing?** Volatility can flag freezing or excess change.
2. **Do its values agree with another artifact?** Disagreement can catch a liar, but may reject an honest reference when the store is the corrupted side.
3. **Who controls the calibration evidence?** A writable trace can neutralize a detector that appears sound when its history is trusted.

No single tested ground-truth-free signal resolves all three. The strongest next test is not another unbounded claim about “freshness,” but controlled comparisons where reference truthfulness, store truthfulness, write access to the calibration trace, and reader redundancy vary independently.

## Source and review boundary

Detailed source files, simulation engines, and run tables remain in the private Emergent repository. This public page contains selected summaries and limitations only; no private CSVs, full run tables, credentials, or internal visitor trails are copied here.
