---
name: foundry-agent-safety
description: >
  Safety policy for AI agents using Foundry cast/forge on live chains.
  Use before any cast send, forge script --broadcast, or value transfer.
  Covers chain confirmation, secrets, dry-run, and confirmation gates.
---

# Foundry agent safety

## Goal

Prevent accidental mainnet writes, wrong-chain sends, and secret leakage when agents use `cast` / `forge script --broadcast`.

## When to use

- Before the first write in a session
- Before any mainnet or funded-wallet transaction
- When a prior cast/forge command reverted

## Mandatory checklist

1. **Chain** — confirm chain id matches intent:

```bash
cast chain-id --rpc-url $ETH_RPC_URL
# Base Sepolia = 84532; Base = 8453; Ethereum = 1
```

2. **RPC** — `ETH_RPC_URL` or `--rpc-url` is the intended endpoint (not a surprise public mainnet when testing).
3. **Identity** — resolve the agent wallet without echoing secrets:

```bash
cast wallet address --private-key $PRIVATE_KEY
# or: cast wallet address --keystore $KEYSTORE --password $KEYSTORE_PASSWORD
```

4. **Balances** — native + token balances cover amount + gas:

```bash
cast balance $USER --rpc-url $ETH_RPC_URL
cast call $TOKEN "balanceOf(address)(uint256)" $USER --rpc-url $ETH_RPC_URL
```

5. **Addresses** — every contract address comes from a skill address table, deployment artifact, or on-chain registry — never invented.
6. **Dry-run** — prefer `cast call` / `cast estimate` before `cast send`:

```bash
cast call $TARGET "$SIG" $ARGS --rpc-url $ETH_RPC_URL --from $USER
cast estimate $TARGET "$SIG" $ARGS --rpc-url $ETH_RPC_URL --from $USER
```

7. **Human confirmation** — for any state change, print summary (chain, to, function, amounts) and wait for explicit user OK.
8. **Size** — first live txs should be small; scale after success.
9. **Receipt** — after send, verify:

```bash
cast receipt $TX --rpc-url $ETH_RPC_URL
cast tx $TX --rpc-url $ETH_RPC_URL
```

10. **No blind gas bumps** — on revert, decode before retry:

```bash
cast 4byte 0x08c379a0   # example Error(string) selector
cast receipt $TX --rpc-url $ETH_RPC_URL -j
```

## Fork rehearsal

Prefer Anvil fork before mainnet writes:

```bash
anvil --fork-url $ETH_RPC_URL --chain-id 31337
# point ETH_RPC_URL at http://127.0.0.1:8545 for dry execution with anvil keys
```

See skill `anvil-node` for forking details.

## Secrets

- Never print `$PRIVATE_KEY`, keystore passwords, or API keys in chat logs.
- Prefer keystore / hardware / Bankr over raw env keys when available.
- If using Bankr instead of cast, still apply this checklist then use `bankr-ops`.

## Relation to other skills

- Execution: `cast-commands`, `foundry-script-ops`
- Optional wallet backend: `bankr-ops` (same confirmation gates)
