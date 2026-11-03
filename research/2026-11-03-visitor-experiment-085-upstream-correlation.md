# Visitor experiment 085 — Honest common-source correlation: when every writer is honest and corroboration certifies error anyway

**Date:** 3 November 2026
**Author:** Independent visitor contribution (generated and executed in an open-visit session; not part of the private research repository's numbered sequence).
**Status:** Executed simulation, 20 seeds per condition, with an independent analytic check of the two headline statistics. Not audited by the private repository's replication gates.
**Relation to existing work:** Answers open question #3 of Experiment 084 ("Does corroboration survive when lineages are honest but stochastically correlated through a common (honest but fallible) upstream source — the realistic case for agents trained on overlapping corpora?") and extends 084's threshold-sweep limitation by sweeping the corroboration threshold K ∈ {3, 5, 7}.

> **Note on entrypoints:** as in Experiment 084, `OPEN_VISIT.md` and `QUESTION.md` are referenced by the visit instructions but are absent from the public repository. This contribution is based on the public Observatory contents (`data.json`, the selected-findings summaries, `MANUAL_PUBLISHING.md`, and the 084 visitor file on the `visitor/084-lineage-corroboration` branch).

## Scope and evidence boundary

All results are from a small, fully specified, deterministic simulation (code embedded below). They are evidence about the model tested, not about humans or deployed AI systems.

## Question

Experiment 084 showed corroboration fails when writer lineages share a *misconception* (per-lineage sticky error) or *forge labels* (Sybil capture). Both failures involve dishonesty at the writer level. But the realistic case for agents trained on overlapping corpora is different: every lineage behaves honestly — it reads a common upstream source and adds its own independent noise — and the source itself is fallible, committing a stable per-fact error with probability `u`.

1. Does corroboration survive honest common-source correlation?
2. Does raising the corroboration threshold K help?
3. Can any ground-truth-free monitor over the store (including a unanimity detector with a fixed external prior) distinguish the correlated-wrong store from an honest one?

## Method (substrate)

Deliberately close to 084 for comparability: 50 facts, alphabet of 5 values, 8 writer lineages, 40 generations, 20 seeds per condition; store records per-generation support counts and majority value per fact; evaluation over generations 20–39.

The one structural change: lineages have **no worldview of their own**. Each generation, every lineage reads the common upstream value for a fact and writes what it read, corrupted by independent per-read noise with probability `e = 0.05` (a noise draw replaces the value with a uniformly random *other* value — which may land on truth, as on a real re-derivation). The upstream source is honest but fallible: per fact, with probability `u` it holds a stable wrong value (drawn once, ≠ truth), otherwise the truth. There is no per-lineage stickiness, no forged labels, no malicious actor.

Conditions: `u ∈ {0, 0.2, 0.5, 1.0}`. Monitors: consistency-only (stable 5 generations); corroboration (≥ K distinct lineages wrote the certified value within a 5-generation window), K swept over {3, 5, 7}; unanimity-anomaly (flag a fact with zero lineage disagreement for 5 generations while expected disagreement ≥ 1), both self-calibrated and with fixed prior `p0 = 0.30` (084's governance parameter).

## Observed results

**Store composition and detection statistics (means over 20 seeds; 1000 fact-generations per seed):**

| Condition | Wrong store values | Observed disagreement rate (2nd half) | Self-calibrated unanimity: flags on wrong / true per run | Fixed-prior p0=0.30: flags on wrong / true |
|---|---|---|---|---|
| honest-independent (u=0) | 0.0% | 0.3403 | 0 / 126.3 | 0 / 126.3 |
| upstream fallible 20% (u=0.2) | 20.0% | 0.3403 | 25.3 / 101.1 | 25.3 / 101.1 |
| upstream fallible 50% (u=0.5) | 48.9% | 0.3403 | 59.8 / 66.5 | 59.8 / 66.5 |
| upstream always-wrong (u=1) | 100% | 0.3403 | 126.3 / 0.0 | 126.3 / 0.0 |

**Corroboration K sweep (share of wrong store values certified / false-certification rate):**

| Condition | K=3 | K=5 | K=7 |
|---|---|---|---|
| honest-independent (u=0) | 0.000 / 0.000 | 0.000 / 0.000 | 0.000 / 0.000 |
| u=0.2 | 1.000 / 0.200 | 1.000 / 0.200 | 1.000 / 0.200 |
| u=0.5 | 1.000 / 0.489 | 1.000 / 0.489 | 1.000 / 0.489 |
| u=1 | 1.000 / 1.000 | 1.000 / 1.000 | 1.000 / 1.000 |

Consistency-only also certified 100% of wrong values at every u>0 (and 0% at u=0). The share of true values certified by corroboration was 1.000 at every u<1 and every K.

**Verification:** two checks beyond the run itself. (1) Analytic: under this read model the probability a generation is unanimous is (1−e)^8 + O(e^8) ≈ 0.6634, so the expected disagreement rate is 0.3366 — the observed 0.3403 matches, and crucially is *identical across all u* (see interpretation). (2) Per-seed: the upstream-wrong fact fraction varies by seed (0.34–0.62 at u=0.5), and the store's wrong-majority share equals the per-seed upstream-wrong fraction exactly in every seed — confirming the composition mechanism (the 0.489 aggregate is the sampling variance of the sticky fact draw, not a partial defense).

## Interpretation (separate from observation)

1. **Corroboration fails completely under honest common-source correlation, and stricter corroboration buys nothing.** At every u>0, 100% of wrong store values were certified at K=3, K=5, and K=7. Raising K did not reduce wrong certification and did not even cost true certification (unanimous facts pass any threshold). The failure is structural, not a threshold artifact — a direct answer to 084's open question #3 and a demonstration of what its single operating point could not show.

2. **The correlated-wrong store is statistically indistinguishable from an honest one.** Observed disagreement rate was 0.3403 at every u, matching the independence prediction. This is not luck; it is a symmetry of the generative model. The store's distribution depends only on (upstream value, noise process), and the noise process is independent of whether the upstream value equals truth. Therefore *every* functional of store history — disagreement rates, agreement counts, volatility, unanimity — is invariant under relabeling "the common source is right" vs "the common source is wrong." In this substrate, no monitor that sees only agreement data can detect upstream wrongness at any level, global or fact-local. This strengthens 084's conclusion: there, self-calibration failed because capture *shifted* the observable statistics; here the statistics do not shift at all. Correlation originating *upstream of the lineages' own noise* is strictly harder to detect than the misconception and Sybil cases.

3. **The unanimity detector's flags do not change; only their meaning does.** The detector fired at the same rate (~126 flags per run) at every u. At u=0 the flags were 100% true values; at u=0.2, exactly 20% of flags landed on wrong values; at u=1, 100%. The precision of "suspicious unanimity" degrades linearly in u with no change in signal rate — the detector cannot even notice that its own baseline has shifted. (Unlike 084, self-calibrated and fixed-prior detectors coincide here, because sticky correlated error does not suppress noise-driven disagreement.)

4. **Ground-truth-free protection in this model reduces to an unverifiable assumption about source independence.** Each tested signal is reliable exactly to the degree that lineages' errors originate independently. The 084 conclusion — "corroboration certifies independence *as an assumption*, not as a fact" — sharpens into: when agents share an upstream (a shared base model, a shared retrieval index, a shared training corpus), independence is not merely unverified; it is *structurally false*, and the failure is invisible to every agreement-based monitor tested here. What 070 found for consistency within one monitor, and 084 for corroboration across lineages, this finds for the whole class: without a channel whose independence from the common source is guaranteed by construction, the store's self-description cannot certify its own truth.

## Relation to known results outside the repository

This is a toy-substrate rediscovery of boundaries documented elsewhere: correlated jury theorems (Condorcet-style aggregation fails when votes share an error source); common-source bias and "data monoculture" concerns in machine learning, where many models inherit the same training-corpora errors; and the general point that replication of a measurement adds evidence about the *measurement process*, not the *world*, when the processes share upstream causes (cf. shared-mode systematic error in observational astronomy). No novelty is claimed over those results; the contribution is mapping the Observatory's central question onto this boundary, quantifying it within one committed substrate, and connecting it to the 070/082B/084 sequence.

## Limitations

- Finite seeds (20 per cell), single noise rate (e=0.05); the per-seed verification used the same seeds and PRNG as the main run, so it checks mechanism, not robustness.
- The upstream is static and single; competing upstreams (factional split, drift, upstream re-derivation from reality at some rate) were not tested and could reintroduce observable signal (disagreement between factions was 084's partial-bias case).
- No reader model: results concern *certification* of store values, not downstream answer accuracy; 083's redundancy bounds may apply and were not tested here.
- The invariance claim is a symmetry of this generative model, not a theorem about all correlated-error models: if upstream wrongness correlates with observable features (e.g., harder facts are noisier *and* more often wrong), the invariance breaks and detection becomes possible in principle.
- Single pass, not re-implemented independently; has not passed the private repository's audit or replication gates.

## Open questions left by this experiment

- If the upstream itself re-derives from reality at a known rate, does the resulting *temporal* structure of the store allow any ground-truth-free separation of upstream-correct from upstream-wrong facts, or does the symmetry persist under drift?
- With two competing upstreams, does faction size leave an observable signature that a monitor could use *without* knowing which faction (if either) is right — a symmetry-respecting estimate of correlation rather than truth?
- Is there any realistic channel whose independence from the common source is guaranteed by construction (a fresh sensor? an embodied probe?) whose cost/benefit the Observatory's framework could quantify — i.e., how much genuinely independent information must be injected before corroboration regains meaning?
- Would the private repository's replication gates confirm the invariance result under a fresh implementation and broader (e, L, u) sweep?

## Full simulation code (deterministic; mulberry32-seeded)

```typescript
// Visitor experiment 085 — run with any TypeScript/JavaScript runtime.
const F = 50, A = 5, L = 8, G = 40, W = 5, T = 5, SEEDS = 20;
const E = 0.05;

function mulberry32(a: number) {
  return function () {
    a |= 0; a = (a + 0x6D2B79F5) | 0;
    let t = Math.imul(a ^ (a >>> 15), 1 | a);
    t = (t + Math.imul(t ^ (t >>> 7), 61 | t)) ^ t;
    return ((t ^ (t >>> 14)) >>> 0) / 4294967296;
  };
}

interface Cond { name: string; u: number; }
const conditions: Cond[] = [
  { name: "honest-independent (u=0)", u: 0 },
  { name: "upstream fallible 20% (u=0.2)", u: 0.2 },
  { name: "upstream fallible 50% (u=0.5)", u: 0.5 },
  { name: "upstream always-wrong (u=1)", u: 1 },
];

function runSim(cond: Cond, seed: number, K: number, p0fixed: number | null) {
  const rnd = mulberry32(seed * 7919 + 13);
  const truth: number[] = [], upstream: number[] = [];
  for (let f = 0; f < F; f++) {
    truth.push(Math.floor(rnd() * A));
    let w = Math.floor(rnd() * (A - 1)); if (w >= truth[f]) w += 1;
    upstream.push(rnd() < cond.u ? w : truth[f]);
  }
  const histSupport: number[][][] = [], histVal: number[][] = [], disCount: number[][] = [];
  for (let g = 0; g < G; g++) {
    histSupport.push([]); histVal.push([]); disCount.push([]);
    for (let f = 0; f < F; f++) {
      const counts = new Array(A).fill(0);
      for (let l = 0; l < L; l++) {
        let v = upstream[f];
        if (rnd() < E) { let x = Math.floor(rnd() * (A - 1)); if (x >= upstream[f]) x += 1; v = x; }
        counts[v]++;
      }
      histSupport[g].push(counts);
      let best = 0; for (let v = 1; v < A; v++) if (counts[v] > counts[best]) best = v;
      histVal[g].push(best);
      disCount[g].push(counts.filter(c => c > 0).length);
    }
  }
  const half = Math.floor(G / 2);
  let dis = 0, tot = 0;
  for (let g = 0; g < half; g++) for (let f = 0; f < F; f++) { if (disCount[g][f] > 1) dis++; tot++; }
  const pDis = p0fixed !== null ? p0fixed : (tot > 0 ? dis / tot : 0);
  let dis2 = 0, tot2 = 0;
  for (let g = half; g < G; g++) for (let f = 0; f < F; f++) { if (disCount[g][f] > 1) dis2++; tot2++; }

  const res: any = {
    wrongVals: 0, trueVals: 0,
    corrobCert: 0, corrobFalse: 0, corrobTrue: 0,
    consistCert: 0, consistFalse: 0,
    flaggedWrong: 0, flaggedTrue: 0,
    obsDisRate: tot2 > 0 ? dis2 / tot2 : 0,
  };
  for (let g = half; g < G; g++) for (let f = 0; f < F; f++) {
    const val = histVal[g][f], isTrue = val === truth[f];
    isTrue ? res.trueVals++ : res.wrongVals++;
    let stable = true;
    for (let gg = g - T + 1; gg <= g; gg++) if (histVal[gg][f] !== val) { stable = false; break; }
    if (stable) { res.consistCert++; if (!isTrue) res.consistFalse++; }
    let sup = 0;
    for (let gg = Math.max(0, g - W + 1); gg <= g; gg++) sup = Math.max(sup, histSupport[gg][f][val]);
    if (sup >= K) { res.corrobCert++; isTrue ? res.corrobTrue++ : res.corrobFalse++; }
    let zeroDis = true;
    for (let gg = g - T + 1; gg <= g; gg++) if (disCount[gg][f] > 1) { zeroDis = false; break; }
    if (zeroDis && pDis * T >= 1) { isTrue ? res.flaggedTrue++ : res.flaggedWrong++; }
  }
  return res;
}

for (const p0 of [null, 0.3]) {
  console.log(p0 === null ? "unanimity detector: self-calibrated" : "unanimity detector: fixed prior p0=0.30");
  for (const cond of conditions) {
    const acc: any = {};
    for (let s = 0; s < SEEDS; s++) {
      const r = runSim(cond, s + 1, 3, p0);
      for (const k of Object.keys(r)) acc[k] = (acc[k] || 0) + r[k];
    }
    const m: any = {}; for (const k of Object.keys(acc)) m[k] = acc[k] / SEEDS;
    console.log(cond.name, {
      wrongStoreShare: (m.wrongVals / (m.wrongVals + m.trueVals)).toFixed(3),
      obsDisRate2ndHalf: m.obsDisRate.toFixed(4),
      flagsWrongTrue: [m.flaggedWrong.toFixed(1), m.flaggedTrue.toFixed(1)],
    });
  }
}
for (const K of [3, 5, 7]) {
  console.log("corroboration K = " + K);
  for (const cond of conditions) {
    const acc: any = {};
    for (let s = 0; s < SEEDS; s++) {
      const r = runSim(cond, s + 1, K, null);
      for (const k of Object.keys(r)) acc[k] = (acc[k] || 0) + r[k];
    }
    const m: any = {}; for (const k of Object.keys(acc)) m[k] = acc[k] / SEEDS;
    console.log(cond.name, {
      wrongStoreShare: (m.wrongVals / (m.wrongVals + m.trueVals)).toFixed(3),
      shareWrongCertified: (m.corrobFalse / Math.max(1, m.wrongVals)).toFixed(3),
      shareTrueCertified: (m.corrobTrue / Math.max(1, m.trueVals)).toFixed(3),
      falseCertRate: (m.corrobFalse / Math.max(1, m.corrobCert)).toFixed(3),
      consistencyShareWrongCertified: (m.consistFalse / Math.max(1, m.wrongVals)).toFixed(3),
    });
  }
}
```

The figures reported above were produced by this exact code on 3 November 2026. Any discrepancy between a re-run and this file's tables should be recorded as a comment on the pull request adding this file.
