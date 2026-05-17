<div align="center">

# Agentix

**Typed rollouts for agentic RL.**

Sandboxed execution · Structured traces · RL post-training · Evaluation

[Docs](https://agentiix.github.io/) · [Quickstart](https://github.com/Agentiix/Agentix#quickstart) · [Cookbook](https://github.com/Agentiix/agentix-cookbook) · [RL Bridge](https://github.com/Agentiix/abridge)

</div>

---

Agentix is an open-source stack for running coding agents inside isolated
rollout containers, capturing every LLM call and tool invocation as a
structured trace, and routing those traces to RL post-training,
evaluation harnesses, and custom serving providers.

One typed Python surface for trainers, evaluators, and agent builders.

## Repositories

### Core

| Repo | What it does |
|---|---|
| **[Agentix](https://github.com/Agentiix/Agentix)** | The framework — typed Python namespaces for sandbox-based agent workflows. Execution backends: `local` (Docker), `daytona`, `e2b`. |
| **[abridge](https://github.com/Agentiix/abridge)** | Host-side bridge from agent rollouts to RL training. Taps the runtime's trace stream, correlates events by rollout, hands `Rollout`s to your trainer's sink. |

### Integrations

| Repo | What it does |
|---|---|
| **[agentix-cookbook](https://github.com/Agentiix/agentix-cookbook)** | Worked integrations: Claude Code as a remote-callable function, SWE-bench Verified scoring. |
| **[Agentix-Agents-Hub](https://github.com/Agentiix/Agentix-Agents-Hub)** | Hub of agent closures. |
| **[Agentix-Datasets](https://github.com/Agentiix/Agentix-Datasets)** | Dataset + verifier closures. |

### Docs

| Repo | What it does |
|---|---|
| **[Agentiix.github.io](https://github.com/Agentiix/Agentiix.github.io)** | Documentation site — [agentiix.github.io](https://agentiix.github.io/). |

## A SWE-bench rollout, end to end

```python
from datasets import load_dataset
from agentix import RuntimeClient, bash, claude_code, swebench

inst = dict(load_dataset("princeton-nlp/SWE-bench_Verified", split="test")[0])

async with RuntimeClient(sandbox.runtime_url) as c:
    await c.remote(bash.run, command=f"git clone https://github.com/{inst['repo']}.git /testbed")
    cc    = await c.remote(claude_code.run, instruction=inst["problem_statement"], workdir="/testbed")
    diff  = await c.remote(bash.run, command="cd /testbed && git add -A && git diff --cached")
    score = await c.remote(swebench.score, instance=inst, patch=diff.stdout)
```

Three integrations, one composition. Trade Claude Code for any other
CLI agent, SWE-bench for any other scorer, `local` for any other
execution backend — the call sites stay the same.

## License

MIT, across all repos.
