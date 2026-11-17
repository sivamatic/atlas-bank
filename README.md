# atlas-bank: Atlas for a bank

A finished Atlas: an internal assistant that answers from company documents, cites its sources, never shows a user a document they can't read, and opens tickets only after a person approves them. This build serves advisors at a fictional retail and private bank. The company, people and policies are fictional.

What it shows: research entitled by region, fee schedules by role, no personal advice.

| Read | |
|---|---|
| [Design doc](examples/bank/docs/design.md) | problem, users, architecture, trade-offs, eval results, risks |
| [Demo run](examples/bank/demo.md) | a transcript of real CLI and HTTP runs, copied verbatim |
| [Golden set](examples/bank/golden.json) | the eval cases the merge gate scores |

It is the same code as [atlas-starter](https://github.com/sivamatic/atlas-starter) with every week's solution applied. Only the data changes: `ATLAS_DATA_DIR=examples/bank`.

## Run it

You need Python 3.11 or later. After the install, everything runs offline: a deterministic stand-in model (`LLM_MODE=stub`) and hash embeddings, so no key and no network.

```
git clone https://github.com/sivamatic/atlas-bank.git
cd atlas-bank
python -m venv .venv
.venv\Scripts\activate          # macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt

$env:ATLAS_DATA_DIR="examples/bank"; $env:LLM_MODE="stub"     # macOS/Linux: export ATLAS_DATA_DIR=examples/bank LLM_MODE=stub
python -m atlas.cli ask --user kenji "What is the penalty if a client closes a Fixed Saver early?"
python tools/eval_gate.py --data-dir examples/bank --compare
python -m pytest -q
```

`eval_gate.py` prints recall, citation precision, refusal accuracy and the permission-leak count, and exits non-zero if any falls below `evals.thresholds.json`. CI runs the tests and the gate on every push (`.github/workflows/ci.yml`).

To use a real model, set `LLM_MODE=live` and `LLM_BASE_URL` to any OpenAI-compatible endpoint (see `.env.example`). The stub model tests retrieval, permissions and refusals, not a real model's writing.

## Make your own

Copy `examples/bank` to a new folder, write your documents with an `acl:` line each, write the users and at least ten golden cases, point `ATLAS_DATA_DIR` at it and run the gate.

---

Part of [AI for Engineers](https://ai4e.phenome.app). The course explains why each piece is built the way it is.
