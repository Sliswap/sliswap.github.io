---
title: Router
parent: Contract guides
nav_order: 1
---

## Overview
The Router module provides pool discovery, multi-hop quoting, swap execution (exact-in and exact-out), and LP helpers. Each interface below includes Description, Parameters, and Returns.

## Views (read-only)

### calc_pool_addr_and_is_exist
```
public fun calc_pool_addr_and_is_exist(
    token_0: Object<Metadata>, token_1: Object<Metadata>
): (address, bool)
```
Description: Computes the deterministic pool address and whether the pool exists.

Parameters:
- token_0: first token metadata
- token_1: second token metadata

Returns:
- (address, bool): `(pool_address, is_exist)`

### get_amounts_out
```
public fun get_amounts_out(
    pools: vector<Object<LiquidityPool>>, from_token: Object<Metadata>, amount_in: u128
): (u128, u128)
```
Description: Quotes final output and aggregated price impact for a multi-hop exact-in swap along `pools`.

| Parameter | Type | Description |
|-----------|------|-------------|
| pools | `vector<Object<LiquidityPool>>` | Route pools in hop order |
| from_token | `Object<Metadata>` | Input token of first hop |
| amount_in | `u128` | Exact input amount |

| Returns | Type | Description |
|---------|------|-------------|
| final_amount_out | `u128` | Final amount after all hops |
| price_impact_bps | `u128` | Aggregated price impact (bps) |

### get_amounts_in
```
public fun get_amounts_in(
    pools: vector<Object<LiquidityPool>>, to_token: Object<Metadata>, amount_out: u128
): (u128, u128)
```
Description: Quotes required total input and aggregated price impact for a multi-hop exact-out swap.

| Parameter | Type | Description |
|-----------|------|-------------|
| pools | `vector<Object<LiquidityPool>>` | Route pools in hop order |
| to_token | `Object<Metadata>` | Final output token |
| amount_out | `u128` | Exact desired final output |

| Returns | Type | Description |
|---------|------|-------------|
| required_amount_in | `u128` | Total input required across all hops |
| price_impact_bps | `u128` | Aggregated price impact (bps) |

### quote_liquidity
```
public fun quote_liquidity(
    token_0: Object<Metadata>, token_1: Object<Metadata>, amount_0: u128
): u128
```
Description: Quotes the proportional amount of token1 needed for providing `amount_0` of token0, based on pool price.

Parameters:
- token_0: base token metadata
- token_1: quote token metadata
- amount_0: token0 amount

Returns:
- u128: required token1 amount

### lp_token_balance
```
public fun lp_token_balance(pool: address, provider: address): u128
```
Description: Returns the LP token balance for a provider address in a pool.

Parameters:
- pool: pool address (also LP token id)
- provider: LP holder address

Returns:
- u128: LP token balance

### liquidity_amount_out
```
public fun liquidity_amount_out(
    token_0: Object<Metadata>, token_1: Object<Metadata>, amount_0: u128, amount_1: u128
): u128
```
Description: Quotes LP tokens minted for the given token amounts.

Parameters:
- token_0: token0 metadata
- token_1: token1 metadata
- amount_0: desired token0 amount
- amount_1: desired token1 amount

Returns:
- u128: LP tokens minted

### redeemable_liquidity
```
public fun redeemable_liquidity(
    pool: Object<LiquidityPool>, amount: u128
): (u128, u128)
```
Description: Quotes underlying token amounts redeemable for LP `amount`.

Parameters:
- pool: pool object
- amount: LP tokens to redeem

Returns:
- (u128, u128): `(amount_0, amount_1)`

### locked_lp_token_amount
```
public fun locked_lp_token_amount(lp_token: Object<LiquidityPool>): u128
```
Description: Returns the amount of LP tokens locked in the black-hole address.

Parameters:
- lp_token: LP token object (pool object)

Returns:
- u128: locked LP amount

## Entries and public functions (state-changing)

### create_pool
```
public entry fun create_pool(
    creator: &signer,
    token_0: Object<Metadata>, token_1: Object<Metadata>,
    amount_0: u128, amount_1: u128
)
```
Description: Creates a new pool (requiring connector-coin checks) and immediately adds initial liquidity using `amount_0/amount_1`.

Parameters:
- creator: signer of the creator
- token_0/token_1: tokens to pair (at least one must be a configured connector coin with sufficient amount)
- amount_0/amount_1: initial deposits

### swap_router_entry
```
public entry fun swap_router_entry(
    user: &signer,
    pools: vector<Object<LiquidityPool>>,
    from_token: Object<Metadata>, amount_in: u128,
    amount_out_min: u128, recipient: address
)
```
Description: Executes a multi-hop exact-in swap on-chain and deposits the final output to `recipient`.

Parameters:
- user: swap initiator signer
- pools: route pools in order
- from_token: input token metadata
- amount_in: exact input amount
- amount_out_min: slippage protection (minimum acceptable output)
- recipient: final receiver address

### swap_router
```
public fun swap_router(
    pools: vector<Object<LiquidityPool>>, in: FungibleAsset, amount_out_min: u128
): FungibleAsset
```
Description: Internal helper to route a multi-hop exact-in swap with a fungible asset, enforcing `amount_out_min`.

