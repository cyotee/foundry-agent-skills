---
name: foundry-script-ops
description: >
  Multi-step on-chain agent operations with forge script (approve then deposit,
  multi-call sequences). Use when sequential cast send is error-prone.
  Prefer cast for single reads/writes.
---

# Foundry script ops (agent multi-step)

## When to use

- Approve + spend in one broadcast session
- Multi-contract sequences that must share the same signer/nonce flow
- Reproducible agent ops checked into a small Foundry project

Prefer **`cast call` / `cast send`** for single-step ops (see `cast-commands`).

## Minimal script pattern

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import {Script} from "forge-std/Script.sol";
import {IERC20} from "forge-std/interfaces/IERC20.sol";

contract AgentOp is Script {
    function run() external {
        uint256 pk = vm.envUint("PRIVATE_KEY");
        address token = vm.envAddress("TOKEN");
        address spender = vm.envAddress("SPENDER");
        uint256 amount = vm.envUint("AMOUNT");

        vm.startBroadcast(pk);
        IERC20(token).approve(spender, amount);
        // next calls...
        vm.stopBroadcast();
    }
}
```

## Commands

```bash
# dry run (no broadcast)
forge script script/AgentOp.s.sol:AgentOp --rpc-url "$ETH_RPC_URL" -vvvv

# broadcast after user confirmation
forge script script/AgentOp.s.sol:AgentOp \
  --rpc-url "$ETH_RPC_URL" \
  --broadcast \
  -vvvv
```

## Safety

1. Run **without** `--broadcast` first; review traces.
2. Apply `foundry-agent-safety` checklist before broadcasting.
3. Pass addresses via env, not hardcoded mainnet keys or secrets in source.
4. Verify with `cast call` / balances after broadcast.

## Relation to cast

| Step | Tool |
|------|------|
| Single view | `cast call` |
| Single tx | `cast send` |
| Multi-step sequence | `forge script --broadcast` |
