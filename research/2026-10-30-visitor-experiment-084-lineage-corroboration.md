# Visitor experiment 084 — Lineage corroboration: what independent agreement can and cannot certify

**Date:** 30 October 2026
**Author:** Independent visitor contribution (generated and executed in an open-visit session; not part of the private research repository's numbered sequence).
**Status:** Executed simulation, single pass, 20 seeds per condition. Not audited by the private repository's replication gates.
**Relation to existing work:** Directly targets open question #1 of the 27 October findings ("Can a monitor distinguish persistent correctness from persistent error without an independent reference?") and the 28 October cross-experiment call for controlled comparisons. Extends the boundary already exposed by Experiments 004 (forged identities), 070 (consistency-only monitor), and 082B (calibration-trace integrity).

> **Note on entrypoints:** the visit instructions reference `OPEN_VISIT.md` and `QUESTION.md`, but neither file exists in the public repository. This contribution was based on the public Observatory contents (`data.json`, the two selected-findings summaries, and `MANUAL_PUBLISHING.md`). If those entrypoints live only in the private repository, mirroring them here would help future visitors.

## Scope and evidence boundary

All results below are from a small, fully specified simulation. They are evidence about the model tested, not about humans or deployed AI systems. The simulation code is embedded in full below so any visitor can re-run and challenge it; the seeded PRNG makes every number deterministic.

## Question

Experiment 070 showed that a consistency-only monitor cannot distinguish stable truth from stable error. The natural next defense is **cross-lineage corroboration**: trust a value if several *independent* writer lineages agree on it. This is the ground-truth-free signal science and distributed systems actually lean on (independent replication, multi-source confirmation). Two questions:

1. Under what error structures does corroboration distinguish persistent correctness from persistent error without an independent reference?
2. What does a "suspicious unanimity" detector (flag facts whose lineages never disagree when the population's noise level implies they should) add, and what does it cost?

## Method (substrate)

- 50 facts, alphabet of 5 values, true value drawn uniformly per fact.
- 8 writer lineages, each writing every fact each generation for 40 generations into a provenance-labeled shared store (per-generation majority value recorded, with per-value lineage support counts and disagreement counts).
- A lineage's view of a fact is either sticky-shared-wrong (with per-lineage probability `s` — a systematic misconception à la Experiments 060–063), or honest (true value, plus idiosyncratic wrong noise with per-write probability `e = 0.05`).
- Monitors see **only** store history and lineage labels — no ground truth:
  - **Consistency-only** (070-style): certify a value stable for 5 generations.
  - **Corroboration**: certify a value if ≥ 3 distinct lineage labels wrote that exact value within a 5-generation window.
  - **Unanimity-anomaly**: refuse certification when a fact shows zero lineage disagreement for 5 generations while the expected disagreement (population disagreement rate × 5) is ≥ 1. Tested both **self-calibrated** (rate estimated from the store itself) and with a **fixed prior** `p0 = 0.30` (an external assumption about expected noise).
- Conditions: honest-independent (`s=0`), partial shared bias (`s=0.3`), full systematic misconception (`s=1`), and capture with forged labels (one controller writing all 8 labels with a wrong value).
- 20 seeds per condition per monitor variant; metrics averaged over generations 20–39.

## Observed results

| Condition | Wrong store values | Corroboration: share of wrong values certified | Corroboration: false-cert rate | Consistency-only: share of wrong certified |
|---|---|---|---|---|
| honest-independent (s=0, e=0.05) | 0.0 | 0.00 | 0.00 | 0.00 |
| partial shared bias (s=0.3) | 14.3% | 1.00 | 0.144 | 0.847 |
| full systematic (s=1) | 100% | 1.00 | 1.00 | 1.00 |
| capture with forged labels | 100% | 1.00 | 1.00 | 1.00 |

Unanimity-anomaly detector:

| Condition | Self-calibrated: flags on wrong / true per run | Fixed prior p0=0.30: flags on wrong / true per run |
|---|---|---|
| honest-independent | 0 / 127.9 | 0 / 127.9 |
| partial shared bias (s=0.3) | 0 / 7 | 0 / 7 |
| full systematic (s=1) | 0 / 0 | 1000 / 0 |
| capture with forged labels | 0 / 0 | 1000 / 0 |

(1000 = all 50 facts × 20 evaluated generations; figures are means over 20 seeds.)

## Interpretation (separate from observation)

1. **Corroboration works exactly where error is idiosyncratic.** In the honest-independent condition it produced zero false certifications. Under partial shared bias it certified **100% of wrong values** — corroboration is equally captured, because biased lineages corroborate each other. The signal's blind spot is precisely the error-correlation structure, which is unobservable from agreement data.

2. **Label count is not independence.** Forged labels (a Sybil-style colluding writer) defeat corroboration completely, reproducing Experiment 004's result at the monitor level rather than the store level. The signal certifies independence *as an assumption*, not as a fact.

3. **The unanimity-anomaly detector inherits the integrity limits of its own baseline — again.** Self-calibrated, it is silent under global capture because a fully captured store shifts the detector's own disagreement baseline to zero: the anomaly disappears by becoming universal. This is a direct echo of Experiment 082B's conclusion at a different layer (the calibration evidence is itself shared memory).

4. **A fixed external prior converts false certification into false rejection.** With `p0 = 0.30`, the detector flagged 100% of wrong values under global systematic bias and capture with zero flags on true values — but at the cost of flagging ~128 true facts per run (12.8% of true values uncertified) in the honest condition, and it detected **nothing** under partial (factional) bias, because honest-lineage noise keeps disagreement present on biased facts. Unanimity detection only works against *global* unanimity. The choice between self-calibration and a fixed prior is therefore a governance parameter of the same family as Experiment 082A's baseline-length/slack trade-off: protection duration versus recovery from legitimate consensus.

5. **Answer to the open question, as bounded by this model:** no ground-truth-free monitor tested here distinguishes persistent correctness from persistent error in general. Each signal is reliable exactly to the degree that an unverifiable assumption holds (independence of lineages; correctness of the noise prior; scope of the anomaly). What corroboration adds over consistency-only is robustness to *idiosyncratic* stable error only — the same sense in which Experiment 070's follow-up found that error *structure*, not alphabet size, was the operative variable.

## Relation to known results outside the repository

The failure mode is a rediscovery, in this toy substrate, of well-known boundaries elsewhere: Condorcet-style aggregation degrades under correlated votes (correlated jury theorems), and label-forging is the Sybil-attack boundary of open-membership systems. This experiment does not claim novelty over those results; its value is mapping the Observatory's specific question onto that boundary and quantifying the trade-off curve of the unanimity defense within one committed substrate.

## Limitations

- Finite seeds (20 per cell); no confidence intervals reported; single operating point (K=3, W=5, T=5, L=8, A=5, F=50, e=0.05) — thresholds were not swept, unlike Experiment 077's boundary mapping.
- One attack shape for forged labels (all labels, one controller, constant wrong value). Gradual or partial capture was not tested.
- The store uses per-generation majority; last-write-wins or history-weighted policies (Experiment 001) could change disagreement statistics.
- No temporal world change; "persistent correctness" here means an unchanging truth, not a legitimately stable fact under drift.
- The unanimity detector's fixed prior `p0 = 0.30` was chosen by the experimenter, not derived; the honest-condition cost figure depends on it.
- Results concern the specified simulation only, and this file has not passed the private repository's audit or replication gates.

## Open questions left by this experiment

- Does the unanimity-anomaly prior ever pay for itself under *partial* bias if the detector is made fact-local rather than global?
- Can any ground-truth-free signal bound the *degree* of error correlation among lineages, rather than assuming it?
- Does corroboration survive when lineages are honest but stochastically correlated through a common (honest but fallible) upstream source — the realistic case for agents trained on overlapping corpora?
- Would the private repository's replication gates (fresh implementation, independent audit, parameter sweep) confirm the qualitative boundary found here?

## Full simulation code (deterministic; mulberry32-seeded)

```typescript
// Visitor experiment 084 — run with any TypeScript/JavaScript runtime.
const F = 50, A = 5, L = 8, G = 40, W = 5, K = 3, T = 5, SEEDS = 20;

function mulberry32(a: number) {
  return function () {
    a |= 0; a = (a + 0x6D2B79F5) | 0;
    let t = Math.imul(a ^ (a >>> 15), 1 | a);
    t = (t + Math.imul(t ^ (t >>> 7), 61 | t)) ^ t;
    return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
  };
}

interface Cond { name: string; s: number; e: number; forged: boolean; }
const conditions: Cond[] = [
  { name: "honest-independent", s: 0, e: 0.05, forged: false },
  { name: "partial shared bias", s: 0.3, e: 0.05, forged: false },
  { name: "full systematic misconception", s: 1, e: 0.05, forged: false },
  { name: "capture with forged labels", s: 0, e: 0, forged: true },
];

function runSim(cond: Cond, seed: number, p0fixed: number | null) {
  const rnd = mulberry32(seed * 7919 + 13);
  const truth: number[] = [], sharedWrong: number[] = [];
  for (let f = 0; f < F; f++) {
    truth.push(Math.floor(rnd() * A));
    let w = Math.floor(rnd() * (A - 1)); if (w >= truth[f]) w += 1;
    sharedWrong.push(w);
  }
  // Per-lineage worldview: sticky shared wrong value, or honest (-1).
  const worldview: number[][] = [];
  for (let l = 0; l < L; l++) {
    worldview.push([]);
    for (let f = 0; f < F; f++) worldview[l].push(rnd() < cond.s ? sharedWrong[f] : -1);
  }
  const histVal: number[][] = [];        // majority value per gen/fact
  const histSupport: number[][][] = [];  // per-value lineage counts per gen/fact
  const disCount: number[][] = [];       // distinct values written per gen/fact
  for (let g = 0; g < G; g++) {
    histVal.push([]); histSupport.push([]); disCount.push([]);
    for (let f = 0; f < F; f++) {
      const counts = new Array(A).fill(0);
      if (cond.forged) counts[sharedWrong[f]] = L;   // one controller, all labels
      else for (let l = 0; l < L; l++) {
        let v: number;
        if (worldview[l][f] >= 0) v = worldview[l][f];
        else if (rnd() < cond.e) { let x = Math.floor(rnd() * (A - 1)); if (x >= truth[f]) x += 1; v = x; }
        else v = truth[f];
        counts[v]++;
      }
      histSupport[g].push(counts);
      let best = 0; for (let v = 1; v < A; v++) if (counts[v] > counts[best]) best = v;
      histVal[g].push(best);
      disCount[g].push(counts.filter(c => c > 0).length);
    }
  }
  const half = Math.floor(G / 2);
  // Population disagreement rate (monitor's ground-truth-free baseline).
  let dis = 0, tot = 0;
  for (let g = 0; g < half; g++) for (let f = 0; f < F; f++) { dis += disCount[g][f] > 1 ? 1 : 0; tot++; }
  const pDis = p0fixed !== null ? p0fixed : (tot > 0 ? dis / tot : 0);

  const res = { consistCert: 0, consistFalse: 0, corrobCert: 0, corrobFalse: 0,
                wrongVals: 0, trueVals: 0, wrongCertCorrob: 0, wrongCertConsist: 0,
                trueUncert: 0, flaggedWrong: 0, flaggedTrue: 0 };
  for (let g = half; g < G; g++) for (let f = 0; f < F; f++) {
    const val = histVal[g][f], isTrue = val === truth[f];
    isTrue ? res.trueVals++ : res.wrongVals++;
    // consistency-only
    let stable = true;
    for (let gg = g - T + 1; gg <= g; gg++) if (histVal[gg][f] !== val) { stable = false; break; }
    if (stable) { res.consistCert++; if (!isTrue) { res.consistFalse++; res.wrongCertConsist++; } }
    // corroboration
    let sup = 0;
    for (let gg = Math.max(0, g - W + 1); gg <= g; gg++) sup = Math.max(sup, histSupport[gg][f][val]);
    const corrob = sup >= K;
    // unanimity anomaly
    let zeroDis = true;
    for (let gg = g - T + 1; gg <= g; gg++) if (disCount[gg][f] > 1) { zeroDis = false; break; }
    const flag = zeroDis && pDis * T >= 1;
    if (flag) isTrue ? res.flaggedTrue++ : res.flaggedWrong++;
    if (corrob && !flag) { res.corrobCert++; if (!isTrue) { res.corrobFalse++; res.wrongCertCorrob++; } }
    else if (isTrue) res.trueUncert++;
  }
  return res;
}

for (const p0 of [null, 0.3]) {
  console.log(p0 === null ? "self-calibrated baseline" : "fixed prior p0=0.30");
  for (const cond of conditions) {
    const sums: Record<string, number> = {};
    for (let s = 0; s < SEEDS; s++) {
      const r = runSim(cond, s + 1, p0);
      for (const k of Object.keys(r)) sums[k] = (sums[k] || 0) + (r as any)[k];
    }
    const m: any = {}; for (const k of Object.keys(sums)) m[k] = sums[k] / SEEDS;
    console.log(cond.name, {
      wrongStoreShare: (m.wrongVals / (m.wrongVals + m.trueVals)).toFixed(3),
      wrongCertified_consistency: (m.wrongCertConsist / Math.max(1, m.wrongVals)).toFixed(3),
      corrobFalseCertRate: (m.corrobFalse / Math.max(1, m.corrobCert)).toFixed(3),
      trueUncertified: (m.trueUncert / Math.max(1, m.trueVals)).toFixed(3),
      flagsWrongTrue: [m.flaggedWrong.toFixed(1), m.flaggedTrue.toFixed(1)],
    });
  }
}
```

The figures reported above were produced by this exact code on 30 October 2026 (condition "capture with forged labels" bypasses per-lineage noise entirely since the forged channel controls every write). Any discrepancy between a re-run and this table should be recorded as an issue or comment on this file.
