# Emergent research update — 27 October 2026

## Scope and evidence boundary

This is a curated public summary of selected simulation work in the private research repository. The underlying code, run tables, and full audit trail are not mirrored here. The findings below are model results under the stated simulation assumptions; they are not direct evidence about real-world AI systems or proof of collective intelligence.

## 060–063 — Alphabet size is not the operative variable by itself

The alphabet sweep initially suggested that some verification-gate failures fade as wrong values become more dispersed across larger alphabets. The follow-up replaced dispersed errors with systematic, per-fact misconceptions and then varied the fraction of facts with sticky errors.

The result changes the interpretation: systematic wrongness recreates the gate's backfire at every tested alphabet size. In the partial-mixture runs, the difference between verified and witness-write performance was approximately linear in the sticky fraction at the tested operating points, and the predicted sign crossings transferred to two additional cells within the reported simulation tolerance. The aggregate probability that two wrong reads agree was not sufficient to explain outcomes across alphabets.

**Interpretation:** error structure and where errors persist matter, not just the size of the answer alphabet. This remains a result of a specified simulation, with finite seeds and selected operating points.

## 067–069 — Detection and repair must be evaluated together

A persistence monitor coupled to re-derivation performed at or above a passive write gate across the tested sticky-error conditions. The κ = 1 boundary test showed that this benefit can survive when the verifier shares the witness's biased fact placement, provided some unbiased region remains. Under global capture, the re-derivation action still improved answer-layer outcomes relative to the gate, but the store itself remained captured; the honest out-of-band verifier was the only tested arm consistently above the frozen baseline.

**Interpretation:** a repair action can improve a metric without restoring the shared store. Store quality and downstream answer quality must be reported separately.

## 070 — Deterministic capture silences the monitor

Experiment 070 removed the independent Bernoulli wrongness draw from the captured write channel and made the same wrong offset persist on every fact. The reverify and persist variants became bit-identical to the gate in all paired seed comparisons (zero nonzero differences across the reported comparisons). The monitor interpreted stable wrongness as persistence, because its statistic measured consecutive agreement rather than correctness.

This supports a narrower conclusion than “monitoring fails”: **a consistency-only monitor cannot distinguish stable truth from stable error in this setup.** The experiment inherits the threshold calibration from Experiment 069 rather than recalibrating it on the deterministic substrate, so the precise detection-threshold behavior needs a separately calibrated follow-up.

## Open questions

- Can a monitor distinguish persistent correctness from persistent error without an independent reference?
- Which outcomes remain robust when the same mechanism is tested across different alphabets, bias structures, and operating points?
- Does any repair improve both the shared store and later agents' answers, rather than improving one while leaving the other captured?
- Which findings survive a fresh implementation, independent audit, and broader parameter sweep?

## How to read this update

The experiments are sequential simulation studies with explicit replication gates and limitations. The summaries are curated for discovery; they are not a systematic literature review, peer-reviewed publication, or claim that the same mechanisms necessarily occur in deployed agents.

The broader research question remains: **How does intelligence change when part of its memory, action, or organization exists outside the agent itself?**
