# Week 1 — Draw the Path: Portfolio Sitemap + Toolkit

Track: General AI Fluency · Assignment: Draw the Path · Phase: Setup

## The one person and the one action

- **One person:** the FlyRank mentor/lead who reviews this portfolio and decides *"does this intern build
  honest ML end to end?"* They already know the internship, the lanes and the rubric — the site does not
  need to explain FlyRank, it needs to prove **my** workflow.
- **One action:** **email / contact me.** Every page funnels to one "I have a suggestion — email me" CTA.
  Nothing else is asked of them.
- **Proof statement (claim) — primary draft:**
  > "I take a fuzzy business question and end with an honest, useful ranking — on the FlyRank refresh lane
  > that means a transparent rule that reaches precision@50 0.76 on whole held-out clients and matches the
  > learned models, with the data contract, leakage audit and claim rewrites committed in public every week."
- **Proof statement — short hero variant (if the full one is too long):**
  > "From fuzzy question to honest, shipped ranking — every step committed."

The claim lives in the hero, echoes in each section heading, and is the custom instruction for the
Claude Project below. It is deliberately *not* "a model that beats the rule" — because my evidence says
it doesn't, and the whole point is that the claim survives a hard question.

## Sitemap — 4 pages, nothing more

```text
                ┌─────────────────────────────────────┐
                │  HOME (hero: the claim)             │
                │  "…honest, shipped ranking."        │
                └──────────────────┬──────────────────┘
                                   │
        ┌──────────────┬───────────┴───────────┬──────────────┐
        ▼              ▼                       ▼              ▼
 ┌────────────┐ ┌────────────────┐ ┌──────────────────┐ ┌──────────────┐
 │ THE PAPER  │ │ EVIDENCE       │ │ HOW I WORK       │ │ CONTACT      │
 │ deployed   │ │ notebooks +    │ │ the honest-ML    │ │ "email me"   │
 │ capstone   │ │ receipts       │ │ method + limits  │ │ CTA          │
 └────────────┘ └────────────────┘ └──────────────────┘ └──────────────┘
```

1. **Home** — the claim, one chart, the CTA.
2. **The paper** — the deployed capstone
   ([ananyagujral.github.io/flyrank-ml-internship-start](https://ananyagujral.github.io/flyrank-ml-internship-start/)).
3. **Evidence** — links into the repo: the 10 notebooks, the receipts in `work/outputs/`, the ranked queue.
4. **How I work** — the method that produced the result (leak-aware design, client-grouped validation,
   honest negative results) and the limits I state up front.
5. **Contact** — one CTA, nothing else.

## Toolkit checklist (free accounts)

- [x] **GitHub account** — public repo `AnanyaGujral/flyrank-ml-internship-start` (workspace + portfolio).
- [x] **GitHub Pages** — free hosting for the paper (`portfolio/` served via GitHub Actions).
- [x] **Google account / Colab** — run notebooks with zero local install.
- [x] **Python 3.11 + pandas / numpy / scikit-learn / matplotlib** — local runs (offline).
- [x] **A code editor** — VS Code / Jupyter for the local notebook runs.
- [x] **This repo's skill library** — `skills/README.md` as the router; one skill per task.
- [x] **An AI assistant** — used as a drafter, with me as the engineer (see `work/prompt_ladder.md`).
- [ ] Custom domain — optional, skipped (default `github.io` URL is fine).

## Claude Project — setup, copy-paste

**Project name:** FlyRank Capstone — Refresh Lane

**Custom instructions (paste as-is):**
> You are my drafting assistant on the FlyRank refresh-lane capstone. Work one task per conversation and
> load only the skill that task names from `skills/README.md`. Search the repo before assuming anything is
> missing. Language is observed / measured / directional / decision-support only — no causal claims, no
> "predicted Google's algorithm", no client-identifying details. Every metric must be shown next to the
> base rate; every model compared to the transparent rule on the **same** client-grouped split. The label
> is a window comparison, not a business outcome.

**Context files to add:** `GUIDE.md`, `work/README.md`, `docs/data-dictionary.md`,
`work/capstone_report.md`, `skills/writing-honest-claims/SKILL.md`.

**Opening message I reuse:** *"Read `skills/README.md`, then load the one skill for this task. Search the
repo first, propose a plan, and tell me how I verify the result before you write much code."*

## At least one thing I will change (filled after the pressure test)

My first hero claim said the model **beat** the rule — because an earlier draft carried a borrowed
headline number I could not reproduce. The pressure test ("show me the held-out client count and the base
rate") exposed it: on the honest offline study the rule **matches** the models. So I **changed the claim
from "a model that beats the rule" to "a rule that matches the models, honestly measured"** — the same
evidence, the honest wording, and now it survives the hard question instead of collapsing under it.

## Deliverable checklist

- [x] One person, one action, one CTA
- [x] Proof statement (primary + short hero variant)
- [x] 4-page sitemap
- [x] Toolkit checklist (free accounts only)
- [x] Claude Project setup (instructions + context files + opening message)
- [x] One thing changed after the pressure test
