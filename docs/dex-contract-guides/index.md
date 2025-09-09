---
title: Contract guides
nav_order: 3
---

# Contract guides
Guides to use endless dex contract
{: .fs-6 .fw-300 }

## Modules
- [Router](./router.md): Swap execution (exact-in/out), multi-hop routing, liquidity add/remove helpers.
- [Liquidity pool](./liquidity_pool.md): Core AMM state, pool parameters, events.
- [Connector](./connector.md): Connector tokens list for routing and pool creation.

## Tips
- Prefer Router for user-facing swaps and liquidity operations.
- Query pool state via `pool_info`/`pools_info` for snapshots.
- Use `get_connectors` to validate supported connector tokens.  