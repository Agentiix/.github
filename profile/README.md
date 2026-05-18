<div align="center">

# Agentix

**Sandboxed rollouts you call like typed Python.**

Ship agents like software. Run them like experiments.

[Docs](https://agentiix.github.io/) · [Quickstart](https://agentiix.github.io/quickstart) · [Cookbook](https://github.com/Agentiix/agentix-cookbook) · [Architecture](https://agentiix.github.io/reference/architecture)

</div>

---

Agentix is a small execution layer for agent experiments. Package an
agent, tool, scorer, or internal workflow into a sandbox image, then call
its Python functions from host-side trainers, evaluators, and scripts.

```python
from agentix import RuntimeClient
from app import run

async with RuntimeClient(sandbox.runtime_url) as client:
    result = await client.remote(run, input="hello")
```

The unit of composition is a function, not a custom runner.

## Core Model

- **Remote calls** run installed Python functions inside a sandbox
  worker. Agentix derives the target from the function object, validates
  arguments from the signature, and returns typed results.
- **Bundles** package one Python project and its declared dependencies
  into a deploy-ready runtime image.
- **Deployments** start bundle images locally or through backend plugins
  and return a `runtime_url` for `RuntimeClient`.

## What This Unlocks

| You have | You expose | You call |
| --- | --- | --- |
| Claude Code, Codex, Aider, OpenHands, or an internal agent | `async def run(...) -> RunResult` | `await client.remote(run, ...)` |
| Shell, files, repo setup, or local tools | `async def run(command: str) -> BashResult` | `await client.remote(bash_run, ...)` |
| SWE-bench, MLE-Bench, or an internal evaluator | `async def score(...) -> Score` | `await client.remote(score, ...)` |
| Streaming or interactive workflows | `async def stream(...) -> AsyncIterator[Event]` | `async for event in client.remote(stream, ...)` |

## Repositories

| Repo | What it does |
| --- | --- |
| [Agentix](https://github.com/Agentiix/Agentix) | Core runtime client, server, worker protocol, build CLI, and deployment plugin interface |
| [Agentix-Runtime-Basic](https://github.com/Agentiix/Agentix-Runtime-Basic) | Sandbox primitives such as shell and file operations |
| [Agentix-Deployment-Docker](https://github.com/Agentiix/Agentix-Deployment-Docker) | Local Docker deployment backend |
| [Agentix-Deployment-Daytona](https://github.com/Agentiix/Agentix-Deployment-Daytona) | Hosted sandbox backend package |
| [Agentix-Deployment-E2B](https://github.com/Agentiix/Agentix-Deployment-E2B) | Hosted sandbox backend package |
| [agentix-cookbook](https://github.com/Agentiix/agentix-cookbook) | Working recipes for agent wrappers and benchmark scorers |
| [abridge](https://github.com/Agentiix/abridge) | Host-side rollout-to-RL-buffer bridge |

## Start Here

- [Read the docs](https://agentiix.github.io/)
- [Build and call a tiny bundle](https://agentiix.github.io/quickstart)
- [Understand remote calls](https://agentiix.github.io/concepts/remote-calls)
- [Understand bundles](https://agentiix.github.io/concepts/bundles)
