---
title: Fees
parent: Protocol
nav_order: 5
---
<head>
   <script type="text/javascript" async
      src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
   </script>
   <script>
      MathJax = {
        tex: {
          inlineMath: [['$', '$'], ['$`', '`$'], ['\\(', '\\)']],
          displayMath: [['```math', '```'], ['$$', '$$'], ['\\[', '\\]']]
        }
      };
   </script>
</head>

# Fee Mechanism 

This document reflects the latest fee design in Sliswap and aligns with the new liquidity model `(s x + y - c) x y = k` and the README notes.

## 1) Fee structure (total 0.30%)
- 0.15% charged on `amount_in` (covers swap_fee + dao_fee)
- 0.15% charged on `amount_out` (split 0.12% + 0.03%)

Application order during a swap of token0 -> token1:
1. Compute raw output from the invariant with the effective input after input-side fee
2. Deduct output-side fee from the raw output

Mathematically:

$$
\text{Input-side fee} = amount\_in \cdot 0.0015
\quad , \quad
\Delta x_{eff} = amount\_in \cdot (1 - 0.0015)
$$

Solve the invariant with `x' = x + \Delta x_{eff}` to get `y'`, then:

$$
amount\_out\_{raw} = y - y'
\quad , \quad
\text{Output-side fee} = amount\_out\_{raw} \cdot 0.0015
\quad , \quad
amount\_out = amount\_out\_{raw} \cdot (1 - 0.0015)
$$

Distribution of the 0.30%:
- Input leg 0.15% = swap_fee + dao_fee (protocol-tunable split)
- Output leg 0.15% = 0.12% + 0.03% (treasury/LP/other destinations per governance settings)


## 3) Implementation flow

1. Compute `k = (s x + y - c) x y` from pre-trade reserves
2. Apply input-side fee to get `\Delta x_{eff}`
3. Set `x' = x + \Delta x_{eff}` and solve quadratic for `y'`
4. `amount_out_raw = y - y'`
5. Apply output-side fee: `amount_out = amount_out_raw * (1 - 0.0015)`
6. Update reserves and then update `s` and `c` as defined in the liquidity spec

Notes:
- Use consistent snapshots: `x_before = x` (pre-trade), `y` in `c_new` uses post-trade `y'`.
- Use safe math with rounding that favors users on outputs.

## 4) Pseudocode (concise)

```text
dx_fee = amount_in * 0.0015
dx_eff = amount_in - dx_fee
x1 = x + dx_eff
k = (s*x + y - c) * x * y
A = s*x1 - c
disc = (A*x1)^2 + 4*k*x1
y1 = (-A*x1 + sqrt(disc)) / (2*x1)
amount_out_raw = y - y1
out_fee = amount_out_raw * 0.0015
amount_out = amount_out_raw - out_fee
```

## 5) Configuration and accounting
- The `swap_fee` vs `dao_fee` split within the 0.15% on input is configurable.
- The 0.15% on output is distributed as `0.12% + 0.03%` per governance settings.
- Fees should be accounted explicitly to designated recipients (e.g., treasury, LP incentives, staking contracts).

## 6) Edge cases and safety
- Reject trades that lead to invalid roots or `y' \notin (0, y)`.
- Enforce minimum output after fees to respect slippage protection.
- Cap `amount_in` to avoid overflow and extremely large curvature changes.
- Use basis points (bps) for configuration; 1 bps = 0.01%.

## 7) Examples

Single-hop illustration (numbers rounded):
- `x = 1,000`, `y = 2,000`, `s = 2`, `c = 1,500`, `amount_in = 100`
- `dx_eff = 99.85`; solve quadratic to get `y1`; `amount_out_raw = y - y1`
- `amount_out = amount_out_raw * 0.9985`

For multi-hop, compute per hop using the same fee application order.
