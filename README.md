# DreamLake CI/CT evidence

Keeps what a GitHub Actions job produced (videos, metrics, summaries) on the
job's **DreamLake CI/CT** run page, when the job runs on a DreamLake GPU
machine (`runs-on: blacksmith-4vcpu-gpu-rtx5090-ubuntu-2404`).

```yaml
    steps:
      - uses: actions/checkout@v4
      - run: python my_tests.py --out outputs        # your code, writing where it always does
      - uses: dreamlake-ai/cict-evidence@v1           # keep outputs/, pass or fail
        if: always()
```

| Input | Default | |
| --- | --- | --- |
| `path` | `outputs` | Directory your job wrote its outputs to |
| `include` | everything | Globs to keep, one per line (relative to `path`) |
| `exclude` | nothing | Globs to leave out, one per line |

- Copies the files into the run's evidence folder (`$DREAMLAKE_RUN_DIR`) and
  writes the manifest DreamLake uploads from. Your code needs no DreamLake
  imports or paths.
- At most 1024 files, each at most 2 GiB; bigger ones are skipped with a
  warning, the rest are saved.
- On a GitHub-hosted runner it only lists what it would save, so the same
  workflow runs anywhere.
- Needs `python3` (standard library only), which DreamLake GPU machines and
  GitHub-hosted runners have.

Optional files DreamLake understands, written by your job into `path`:

- `metrics.json` — headline numbers the run page charts:
  `[{"step": 0, "values": {"pass_rate": 1.0, "fps": 812}}]`.
- `.dreamrc` — opens the outputs as a replay in DreamLake's viewer (videos and
  charts on one timeline). Examples: [cict-template](https://github.com/dreamlake-ai/cict-template).

Without the action, the same script runs directly:
`python3 evidence.py OUTPUT_DIR [--include GLOB] [--exclude GLOB]`.
