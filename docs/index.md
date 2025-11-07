---
title: Overview
layout: home
nav_order: 1
permalink: /
---

# Sliswap Protocol Overview

## Concept and Innovation

Sliswap is a next-generation decentralized exchange (DEX) built on the Endless blockchain, featuring an innovative adaptive invariant model that dynamically adjusts to market conditions. Unlike traditional constant product market makers (CPMM), Sliswap uses a sophisticated mathematical model that responds to trading patterns and liquidity flows.

### Key Innovations

**Adaptive Invariant Model**: The core innovation is the dynamic invariant `(s x + y - c) x y = k`, where:
- `s` (slope parameter) adapts to recent trading patterns, moving toward the current price ratio
- `c` (offset parameter) provides additional flexibility and can be scaled with liquidity changes
- This creates more stable prices and reduced slippage compared to traditional AMMs

**Dynamic Parameter Updates**: 
- `s` updates proportionally to trade size, nudging toward `y/x` ratio
- `c` scales with LP token supply changes and updates after each trade
- Parameters are initialized as `s₀ = y/x` and `c₀ = ¾y`

**Enhanced Price Stability**: The adaptive model provides better price stability during volatile periods while maintaining capital efficiency.

## Fee Structure and Economic Model

Sliswap implements a dual-sided fee model totaling 0.30% per trade, strategically applied to both input and output legs for optimal economic efficiency.

### Fee Breakdown
- **Input Side (0.15%)**: Applied to `amount_in` before swap calculation
  - `swap_fee` + `dao_fee` (configurable split)
- **Output Side (0.15%)**: Applied to `amount_out` after swap calculation  
  - Split as 0.12% + 0.03% (governance-configurable destinations)

### Economic Benefits
- **Reduced MEV**: Dual-sided fees make sandwich attacks less profitable
- **Fair Distribution**: Fees support both protocol development and governance
- **Competitive Rates**: 0.30% total is competitive with major DEXs
- **Configurable**: Fee splits can be adjusted through governance

## Trading and Liquidity Management

Sliswap provides comprehensive trading and liquidity management tools with advanced routing capabilities and optimal liquidity provision.

### Trading Features
- **Single-Hop Swaps**: Direct token-to-token exchanges with minimal slippage
- **Multi-Hop Routing**: Complex trades through multiple pools for better prices
- **Exact-In/Exact-Out**: Both input and output amount specifications supported
- **Slippage Protection**: Built-in minimum output and maximum input controls

### Liquidity Management
- **Optimal Amount Calculation**: Automatic calculation of ideal token ratios
- **LP Token System**: Standardized liquidity provider tokens with 8 decimals
- **Minimum Liquidity Lock**: Initial liquidity permanently locked for price stability
- **Proportional Withdrawals**: Fair redemption based on LP token holdings

### Advanced Features
- **Connector Coin System**: EDS-based routing for enhanced liquidity
- **Black Hole Locking**: Permanent LP token locking mechanism
- **Price Impact Calculation**: Sophisticated impact measurement across hops

## Governance and Security

Sliswap implements a robust governance and security framework designed for both efficiency and user protection.

### Governance Structure
- **Manager-Based System**: Designated managers control critical parameters
- **Fee Management**: Separate fee managers for swap and DAO fee configuration
- **Pause Controls**: Emergency pause functionality for protocol safety
- **Parameter Updates**: Dynamic adjustment of fees and pool-specific settings

### Security Features
- **Access Controls**: Strict authorization for administrative functions
- **Parameter Constraints**: Built-in limits (e.g., max 0.30% total fees)
- **Input Validation**: Comprehensive checks for all user inputs
- **Event Tracking**: Complete audit trail for all protocol activities

### Transparency
- **Public Interfaces**: All functions and parameters are publicly viewable
- **Event Emissions**: Detailed events for swaps, liquidity changes, and governance actions
- **Open Source**: Full protocol code available for review and verification

## Technical Architecture

### Core Modules
- **Liquidity Pool**: Core AMM logic with adaptive invariant
- **Router**: User-friendly interface for swaps and liquidity management
- **Oracle**: Price feed and historical data management
- **Connector**: EDS-based routing and liquidity enhancement

### Key Benefits
- **Capital Efficiency**: Higher trading volume per unit of liquidity
- **Reduced Slippage**: Adaptive model minimizes price impact
- **MEV Resistance**: Dual-sided fees and sophisticated routing
- **Scalability**: Optimized for high-frequency trading

## Getting Started

For developers and integrators:
- **[Developer Documentation](../developer/)**: Complete technical documentation
  - **Contract Guides**: Detailed interface documentation for all modules
  - **Protocol Specs**: Mathematical models and fee calculations
  - **Integration Examples**: Code samples for common use cases
  - **API Reference**: Complete function signatures and parameters


----

[Sliswap]: https://www.sliswap.com/