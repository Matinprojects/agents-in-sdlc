---
name: autoresearch
description: Run Karpathy's autoresearch loop - an autonomous agent that repeatedly edits train.py of a small single-GPU LLM training setup (nanochat-style), trains for a fixed 5-minute budget, keeps changes that lower val_bpb and reverts the rest, logging every experiment to results.tsv. Use when the user says "autoresearch", asks to start/resume an autonomous overnight training-research run, or wants the agent to iterate on train.py to minimize val_bpb. Requires a single NVIDIA GPU (H100-class tested) plus uv.
---

# autoresearch

Source: https://github.com/karpathy/autoresearch (MIT). This skill wraps the
repo's `program.md` so it can be invoked from any session.

## 0. Preflight (do this before anything else)

1. **GPU check**: run `nvidia-smi`. If it fails or shows no NVIDIA GPU, STOP and
   tell the user: upstream autoresearch needs a single NVIDIA GPU. Suggest the
   forks instead: `miolini/autoresearch-macos` or `trevin-creator/autoresearch-mlx`
   (Mac), `jsegov/autoresearch-win-rtx` (Windows), `andyluo7/autoresearch` (AMD).
   Do not try to run training on CPU.
2. **Locate the repo**: if the current directory already contains `prepare.py`,
   `train.py` and `program.md`, use it. Otherwise ask the user where to put it and
   `git clone https://github.com/karpathy/autoresearch.git` there, then `cd` in.
3. **uv**: if `uv` is missing, install it (`curl -LsSf https://astral.sh/uv/install.sh | sh`),
   then `uv sync`.
4. **Data**: if `~/.cache/autoresearch/` has no data shards or tokenizer, run
   `uv run prepare.py` (one-time, ~2 min; downloads from Hugging Face).
5. If the repo has its own `program.md` that differs from the rules below, the
   repo's `program.md` wins - the human iterates on that file.

## 1. Setup (with the user)

1. **Agree on a run tag** based on today's date (e.g. `oct19`). Branch
   `autoresearch/<tag>` must not already exist.
2. `git checkout -b autoresearch/<tag>` from current master.
3. Read `README.md`, `prepare.py` (read-only), `train.py` (the file you edit).
4. Create `results.tsv` with only the header row (tab-separated):
   `commit	val_bpb	memory_gb	status	description`
5. Confirm setup with the user. After confirmation, start the loop.

## 2. Rules

- You may ONLY modify `train.py`. Architecture, optimizer, hyperparameters,
  batch size, model size - all fair game.
- Never modify `prepare.py` or the `evaluate_bpb` harness. Never add packages.
- Goal: lowest `val_bpb`. Training always runs a fixed 5-minute budget.
- VRAM is a soft constraint: modest increases are fine for real gains.
- Simplicity criterion: tiny gains that add ugly complexity are not worth it;
  equal results with less code is a win.
- First run is always the unmodified baseline.

## 3. The experiment loop (LOOP FOREVER)

1. Check git state (branch/commit).
2. Edit `train.py` with one experimental idea.
3. `git commit`.
4. `uv run train.py > run.log 2>&1` (never tee; don't flood context).
5. `grep "^val_bpb:\|^peak_vram_mb:" run.log`
6. Empty output = crash: `tail -n 50 run.log`, fix trivial bugs and re-run;
   if the idea is fundamentally broken, log `crash` and move on.
7. Append to `results.tsv` (do NOT commit it): short commit hash, val_bpb
   (0.000000 on crash), memory_gb = peak_vram_mb/1024 rounded to .1f (0.0 on
   crash), status `keep`/`discard`/`crash`, short description.
8. Improved (lower val_bpb) -> keep the commit and advance.
9. Equal or worse -> `git reset --hard` back to the previous kept commit.

- **Timeout**: kill any run exceeding 10 minutes; treat as failure and revert.
- **NEVER STOP**: once the loop starts, do not ask the human whether to
  continue. They may be asleep. If out of ideas, re-read the code, combine
  near-misses, try more radical changes. Run until manually interrupted.
