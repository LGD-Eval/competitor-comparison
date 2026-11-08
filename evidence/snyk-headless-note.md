# Snyk Agent Scan · headless behavior (observed 2026-11-08)

- Package: `snyk-agent-scan` **0.6.11** (module `agent_scan`, entry `agent_scan.run:run`)
- `scan --skills --json --no-bootstrap` → **exit 1, empty stdout/stderr** (no TTY)
- `--ci` → refused: `Running with --ci requires --dangerously-run-mcp-servers.`
- `--verbose` → large debug log; analysis JSON fragments visible (including scanned skill source), but no stable stdout or result file
- Conclusion: **installable, not headless-reproducible in this environment (NV)**
- Comparison: NVIDIA SkillSpector runs headless with `--no-llm`; LGD-Eval runs headless without a TTY.
