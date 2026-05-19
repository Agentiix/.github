<div align="center">

# Agentix

**The bridge between agents, evaluation, RL training, and LLM serving.**

Sandboxed execution - typed dispatch - runtime bundles - structured traces

[Docs](https://agentiix.github.io/) -
[Quickstart](https://agentiix.github.io/quickstart) -
[Cookbook](https://github.com/Agentiix/agentix-cookbook) -
[Architecture](https://agentiix.github.io/reference/architecture)

</div>

---

Agentix is a small execution layer for agent experiments. Package an
agent, tool, scorer, or internal workflow into a runtime image, overlay
it onto a sandbox task image, then call its Python functions from
host-side trainers, evaluators, and scripts.

The unit of composition is a function, not a custom runner.

## Core Model

- **Remote calls** run installed Python functions inside a sandbox
  worker. `RuntimeClient.remote(fn, ...)` derives the target from the
  function object, validates arguments from the signature, and returns
  typed results.
- **Bundles** package one Python project and its declared dependencies
  into a deploy-ready runtime image with `agentix build`.
- **Deployments** start task images locally or through backend plugins,
  overlay the runtime image, and return a `runtime_url` for
  `RuntimeClient`.

## Smallest Runnable Demo

Start with the complete demo in
[`agentix-cookbook/examples/hello-agentix`](https://github.com/Agentiix/agentix-cookbook/tree/main/examples/hello-agentix):

```bash
cd examples/hello-agentix
uv sync
uv run agentix build . --name hello-agentix  # builds hello-agentix:0.1.0
uv run python run.py
```

The runner overlays `hello-agentix:0.1.0` onto `python:3.13-slim`,
starts a sandbox, then calls `agentix.bash.run` from host-side Python:

```python
import asyncio

from agentix import RuntimeClient, SandboxConfig, session
from agentix.bash import run
from agentix.deployment.docker import DockerDeployment


async def main() -> None:
    config = SandboxConfig(
        image="python:3.13-slim",
        runtime_image="hello-agentix:0.1.0",
    )
    async with session(DockerDeployment(), config) as sandbox:
        print(f"sandbox up at {sandbox.runtime_url}")
        async with RuntimeClient(sandbox.runtime_url) as client:
            result = await client.remote(run, command="echo hello from $(uname -a)")
            print(f"exit={result.exit_code} stdout={result.stdout!r}")


asyncio.run(main())
```

The same pattern scales to the larger
[`eval-cc-swe`](https://github.com/Agentiix/agentix-cookbook/tree/main/examples/eval-cc-swe)
demo: clone a SWE-bench repo with `bash.run`, run Claude Code through
an agent namespace, extract the patch, and score it with a SWE-bench
namespace.

## What This Unlocks

| You have | You expose | You call |
| --- | --- | --- |
| Claude Code, Codex, Aider, OpenHands, or an internal agent | `async def run(...) -> RunResult` | `await client.remote(run, ...)` |
| Shell, files, repo setup, or local tools | `async def run(command: str) -> BashResult` | `await client.remote(bash_run, ...)` |
| SWE-bench, MLE-Bench, or an internal evaluator | `async def score(...) -> Score` | `await client.remote(score, ...)` |
| Streaming or interactive workflows | `async def stream(...) -> AsyncIterator[Event]` | `async for event in client.remote(stream, ...)` |

## What Agentix Bridges

| From | Agentix layer | To |
| --- | --- | --- |
| Agent CLIs and Python frameworks | Isolated sandbox workers with typed remote dispatch | Evaluators, trainers, and orchestration code |
| Shell, file, and tool operations | Shared rollout container surface | Agents and benchmark harnesses |
| LLM calls and tool events | Structured trace capture and fan-out | Observability, reward, replay, and dataset pipelines |
| Rollout traces | `abridge` correlation and sink protocol | RL buffers such as slime / verl, or custom serving stacks |
| Local and hosted sandboxes | Deployment backend packages | Docker today; hosted backends through plugins |

## Repository Map

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

## License

MIT across the Agentix repositories.
