---
name: autoresearch
description: Fully autonomous research loop with council oversight — runs until marginal benefit is exhausted.
---

# Skill: Autoresearch Lab (`/autoresearch`)

## Objective
Run a fully autonomous research loop combining discovery → extraction → gap analysis → council review with dual-metric termination. Stops when both speed improvement and information gain plateau.

## How It Works

```
Query → Discovery → Extraction → Gap Analysis
              ↑              ↓
              └── Re-Query ←─┘
                       │
                  Synthesis
                       │
               ┌──────┴──────┐
               │ COUNCIL PANEL│
               │ Karpathy     │
               │ Klein        │
               │ Gigerenzer   │
               └──────┬──────┘
                      │
               STOPPING CRITERIA
               speed Δ < 5%
               entropy  < 0.05
```

## Protocol

### Step 1: Parse Invocation
```
/autoresearch "calgary city budget 2026 challenges"
autoresearch "calgary housing affordability"
```

### Step 2: Execute Autoresearch Loop
Run the CLI:
```bash
python3 06_System/bin/kiban-autoresearch.py --seed "<query>" --max-iterations 30
```

Or invoke via module:
```python
from autoresearch.loop import AutoresearchLoop
result = asyncio.run(AutoresearchLoop(seed_query="<query>").run())
```

### Step 3: Interpret Results
Output is JSON:
```json
{
  "completed": true,
  "terminated": false,
  "reason": "max_iterations",
  "iterations_completed": 3,
  "total_urls_processed": 12,
  "final_entropy": 1.0,
  "final_speed_delta": 88.28
}
```

**Termination reasons:**
- `max_iterations` — hard cap reached
- `dual_metric_stall` — both metrics failed 3 consecutive iterations
- `council_emergency` — speed dropped >30% (emergency council trigger)

### Step 4: Query Logs
Iteration data: `06_System/Logs/autoresearch_log.jsonl`
Council verdicts: `06_System/Logs/council_verdicts.jsonl`

## Dual-Metric Stopping Criteria

| Metric | Threshold | Meaning |
|--------|-----------|---------|
| `speed_delta_pct` | < 5% over 3 iter | Latency improvement stagnating |
| `entropy` | < 0.05 | New insights per iteration diminishing |

Both must fail 3 times in a row → terminate.

## Natural Language Prompts

```
research calgary transit budget challenges
investigate calgary housing affordability 2026
deep dive on calgary police budget allocation
find gaps in calgary 2026 budget fiscal pressures
autoresearch calgary infrastructure deficit
```

## Components

| Component | File | Role |
|-----------|------|------|
| DiscoveryAgent | `autoresearch/discovery.py` | Serper API, returns up to 5 URLs |
| ExtractionAgent | `autoresearch/extraction.py` | asyncio.gather parallel fetch + cache |
| GapAnalyzer | `autoresearch/gap_analysis.py` | TF-IDF cosine similarity, gap detection |
| CouncilPanel | `autoresearch/council.py` | Expert voting (Karpathy/Klein/Gigerenzer) |
| TerminationController | `autoresearch/termination.py` | Dual-metric rolling window |
| AutoresearchLoop | `autoresearch/loop.py` | Orchestration |

## Integration (OpenClaw / Hermes)

For agent integration, add to the agent's skill registry:

```json
{
  "name": "autoresearch",
  "command": "/autoresearch",
  "handler": "cli",
  "path": "06_System/bin/kiban-autoresearch.py",
  "args": "--seed",
  "description": "Autonomous research loop with council oversight"
}
```

Or call directly from agent code:
```python
import subprocess
result = subprocess.run(
    ["python3", "06_System/bin/kiban-autoresearch.py", "--seed", query],
    capture_output=True, text=True
)
print(result.stdout)
```

## Requirements

- Python 3.10+
- `SERPER_API_KEY` environment variable
- SSH access to Sovereign (172.16.0.46) for Lightpanda fetches
- `sklearn` (`TfidfVectorizer` for gap analysis)

## Troubleshooting

**No API key:** Set `SERPER_API_KEY` in `.env` or pass `--serper-api-key`
**Lightpanda timeout:** Government sites (`.gov`) may timeout — extraction fails gracefully, loop continues
**All URLs fail:** entropy stays at 1.0, loop terminates via max_iterations cap
