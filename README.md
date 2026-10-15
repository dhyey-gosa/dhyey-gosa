# DHYEY-GOSA

**AI engineer. Agents, evaluation, robotics.**

Most AI projects are a prompt and a README claiming it works. I build the
measurement first.

---

## Featured

### [`pr-review-agent`](https://github.com/dhyey-gosa/pr-review-agent) `Python`

**An LLM code reviewer whose eval harness is the product.**

It shipped working, with an eval report reading `F1: 0.163`. Investigating that
number was the interesting part, because **most of the loss was in the
measurement, not the agent.** Seven defects in the eval code itself:

| Defect | Effect |
|---|---|
| Matcher never compared `file_path` | A finding on the wrong file scored as a true positive |
| Severity accepted at-or-above | A "critical" satisfied an expected "suggestion" |
| Two incompatible FP definitions, summed | `precision` was not a precision |
| Case passed on `tp > 0` | Finding 1 of 3 expected defects counted as a pass |
| `except Exception` swapped in a different prompt | On a rate limit the eval measured **a different system than it claimed to** |
| `synthesis_agent` written, never called | The only FP filter was dead code. This is where 20 of the FPs came from. |
| 4 cases × 4 specialists = 16 concurrent requests | Self-inflicted rate limiting; 12 of 27 cases silently degraded |

After fixing the measurement:

```
detection F1        0.163  ->  0.776
precision           0.167  ->  0.792
false positives     20     ->  5
exactly correct             17 / 27
localisation                100%  (19/19)
```

Features a **dev / held-out split** (no overlap, asserted in tests), an
**ablation mode** that moves one variable at a time, a **degradation contract**
that refuses to publish a number from a run where any stage failed, and latency
and cost tracked alongside quality.

Four real bugs were found by running it, not reading about it: a diff parser
that sent the model header-only diffs (so it correctly answered `[]` every time),
a pinned model that had been silently 404ing for months, a daily token ceiling
that made a healthy agent look dead, and an EOL SDK that hung.

---

### Selected work

| Repo | What it is |
|---|---|
| [`LLM-From-Scratch`](https://github.com/dhyey-gosa/LLM-From-Scratch) | Transformer components built up from the math |
| [`Looped-Transformer`](https://github.com/dhyey-gosa/Looped-Transformer) | Single thin looped layer replacing a deep stack, from the paper |
| [`robotics-guide`](https://github.com/dhyey-gosa/robotics-guide) | Python implementation of *Modern Robotics* |
| [`self-healing-RAG-pipeline`](https://github.com/dhyey-gosa/self-healing-RAG-pipeline) | RAG that detects bad retrieval and recovers, rather than only retrieving |
| [`coin-data`](https://github.com/dhyey-gosa/coin-data) | 4.2M OHLCV bars, silver and gold, spot and futures |
| [`sec-scrapper`](https://github.com/dhyey-gosa/sec-scrapper) | SEC filings scraper |

---

## How I work

- **Measure before you claim.** If I cannot produce a number, I do not say it works.
- **Fix the instrument first.** The first instinct when a metric looks bad is to
  improve the system. Usually it is the metric.
- **Publish the failures.** The baseline in my flagship repo ships with a
  "3 of 27 cases degraded, do not quote this" warning attached. A number nobody
  trusts is worse than no number.
- **Constraints are a filter, not an excuse.** No CUDA on this machine, so
  everything runs CPU-only. That is exactly what edge and drone work looks like.

## Looking for

An **AI/ML engineering internship**. Agent systems, evaluation infrastructure,
or robotics / edge deployment.

## Stack

`Python` `PyTorch` `LangGraph` `FastAPI` `PyBullet` `ROS2` `Docker` `Postgres`

---

<sub>Built to be read in ten seconds. The long version is in the
[flagship repo](https://github.com/dhyey-gosa/pr-review-agent).</sub>