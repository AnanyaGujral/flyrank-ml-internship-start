# Prompt Ladder — from "Explain this data" to a reusable refresh-queue prompt

**Track:** Machine Learning · **Context:** FlyRank refresh lane (the capstone)
**Method:** one weak baseline + five versions, each adding exactly **one** named layer. Each version was
run against the assistant and excerpted below, so the change in the *output* can be compared rather than
remembered. Outputs are lightly condensed for length; the headers, verdicts and key rows are kept as they
came back. The data is the anonymized slice only — no client-identifying detail appears in any prompt or
output.

---

## V0 — Baseline (weak prompt)

> Explain this data.

**Excerpt of the output:**
> This looks like a spreadsheet of pages with columns for impressions, clicks, and average position, plus
> a date. Impressions means how many times your page was shown in search, clicks means how often people
> clicked it, and position is roughly where it ranks… You could use this to see which pages get the most
> attention, or maybe spot pages with high impressions but low clicks. If you want, I can make a chart or
> help you decide what to look at.

**Notes**
- **Changed in the prompt:** nothing — the baseline. One sentence: no goal, no audience, no shape.
- **Improved in the output:** nothing yet; it described the columns I already knew and offered to "help" without doing anything.
- **Still failed:** no decision, no ranking, no honesty problem even named; it hedged with "maybe/roughly".
- **Next:** state the goal — the decision I actually need help with.

---

## V1 — Layer added: a clear goal

> Explain this data to help me decide which pages to review first. I can only look at a few hundred pages
> a week, but the portfolio has thousands.

**Excerpt of the output:**
> With a few hundred reviews a week and thousands of pages, you want a priority order, not a description.
> Your best entry point is pages that get seen but don't get clicked: high impressions, low CTR. A quick
> score would be `impressions × (1 − ctr)`… Sort descending and take the top few hundred. Pages with many
> impressions and almost no clicks are the ones most likely to be "visible but under-earning".

**Notes**
- **Changed in the prompt:** added the **decision** and the **budget** (a few hundred/week, thousands of pages).
- **Improved in the output:** it switched from describing columns to proposing a ranking, and invented a first score.
- **Still failed:** it never asked where the label comes from; "high impressions × low CTR" is a guess, and it treats impressions as risk when volume only makes a page *matter*.
- **Next:** pin the data contract so the ranking can't quietly read the future.

---

## V2 — Layer added: the data contract (no leakage)

> …The file stores two stacked 30-day windows (`*_prev_30d` = days 31–60, `*_last_30d` = days 1–30). I want
> the decision to use only the *prior* window, and the outcome to be the *later* window. Do not use any
> column that belongs to the later window, or `trend_direction` / `trend_pct`.

**Excerpt of the output:**
> Good — that makes this a clean prediction, not a same-row lookup. Decision window = prior 30 days;
> outcome = later 30 days. Label: `declined_next_30d = impressions_last_30d < 0.8 × impressions_prev_30d`.
> Features allowed: everything from the prior window plus static metadata. **Excluded:** `trend_direction`,
> `trend_pct` (they define the label), all `*_last_30d` (the outcome), all `*_90d` totals and their rates,
> the tiers built from them, and the IDs. Quick check: adding `impressions_last_30d` as a "feature" should
> send the score toward 1.0 — that's the leak, not skill.

**Notes**
- **Changed in the prompt:** named the **two windows** and the **exclusion list**.
- **Improved in the output:** it stopped guessing and defined a label with an auditable rule, and it named the leak test before running it.
- **Still failed:** no output shape yet — it would hand me prose, not a queue I can act on.
- **Next:** specify the shape of the answer.

---

## V3 — Layer added: the output shape

> Return (1) a ranked table of the top pages with a score and a feature-only reason code per row, and (2) a
> baseline score I can compare any model against.

**Excerpt of the output:**
> Two outputs. **Baseline (transparent rule):** `score = (ctr_prev ≤ 0.5 × pool_median_ctr) ×
> log1p(impressions_prev_30d)`, reason codes `ctr_gap`, `no_clicks`, `high_exposure`. **Queue:** sort by
> score, keep `content_id, score, reason_code, impressions_prev_30d, clicks_prev_30d, ctr_prev`. Pool =
> `impressions_prev_30d ≥ 100` → 18,010 pages. On a first look the top of the queue is dominated by
> `ctr_gap+no_clicks`.

**Notes**
- **Changed in the prompt:** added the **deliverable shape** — a ranked table plus a named baseline.
- **Improved in the output:** it produced something an editor can actually work, and fixed the pool floor.
- **Still failed:** it reported no *measured* quality and no comparison to a base rate — a pretty queue with no reason to trust it.
- **Next:** force honesty about the numbers.

---

## V4 — Layer added: honesty constraints

> Evaluate on clients the model has never seen, and always show precision@K next to the base rate. Use
> observed/measured/directional language — no causal claims, no "predicting Google".

**Excerpt of the output:**
> Held-out clients (GroupShuffleSplit, 30% of clients, seed 42) → 9 clients / 3,571 pages / **base rate
> 0.567**. Precision@50: transparent rule **0.76**, logistic regression **0.76**, random forest **0.64**,
> gradient boosting **0.58**, rule+model blend **0.78**. ROC-AUC 0.58–0.61, i.e. barely over chance.
> **Measured:** the learned models do **not** beat the transparent rule. Also note: a random *page* split
> lifts the forest to P@50 0.96 because 27 of 28 test clients leak into training — that number is an
> artifact, not a result.

**Notes**
- **Changed in the prompt:** added the **validation design** and the **honesty constraints**.
- **Improved in the output:** this is the turn where the work became trustworthy — base rate visible, split named, negative result stated plainly.
- **Still failed:** the numbers aren't reproducible from the message alone — no seed, no command, no pointer to the receipts.
- **Next:** make it re-runnable.

---

## V5 — Layer added: reproducibility

> Add the exact command to reproduce this from a fresh clone, the seed, and the file each number traces
> back to.

**Excerpt of the output (the reusable prompt, final):**
> **Re-runnable package.** *Command:* `pip install -r requirements.txt`, then run
> `work/notebooks/w01…w07` and `work/notebooks/capstone.ipynb` top to bottom (offline; no token).
> *Seeds:* `random_state=42`; client-grouped `GroupShuffleSplit(0.30)` and `GroupKFold(5)`. *Receipts:*
> every headline number traces to `work/outputs/*.json` (`w05_model_metrics.json`,
> `w06_validation_metrics.json`, …), and the paper artifacts regenerate into `output/`. *Reusable prompt:*
> "Using only the prior-30-day window, build a transparent rule and three models to rank already-visible
> pages by next-window decline; evaluate on whole held-out clients vs the base rate; return a ranked queue
> with feature-only reason codes; report receipts, seeds and limits."

**Notes**
- **Changed in the prompt:** added **reproducibility** — seed, command, and receipt provenance.
- **Improved in the output:** the whole chain is now checkable by a stranger with a fresh clone.
- **Still failed:** nothing structural — only that a single snapshot bounds the claim (stated as a limit, not hidden).
- **Next:** reuse the final prompt as a template on the next ranking task.

---

## What the ladder shows

Each version added **one** layer — goal → data contract → output shape → honesty → reproducibility — and
each layer changed a *specific* defect in the previous output. The two layers that mattered most for
trust were not modelling choices at all: **stating the label windows** (V2) and **reporting the base
rate** (V4). The final prompt is boring on purpose: it forces the honest answer (the rule matches the
models) instead of an impressive one.