Parameters:
- pools: route pools in order
- in: input fungible asset (already withdrawn)
- amount_out_min: minimum acceptable output

Returns:
- FungibleAsset: final output asset

### swap_entry
```
public entry fun swap_entry(
    user: &signer,
    pool: Object<LiquidityPool>,
    from_token: Object<Metadata>, amount_in: u128,
    amount_out_min: u128, recipient: address
)
```
Description: Executes a single-hop exact-in swap and deposits output to `recipient`.

| Parameter | Type | Description |
|-----------|------|-------------|
| user | `&signer` | Swap initiator signer |
| pool | `Object<LiquidityPool>` | Target pool |
| from_token | `Object<Metadata>` | Input token metadata |
| amount_in | `u128` | Exact input amount |
| amount_out_min | `u128` | Minimum acceptable output |
| recipient | `address` | Final receiver |

| Returns | Type | Description |
|---------|------|-------------|
| (none) | - | Side effects: withdraw/deposit, emits events |

### swap
```
public fun swap(
    pool: Object<LiquidityPool>, in: FungibleAsset, amount_out_min: u128
): FungibleAsset
```
Description: Internal helper for single-hop exact-in swap enforcing `amount_out_min`.

Parameters:
- pool: target pool
- in: input fungible asset
- amount_out_min: minimum acceptable output

Returns:
- FungibleAsset: output asset

### swap_exact_out_router_entry
```
public entry fun swap_exact_out_router_entry(
    user: &signer,
    pools: vector<Object<LiquidityPool>>,
    to_token: Object<Metadata>, amount_out: u128,
    amount_in_max: u128, recipient: address
)
```
Description: Executes a multi-hop exact-out swap on-chain, ensuring total input does not exceed `amount_in_max`.

Parameters:
- user: swap initiator signer
- pools: route pools in order
- to_token: final output token
- amount_out: exact desired final output
- amount_in_max: slippage protection (max input tolerated)
- recipient: final receiver address

### swap_exact_out_entry
```
public entry fun swap_exact_out_entry(
    user: &signer,
    pool: Object<LiquidityPool>,
    to_token: Object<Metadata>, amount_out: u128,
    amount_in_max: u128, recipient: address
)
```
Description: Executes a single-hop exact-out swap, ensuring input does not exceed `amount_in_max`.

| Parameter | Type | Description |
|-----------|------|-------------|
| user | `&signer` | Swap initiator signer |
| pool | `Object<LiquidityPool>` | Target pool |
| to_token | `Object<Metadata>` | Desired output token |
| amount_out | `u128` | Exact desired output |
| amount_in_max | `u128` | Maximum input permitted |
| recipient | `address` | Final receiver |

| Returns | Type | Description |
|---------|------|-------------|
| (none) | - | Side effects: withdraw/deposit, emits events |

### swap_exact_out
```
public fun swap_exact_out(
    in: FungibleAsset, amount_out: u128, to_token: Object<Metadata>
): (FungibleAsset, FungibleAsset)
```
Description: Internal helper for single-hop exact-out swap. Returns `(out, in_remainder)` where the remainder must be destroyed or returned.

Parameters:
- in: input fungible asset (upper bound)
- amount_out: exact desired output
- to_token: output token metadata

Returns:
- (FungibleAsset, FungibleAsset): `(out, in_remainder)`

### add_liquidity_entry
```
public entry fun add_liquidity_entry(
    lp: &signer,
    token_0: Object<Metadata>, token_1: Object<Metadata>,
    amount_0: u128, amount_1: u128,
    amount_0_min: u128, amount_1_min: u128
)
```
Description: Adds liquidity after computing optimal amounts. Mints LP tokens to the provider.

Parameters:
- lp: LP provider signer
- token_0/token_1: pool tokens
- amount_0/amount_1: desired amounts
- amount_0_min/amount_1_min: slippage guards for deposits

### remove_liquidity_entry
```
public entry fun remove_liquidity_entry(
    lp: &signer,
    token_0: Object<Metadata>, token_1: Object<Metadata>,
    liquidity: u128, amount_0_min: u128, amount_1_min: u128,
    recipient: address
)
```
Description: Burns LP and withdraws underlying tokens to `recipient`, enforcing minimum amounts.

Parameters:
- lp: LP provider signer
- token_0/token_1: pool tokens
- liquidity: LP amount to burn
- amount_0_min/amount_1_min: slippage guards for withdrawals
- recipient: receiver address

### lock_lp_token_in_black_hole
```
public entry fun lock_lp_token_in_black_hole(
    from: &signer, lp_token: Object<LiquidityPool>, amount: u128
)
```
Description: Sends LP tokens to a designated black-hole address for permanent lock.

Parameters:
- from: LP holder signer
- lp_token: LP token object
- amount: LP amount to lock

Notes:
- Slippage protection: use `amount_out_min` on exact-in; `amount_in_max` on exact-out.
- Multi-hop swaps: pass `pools` in route order.
- `create_pool`: requires a configured connector coin on at least one side with sufficient minimum amount.
