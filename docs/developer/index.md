---
title: Developer
nav_order: 25
has_children: true
---

# Developer Documentation

Welcome to the Sliswap developer documentation. This section contains comprehensive technical documentation for developers, integrators, and protocol contributors.

## Documentation Structure

### Contract Guides
- **[DEX Contract Guides](./dex-contract-guides/)**: Detailed interface documentation for all core modules
  - [Liquidity Pool](./dex-contract-guides/liquidity_pool.md): Core AMM logic and adaptive invariant
  - [Router](./dex-contract-guides/router.md): User-friendly interface for swaps and liquidity management
  - [Connector](./dex-contract-guides/connector.md): EDS-based routing and liquidity enhancement

### Protocol Specifications
- **[Protocol](./protocol/)**: Mathematical models and technical specifications
  - [Liquidity](./protocol/liquidity.md): Adaptive invariant, parameter updates, swap math, examples
  - [Fees](./protocol/fee.md): Dual-sided fee model (0.30%), application order, accounting, safety

### Governance
- **[Governance](./govermance.md)**: Protocol governance mechanisms and management

## Quick Start

1. **For Protocol Integration**: Start with the [Protocol Specifications](./protocol/) to understand the mathematical models
2. **For Contract Development**: Review the [Contract Guides](./dex-contract-guides/) for interface details
3. **For Governance Participation**: Check the [Governance](./govermance.md) documentation

## Key Concepts

- **Adaptive Invariant**: `(s x + y - c) x y = k` - Dynamic AMM model
- **Dual-Sided Fees**: 0.30% total (0.15% input + 0.15% output)
- **Parameter Updates**: Dynamic `s` and `c` parameters based on trading patterns
- **Multi-Hop Routing**: EDS-based connector system for enhanced liquidity

## Support

For technical questions or integration support, please refer to the specific documentation sections or contact the development team.
