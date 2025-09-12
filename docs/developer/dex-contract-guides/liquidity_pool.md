---
title: Liquidity Pool
parent: DEX Contract Guides
nav_order: 2
---

# Liquidity Pool

The Liquidity Pool contract is a core component of Sliswap that manages token pairs, liquidity provision, and swap operations. It implements an adaptive invariant `(s x + y - c) x y = k` with dynamic parameters `s` and `c`.

## Overview

The Liquidity Pool contract provides the following key functionalities:
- Pool creation and management
- Liquidity provision and removal
- Token swaps with dynamic fee calculation
- Virtual liquidity management
- LP token minting and burning

## Data Structures

### PoolInfo
```
struct PoolInfo {
    pool_addr: address,
    token_0: Object<Metadata>,
    token_1: Object<Metadata>,
    reserve_0: u128,
    reserve_1: u128,
    fee_0: u128,
    fee_1: u128,
    s_numerator: u128,
    s_denominator: u128,
    c: u128,
    swap_fee_bps: u128,
    dao_fee_bps: u128,
    lp_token_supply: u128,
    lp_token_decimals: u8,
}
```

## Read-Only Functions

### Addresses and metadata

#### liquidity_pool
```
#[view]
public fun liquidity_pool(
    token_0: Object<Metadata>, token_1: Object<Metadata>
): Object<LiquidityPool>
```
Description: Returns the pool object that manages swaps and LP tokens for the given token pair.

| Parameter | Type | Description |
|-----------|------|-------------|
| token_0 | `Object<Metadata>` | First token metadata |
| token_1 | `Object<Metadata>` | Second token metadata |

| Returns | Type | Description |
|---------|------|-------------|
| pool | `Object<LiquidityPool>` | Pool object (also the LP token id) |

```
#[view]
public fun liquidity_pool_address(
    token_0: Object<Metadata>, token_1: Object<Metadata>
): address
```
Description: Computes the deterministic address for the pool of the given pair.

| Parameter | Type | Description |
|-----------|------|-------------|
| token_0 | `Object<Metadata>` | First token metadata |
| token_1 | `Object<Metadata>` | Second token metadata |

| Returns | Type | Description |
|---------|------|-------------|
| address | `address` | Deterministic pool address (order-insensitive) |

```
#[view]
public fun is_sorted(token_0: Object<Metadata>, token_1: Object<Metadata>): bool
```
Description: Checks if the tokens are in the canonical pool order.

| Parameter | Type | Description |
|-----------|------|-------------|
| token_0 | `Object<Metadata>` | Token metadata to check |
| token_1 | `Object<Metadata>` | Token metadata to check |

| Returns | Type | Description |
|---------|------|-------------|
| is_sorted | `bool` | True if `(token_0, token_1)` is canonical order |

```
#[view]
public fun supported_inner_assets(pool: Object<LiquidityPool>): vector<Object<Metadata>>
```
Description: Returns the two token metadata objects supported by the pool.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<LiquidityPool>` | Pool object |

| Returns | Type | Description |
|---------|------|-------------|
| tokens | `vector<Object<Metadata>>` | `[token_0, token_1]` |

### Pool info and reserves

#### pool_info_by_tokens
```
#[view]
public fun pool_info_by_tokens(
    token_0: Object<Metadata>, token_1: Object<Metadata>
): PoolInfo
```
Description: Convenience view to fetch `PoolInfo` by passing the token pair.

| Parameter | Type | Description |
|-----------|------|-------------|
| token_0 | `Object<Metadata>` | First token metadata |
| token_1 | `Object<Metadata>` | Second token metadata |

| Returns | Type | Description |
|---------|------|-------------|
| info | `PoolInfo` | Snapshot (reserves, params, fees, LP info) |

```
#[view]
public fun pool_info(pool: Object<LiquidityPool>): PoolInfo
```
Description: Returns a detailed snapshot of the pool state.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<LiquidityPool>` | Pool object |

| Returns | Type | Description |
|---------|------|-------------|
| info | `PoolInfo` | Reserves, fee balances, `s`, `c`, fee bps, LP info |

```
#[view]
public fun pools_info(pools: vector<Object<LiquidityPool>>): vector<PoolInfo>
```
Description: Batch variant of `pool_info` for multiple pools.

| Parameter | Type | Description |
|-----------|------|-------------|
| pools | `vector<Object<LiquidityPool>>` | List of pool objects |

| Returns | Type | Description |
|---------|------|-------------|
| infos | `vector<PoolInfo>` | One `PoolInfo` per pool, same order |

```
#[view]
public fun pool_reserves<T: key>(pool: Object<T>): (u128, u128)
```
Description: Fetches the current on-chain token reserves.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<T>` | Pool or LP token object |

| Returns | Type | Description |
|---------|------|-------------|
| reserve_0 | `u128` | Token0 reserve |
| reserve_1 | `u128` | Token1 reserve |

```
#[view]
public fun pool_params<T: key>(pool: Object<T>): (u128, u128, u128)
```
Description: Fetches invariant parameters.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<T>` | Pool or LP token object |

| Returns | Type | Description |
|---------|------|-------------|
| s_numerator | `u128` | Numerator of `s` |
| s_denominator | `u128` | Denominator of `s` |
| c | `u128` | Offset parameter |

