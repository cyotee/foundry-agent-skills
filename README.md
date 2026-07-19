# foundry-agent-skills

Foundry **agent runtime** skills: `cast` RPC interaction, Anvil fork rehearsal, multi-step `forge script` ops, and safety gates.

This package is for **operating on chains**, not for writing/fuzzing contracts.

| Concern | Package |
|---------|---------|
| Cast, Anvil, agent safety, script ops | **This repo** (`foundry-agent`) |
| forge test, fuzz, project setup, deploy patterns | [`foundry-skills`](https://github.com/cyotee/foundry-skills) |

## Skills

- `cast-commands` — read/write RPC via Cast
- `anvil-node` — local/fork nodes
- `foundry-agent-safety` — confirmation and secret policy
- `foundry-script-ops` — multi-step `forge script` broadcasts

## Install

Listed as plugin **`foundry-agent`** in:

- [defi-agent-skills](https://github.com/cyotee/defi-agent-skills) (agent marketplace)
- [cyotee-claude-plugins](https://github.com/cyotee/cyotee-claude-plugins) (developer marketplace also needs cast)

```bash
/plugin install foundry-agent@defi-agent-skills
# or
/plugin install foundry-agent@cyotee
```

## License

MIT
