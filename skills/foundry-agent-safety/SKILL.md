---
name: foundry-agent-safety
description: >
  Safety policy for AI agents using Foundry cast/forge on live chains.
  Use before any cast send, forge script --broadcast, or value transfer.
  Covers chain confirmation, secrets, dry-run, and confirmation gates.
---

# Foundry agent safety

## Goal
Agent-usable Foundry skill: see When to Use / overview above for skills/foundry-agent-safety/SKILL.md.

## Inspect
Read-only first: confirm chain id and addresses before writes.


## When to use

- Before the first write on a session
- Before any mainnet or funded-wallet transaction
- When a prior cast/forge command reverted

## Mandatory checklist

1. **Chain** — `cast chain-id` matches the intended network (document expected id).
2. **RPC** — `ETH_RPC_URL` or `--rpc-url` points at the correct endpoint (not a surprise public mainnet when testing).
3. **Identity** — `cast wallet address --private-key $PRIVATE_KEY` (or keystore) is the intended agent wallet. Never echo the key.
4. **Balances** — native + token balances cover amount + gas.
5. **Addresses** — every contract address comes from a skill address table, deployment artifact, or on-chain registry query — never invented.
6. **Dry-run** — prefer `cast call` / `cast estimate` before `cast send`.
7. **Human confirmation** — for any state change, print a summary (chain, to, function, amounts) and wait for explicit user OK.
8. **Size** — first live txs should be small; scale after success.
9. **Receipt** — after send, `cast receipt $TX` and re-read state; do not assume success from a hash alone.
10. **No blind gas bumps** — on revert, decode error (`cast 4byte`, receipt logs) before retrying.

## Fork rehearsal

Prefer Anvil fork before mainnet writes:

```bash
anvil --fork-url "$ETH_RPC_URL" --port 8545
export ETH_RPC_URL=http://127.0.0.1:8545
# use Anvil default keys only on the fork
```

See skill `anvil-node` for forking details.

## Secrets

- Use env vars: `PRIVATE_KEY`, `ETH_RPC_URL` — never paste keys into skill files or commits.
- Prefer `cast wallet` keystore / `--account` when available.
- Do not log full `.env` files.

## Confirmation summary template

```text
CHAIN_ID: <id> (<name>)
FROM: <address>
TO: <contract>
FN: <signature>
ARGS: <human readable>
VALUE: <eth or 0>
TOKEN_AMOUNTS: <...>
EST_GAS: <from cast estimate if available>
USER_OK?: waiting
```

Only broadcast after the user confirms.