```
#[view]
public fun lp_token_supply<T: key>(pool: Object<T>): u128
```
Description: Returns the total supply of LP tokens for the pool.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<T>` | Pool or LP token object |

| Returns | Type | Description |
|---------|------|-------------|
| supply | `u128` | Total LP supply |

```
#[view]
public fun min_liquidity(): u128
```
Description: Returns the minimum LP amount permanently locked on initial mint.

| Returns | Type | Description |
|---------|------|-------------|
| min_liquidity | `u128` | Constant minimum LP liquidity |

```
#[view]
public fun all_pool_addresses(): vector<Object<LiquidityPool>>
```
Description: Lists all existing pools.

| Returns | Type | Description |
|---------|------|-------------|
| pools | `vector<Object<LiquidityPool>>` | All pool objects |

```
#[view]
public fun pool_metadata(
    pool: Object<LiquidityPool>
): (Object<Metadata>, Object<Metadata>, u128, u128, u8, u8)
```
Description: Returns tokens and reserves plus decimals.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<LiquidityPool>` | Pool object |

| Returns | Type | Description |
|---------|------|-------------|
| token_0 | `Object<Metadata>` | Token0 metadata |
| token_1 | `Object<Metadata>` | Token1 metadata |
| reserve_0 | `u128` | Token0 reserve |
| reserve_1 | `u128` | Token1 reserve |
| decimals_0 | `u8` | Token0 decimals |
| decimals_1 | `u8` | Token1 decimals |

### Swap calculations

#### get_amount_out
```
#[view]
public fun get_amount_out(
    pool: Object<LiquidityPool>, token_in: Object<Metadata>, amount_in: u128
): (u128, u128, u128, u128, u128, u128)
```
Description: Calculates output amount and per-side fee breakdown for exact input.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<LiquidityPool>` | Target pool |
| token_in | `Object<Metadata>` | Input token metadata |
| amount_in | `u128` | Exact input amount |

| Returns (tuple) | Type | Description |
|-----------------|------|-------------|
| amount_out | `u128` | Final output after output-side fees |
| token0_swap_fee | `u128` | Swap fee charged on token0 leg |
| token0_dao_fee | `u128` | DAO fee charged on token0 leg |
| token1_swap_fee | `u128` | Swap fee charged on token1 leg |
| token1_dao_fee | `u128` | DAO fee charged on token1 leg |
| price_impact_bps | `u128` | Price impact in basis points for this hop |

```
#[view]
public fun get_amount_in(
    pool: Object<LiquidityPool>, token_in: Object<Metadata>, amount_out: u128
): (u128, u128, u128, u128, u128, u128)
```
Description: Calculates required input amount and per-side fee breakdown for exact output.

| Parameter | Type | Description |
|-----------|------|-------------|
| pool | `Object<LiquidityPool>` | Target pool |
| token_in | `Object<Metadata>` | Input token metadata (the token you will spend) |
| amount_out | `u128` | Exact desired output amount |

| Returns (tuple) | Type | Description |
|-----------------|------|-------------|
| amount_in | `u128` | Required input including input-side fees |
| token0_swap_fee | `u128` | Swap fee charged on token0 leg |
| token0_dao_fee | `u128` | DAO fee charged on token0 leg |
| token1_swap_fee | `u128` | Swap fee charged on token1 leg |
| token1_dao_fee | `u128` | DAO fee charged on token1 leg |
| price_impact_bps | `u128` | Price impact in basis points for this hop |

Return tuple order for both functions:
- `(amount, token0_swap_fee, token0_dao_fee, token1_swap_fee, token1_dao_fee, price_impact_bps)`

### Liquidity providers

#### transfer
```
public entry fun transfer(
    from: &signer, lp_token: Object<LiquidityPool>, to: address, amount: u128
)
```
Description: Transfers LP tokens; must be used instead of generic transfers to preserve accounting and events.

| Parameter | Type | Description |
|-----------|------|-------------|
| from | `&signer` | LP token holder signer |
| lp_token | `Object<LiquidityPool>` | LP token object (the pool object) |
| to | `address` | Recipient address |
| amount | `u128` | LP tokens to transfer |

## Events

```
#[event]
struct CreatePoolEvent { pool: Object<LiquidityPool>, token_0: address, token_1: address }

#[event]
struct SwapEvent {
    pool: address,
    token_in: address,
    token_out: address,
    amount_in: u128,
    amount_out: u128,
    token0_swap_fee_amount: u128,
    token1_swap_fee_amount: u128,
}

#[event]
struct ReservesUpdated { pool: address, reserve_0: u128, reserve_1: u128 }

#[event]
struct MintLP { lp: address, pool: address, amount_lp: u128, amount_0: u128, amount_1: u128 }

#[event]
struct BurnLP { lp: address, pool: address, amount_lp: u128, amount_0: u128, amount_1: u128 }

#[event]
struct TransferEvent { pool: address, amount: u128, from: address, to: address }

#[event]
struct ClaimFeesEvent { pool: address, amount_0: u128, amount_1: u128 }
```

## Fee model (summary)
- Default: `swap_fee_bps = 12`, `dao_fee_bps = 3` (total 0.15% on in, 0.15% on out)
- Fees are taken on both input and output legs as defined in the protocol docs