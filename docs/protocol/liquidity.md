---
title: Liquidity
parent: Protocol
nav_order: 3
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

# Liquidity

## Market Making and Fee Model

### 1. Invariant

```math
(s x + y - c) x y = k
```

- Let `x` be the reserve of token0, `y` be the reserve of token1.
- `s` and `c` are dynamic control parameters; `k` is the pool invariant.

Compared to the constant product market maker, the term `(s x + y - c)` introduces a price shape that adapts with recent order flow (`s`) and a shift term (`c`) that can be scaled with liquidity and nudged by trades.

### 2. Updating the slope parameter s

- Initialization:

```math
s_{0} = \frac{y}{x}
```

- Post-trade incremental update, proportional to trade size and nudging `s` toward the current price ratio `y/x`:

```math
s_{new} = s \times \Bigl(1 \pm \frac{5}{1000} \cdot \frac{\Delta x}{x_{before}}\Bigr)
```

Notes:
- `\Delta x` is absolute (unsigned) input size in token0 units.
- The sign is chosen to steer `s` toward `y/x` given trade direction and price deviation.

### 3. Updating the offset parameter c

- Initialization:

```math
c_{0} = \frac{3}{4} \cdot y
```

- Liquidity changes: scale `c` proportionally with `lp_token_supply`.
- After each swap (with `y` taken as the post-trade token1 balance):

```math
c_{new} = \Bigl(\bigl(\tfrac{3}{2} c - y\bigr) \cdot \frac{s_{new}}{s} + y\Bigr) \cdot \tfrac{2}{3}
```

Equivalent form:

```math
c_{new} = \tfrac{2}{3} y + c \cdot \frac{s_{new}}{s} - y \cdot \frac{s_{new}}{s} \cdot \tfrac{2}{3}
```

### 4. Fees (total 0.30%)

- Charge 0.15% on `amount_in` (covers `swap_fee + dao_fee`).
- Charge 0.15% on `amount_out` (split into 0.12% + 0.03%).

Implementation tip:
- First solve the raw output from the invariant (no fees), then apply the two-legged fees to input and output. Keep the timing consistent for `x_before` (pre-trade) and `y` (post-trade).

### 5. Solving a swap (amount_out)

Given an input of token0 `amount_in`, define pre-trade reserves `(x, y)`. After deducting the input-side fee, let

```math
\Delta x_{eff} = amount\_in \cdot (1 - 0.0015)
```

Then the tentative post-trade reserves are `x' = x + \Delta x_{eff}`, `y' = y - amount_out_raw`. Enforce the invariant with current `(s, c)` to find `amount_out_raw`:

```math
f(y') = (s x' + y' - c) x' y' - k = 0
```

This is a quadratic in `y'` (since `x'` is known in this step). Select the economically valid root `0 < y' < y` and compute:

```math
amount\_out\_{raw} = y - y'
```

Finally apply the output-side fee:

```math
amount\_out = amount\_out\_{raw} \cdot (1 - 0.0015)
```

Edge handling:
- If the quadratic yields no valid root in `(0, y)`, cap by available liquidity and revert with InsufficientLiquidity.
- Use high-precision math and stable quadratic formula to avoid catastrophic cancellation.

### 6. Closed-form for y'

Let `x' = x + \Delta x_{eff}` and define `A = s x' - c`. The invariant becomes:

```math
(A + y') x' y' = k
```

which expands to a standard quadratic in `y'`:

```math
x' (y')^2 + A x' y' - k = 0
```

Solve with the quadratic formula and choose the root in `(0, y)`:

```math
y' = \frac{ -A x' + \sqrt{ (A x')^2 + 4 k x' } }{ 2 x' }
```

where `k = (s x + y - c) x y` from pre-trade reserves.

### 7. Updating s and c after the swap

Once `amount_out` is finalized and reserves are updated to `(x', y')`, update parameters:

1) Update `s` using `\Delta x = |amount_in|` and `x_before = x`:

```math
s_{new} = s \times \Bigl(1 \pm \frac{5}{1000} \cdot \frac{\Delta x}{x}\Bigr)
```

2) Update `c` using `y = y'` (post-trade):

```math
c_{new} = \Bigl(\bigl(\tfrac{3}{2} c - y'\bigr) \cdot \frac{s_{new}}{s} + y'\Bigr) \cdot \tfrac{2}{3}
```

On mint/burn liquidity, scale `c` proportionally to `lp_token_supply`.

### 8. Reference pseudocode

```text
input: x, y, s, c, amount_in
// 1) pre-trade invariant
k = (s*x + y - c) * x * y

// 2) input-side fee
dx_eff = amount_in * (1 - 0.0015)
x1 = x + dx_eff

// 3) solve y1 from quadratic
A = s * x1 - c
disc = (A*x1)^2 + 4*k*x1
y1 = (-A*x1 + sqrt(disc)) / (2*x1)
amount_out_raw = y - y1

// 4) output-side fee
amount_out = amount_out_raw * (1 - 0.0015)

// 5) finalize state
x := x1
y := y - amount_out

// 6) update s and c
s_new = s * (1 +/- 0.005 * (amount_in / x_before))
c_new = (((3/2)*c - y) * (s_new/s) + y) * (2/3)
```

### 9. Parameter bounds and safety
- Clamp `s` to a reasonable range to avoid extreme curvature, e.g., `s_min > 0` and `s_max` based on historical volatility.
- Ensure `c` remains non-negative and scales linearly with LP supply changes.
- Validate inputs: `amount_in > 0`, reserves non-zero, and post-trade `y1` within `(0, y)`.
- Use 256-bit integer math or fixed-point with rounding toward user safety on outputs.

### 10. Worked example (illustrative)
- Reserves: `x = 1,000`, `y = 2,000`
- Params: `s = y/x = 2`, `c = 0.75*y = 1,500`
- Trade: `amount_in = 100`
1) `k = (s x + y - c) x y = (2*1000 + 2000 - 1500)*1000*2000 = 1,500 * 2,000,000`
2) `dx_eff = 100 * (1 - 0.0015) = 99.85`, `x' = 1099.85`
3) `A = s x' - c = 2*1099.85 - 1500 = 699.7`
4) Solve `y'` via quadratic, then `amount_out_raw = y - y'`
5) `amount_out = amount_out_raw * (1 - 0.0015)`

Numbers will vary with precise rounding; production should use the stable quadratic and safe rounding for `amount_out`.
