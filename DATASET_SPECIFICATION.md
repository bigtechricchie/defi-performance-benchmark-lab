# Dataset Specification

## 1. Purpose

This document defines the deterministic market dataset and update sequence used by the baseline DeFi Performance Benchmark Lab workload.

Its purpose is to ensure that Angular 22 and SolidJS process:

- the same initial market records;
- the same portfolio values;
- the same rows on every update cycle;
- the same resulting numeric values;
- the same update frequency.

The dataset must be reproducible without network access, external data sources, uncontrolled randomness, or framework-specific behaviour.

---

## 2. Design Principles

The baseline dataset follows these rules:

- deterministic;
- locally generated;
- framework-independent;
- dependency-free where practical;
- straightforward to audit;
- straightforward to reproduce;
- sufficiently varied to resemble a market-oriented financial interface;
- intentionally synthetic.

The dataset is not intended to model economically realistic market behaviour.

Its purpose is to provide a stable and repeatable frontend workload.

---

## 3. Market Count

The baseline contains exactly:

```text
500 market records
```

Market indexes range from:

```text
0
```

through:

```text
499
```

Each market receives a stable identifier derived from its zero-based index.

---

## 4. Market Identifier

Each market identifier follows:

```text
market-000
market-001
market-002
...
market-499
```

The numeric portion must always contain three digits.

Examples:

```text
index 0   → market-000
index 7   → market-007
index 42  → market-042
index 499 → market-499
```

The identifier must remain stable for the lifetime of the benchmark run.

---

## 5. Asset Symbol

Each synthetic asset symbol follows:

```text
AST000
AST001
AST002
...
AST499
```

Examples:

```text
index 0   → AST000
index 7   → AST007
index 42  → AST042
index 499 → AST499
```

These symbols are deliberately synthetic.

Real token symbols are not used because the benchmark does not need external market semantics.

---

## 6. Market Record

Every market record contains exactly these baseline fields:

```text
id
symbol
price
supplyApy
borrowApy
availableLiquidity
utilisation
```

Conceptually:

```text
Market
├── id
├── symbol
├── price
├── supplyApy
├── borrowApy
├── availableLiquidity
└── utilisation
```

Framework implementations may use idiomatic language-level types, but the observable values must remain equivalent.

---

## 7. Numeric Representation

All baseline calculations must be deterministic.

Implementations must not use uncontrolled floating-point accumulation across update cycles.

Where decimal display values are required, values should be derived from integer source units.

The baseline uses fixed integer scales:

```text
price:               cents
APY:                 basis points
available liquidity: whole synthetic currency units
utilisation:         basis points
```

Definitions:

```text
100 cents           = 1 display currency unit
100 basis points    = 1%
10,000 basis points = 100%
```

This avoids relying on binary floating-point arithmetic as the source of truth for benchmark state.

---

## 8. Initial Price

For market index `i`, initial price in cents is:

```text
priceCents = 10_000 + (i × 137)
```

Examples:

```text
index 0
10,000 cents
= 100.00

index 1
10,137 cents
= 101.37

index 10
11,370 cents
= 113.70
```

The formula exists only to provide deterministic variation across records.

---

## 9. Initial Supply APY

For market index `i`, initial supply APY in basis points is:

```text
supplyApyBps = 100 + ((i × 17) mod 700)
```

This produces values between:

```text
100 bps
and
799 bps
```

equivalent to:

```text
1.00%
to
7.99%
```

---

## 10. Initial Borrow APY

For market index `i`, initial borrow APY in basis points is:

```text
borrowApyBps = supplyApyBps + 150 + ((i × 11) mod 250)
```

This guarantees that the initial borrow APY remains above the corresponding supply APY.

The relationship exists only to produce plausible-looking benchmark data.

It must not be interpreted as a lending-protocol economic model.

---

## 11. Initial Available Liquidity

For market index `i`, initial available liquidity is:

```text
availableLiquidity = 1_000_000 + (i × 25_000)
```

Examples:

```text
index 0
1,000,000

index 1
1,025,000

index 100
3,500,000
```

The unit is synthetic and has no external currency meaning.

---

## 12. Initial Utilisation

For market index `i`, initial utilisation in basis points is:

```text
utilisationBps = 2_000 + ((i × 29) mod 7_500)
```

This produces baseline values between:

```text
2,000 bps
and
9,499 bps
```

equivalent to:

```text
20.00%
to
94.99%
```

---

## 13. Portfolio Summary

The baseline portfolio summary contains:

```text
walletBalance
suppliedAmount
borrowedAmount
collateralValue
healthFactor
```

The initial values are fixed.

Use:

```text
wallet balance:      250,000.00
supplied amount:     175,000.00
borrowed amount:      75,000.00
collateral value:    225,000.00
health factor:             1.80
```

These values remain unchanged during the initial baseline update workload.

The portfolio summary exists primarily to ensure both applications render a small static summary alongside the frequently updating market list.

Dynamic portfolio updates may be introduced as a later benchmark scenario.

---

## 14. Update Cycle

Update cycles are zero-based:

```text
cycle 0
cycle 1
cycle 2
...
```

A cycle occurs every:

```text
250 ms
```

during the sustained benchmark workload.

The first update after the initial render is:

```text
cycle 0
```

---

## 15. Updated Rows Per Cycle

Exactly:

```text
50 market rows
```

must be updated during every cycle.

The rows form one contiguous group.

There are:

```text
10 groups
```

because:

```text
500 total rows ÷ 50 rows per group = 10 groups
```

---

## 16. Row-Selection Rule

The group for cycle `c` is:

```text
group = c mod 10
```

The first market index updated is:

```text
startIndex = group × 50
```

The updated indexes are:

```text
startIndex
through
startIndex + 49
```

Examples:

```text
cycle 0
→ indexes 0–49

cycle 1
→ indexes 50–99

cycle 2
→ indexes 100–149

...

cycle 9
→ indexes 450–499

cycle 10
→ indexes 0–49
```

This guarantees that every market is updated once every ten cycles.

At a 250 ms update interval, each market therefore receives an update approximately every:

```text
2.5 seconds
```

---

## 17. Update Principle

Updated values must be derived from:

```text
market index
+
cycle number
```

They must not depend on:

- wall-clock time;
- previous floating-point calculations;
- browser timing;
- random-number generators;
- network state;
- framework scheduler behaviour.

This means that a market value can be independently reconstructed for any cycle.

---

## 18. Price Update

For market index `i` during cycle `c`, calculate:

```text
priceDeltaCents =
((i × 13 + c × 7) mod 41) - 20
```

The cycle price becomes:

```text
updatedPriceCents =
initialPriceCents + priceDeltaCents
```

The delta therefore ranges from:

```text
-20 cents
to
+20 cents
```

The update is calculated from the initial value, not from the previous cycle's value.

This prevents cumulative drift.

---

## 19. Supply APY Update

For market index `i` during cycle `c`:

```text
supplyDeltaBps =
((i × 5 + c × 3) mod 21) - 10
```

Then:

```text
updatedSupplyApyBps =
initialSupplyApyBps + supplyDeltaBps
```

The delta ranges from:

```text
-10 bps
to
+10 bps
```

Again, the value is derived from the initial value rather than accumulated from previous cycles.

---

## 20. Borrow APY Update

For market index `i` during cycle `c`:

```text
borrowDeltaBps =
((i × 7 + c × 5) mod 31) - 15
```

Then:

```text
updatedBorrowApyBps =
initialBorrowApyBps + borrowDeltaBps
```

The delta ranges from:

```text
-15 bps
to
+15 bps
```

---

## 21. Available Liquidity Update

For market index `i` during cycle `c`:

```text
liquidityDelta =
((i × 97 + c × 53) mod 20_001) - 10_000
```

Then:

```text
updatedAvailableLiquidity =
initialAvailableLiquidity + liquidityDelta
```

The delta ranges from:

```text
-10,000
to
+10,000
```

Because initial liquidity is at least 1,000,000, this formula cannot produce a negative baseline value.

---

## 22. Utilisation Update

For market index `i` during cycle `c`:

```text
utilisationDeltaBps =
((i × 11 + c × 13) mod 101) - 50
```

Then calculate:

```text
candidateUtilisationBps =
initialUtilisationBps + utilisationDeltaBps
```

Clamp the result to:

```text
0
through
10,000
```

The displayed utilisation must therefore always remain between:

```text
0%
and
100%
```

---

## 23. No Cumulative Mutation

A critical baseline rule is:

```text
updated value
=
initial value
+
deterministic cycle-derived delta
```

not:

```text
previous value
+
new delta
```

This means the workload does not accumulate numerical drift over the 60-second run.

It also makes debugging considerably easier because any expected state can be reconstructed directly from:

```text
market index
cycle number
```

---

## 24. Unchanged Rows

Rows outside the selected 50-row group for a given cycle must remain unchanged during that cycle.

The benchmark should not reconstruct or replace all 500 market records merely because 50 values changed unless the framework's idiomatic state model makes that unavoidable.

The intended workload is:

```text
500 rendered rows
50 logically changed rows
450 logically unchanged rows
```

This distinction is fundamental to the benchmark.

---

## 25. Object Identity and Framework Semantics

The benchmark specification defines observable state, not internal object identity.

Each framework may use its normal idiomatic update model.

The Angular and SolidJS implementations do not need to manipulate JavaScript objects identically.

However, the implementation must not deliberately introduce unnecessary whole-dataset replacement solely to disadvantage one framework.

Any framework-specific state-management decision that materially affects the workload should be documented.

---

## 26. Display Formatting

Formatting must remain equivalent across implementations.

Baseline display rules are:

```text
price
→ 2 decimal places

supply APY
→ 2 decimal places followed by %

borrow APY
→ 2 decimal places followed by %

available liquidity
→ whole units with grouping separators where implemented consistently

utilisation
→ 2 decimal places followed by %

health factor
→ 2 decimal places
```

Framework implementations should prefer platform-native formatting unless there is a demonstrated reason to add a formatting dependency.

---

## 27. Deterministic Examples

Implementations should eventually validate selected known values from this specification.

Examples include:

```text
market index 0

id:
market-000

symbol:
AST000

initial price:
100.00

initial supply APY:
1.00%

initial borrow APY:
2.50%

initial liquidity:
1,000,000

initial utilisation:
20.00%
```

For cycle 0, market index 0 is updated.

Its deltas are:

```text
price delta:
-20 cents

supply APY delta:
-10 bps

borrow APY delta:
-15 bps

liquidity delta:
-10,000

utilisation delta:
-50 bps
```

Therefore its cycle-0 state is:

```text
price:
99.80

supply APY:
0.90%

borrow APY:
2.35%

available liquidity:
990,000

utilisation:
19.50%
```

These examples should later become automated test fixtures in both framework implementations.

---

## 28. Workload Duration

The benchmark runs for:

```text
60 seconds
```

with an update interval of:

```text
250 ms
```

The expected theoretical number of update cycles is:

```text
240
```

because:

```text
60,000 ms ÷ 250 ms = 240
```

The benchmark driver should eventually record the actual completed cycle count.

A run that materially fails to execute the intended workload should be treated according to the invalid-run rules defined in `BENCHMARK_SPECIFICATION.md`.

---

## 29. Scheduling Behaviour

The benchmark should request updates at the documented 250 ms cadence.

However, JavaScript timers do not guarantee exact execution at the requested interval.

The benchmark must therefore not pretend that:

```text
setInterval(..., 250)
```

guarantees perfect 250 ms execution.

Actual scheduling delay is part of runtime behaviour.

Both implementations must use equivalent scheduling semantics for the baseline.

The exact timer implementation should be reviewed when framework code is introduced.

---

## 30. Dataset Generation

The baseline dataset should be generated programmatically from this specification.

Do not commit a manually maintained 500-record dataset unless a later requirement demonstrates that a static fixture is preferable.

Programmatic generation reduces:

- repository noise;
- manual data-entry mistakes;
- maintenance burden.

The generation code must itself remain small and deterministic.

---

## 31. Shared Runtime Code

The Angular and SolidJS applications must not initially depend on a shared runtime benchmark-data package.

Avoid introducing:

```text
packages/
└── benchmark-data/
```

for the baseline.

Instead:

```text
DATASET_SPECIFICATION.md
        │
        ├── Angular implementation
        │
        └── SolidJS implementation
```

Both implementations should independently satisfy the same specification.

Known-value tests can verify that their generated data is equivalent.

This prevents a shared benchmark runtime library from becoming another variable in the framework comparison.

---

## 32. Dependencies

Dataset generation must not require an external random-number or fixture-generation library.

The formulas in this document are sufficient.

If implementation later appears to require an additional dependency, that requirement must be justified before introducing it.

---

## 33. Boundary Conditions

Implementations must correctly handle:

```text
first market index:
0

last market index:
499

first cycle:
0

group wrap:
cycle 10 returns to indexes 0–49

utilisation lower bound:
0 bps

utilisation upper bound:
10,000 bps
```

Negative market indexes and indexes greater than 499 are outside the benchmark dataset.

---

## 34. Test Expectations

When framework code is introduced, both implementations should test at minimum:

- total generated market count is 500;
- first market identifier;
- last market identifier;
- selected known initial values;
- exactly 50 rows selected per cycle;
- cycle-group wrap behaviour;
- deterministic values for known index/cycle combinations;
- utilisation clamping;
- unchanged rows remain logically unchanged;
- repeated generation produces equivalent results.

The exact test framework should remain framework-native where practical.

---

## 35. Change Control

Once framework implementation begins, changes to these formulas affect comparability.

Any future modification must therefore:

1. explain the requirement;
2. update this document first or in the same isolated methodology change;
3. update both framework implementations;
4. update corresponding tests;
5. clearly separate results produced under incompatible dataset versions.

Dataset formulas must not be tuned after benchmark results are observed merely to produce a preferred outcome.

---

## 36. Baseline Dataset Summary

The baseline data contract is:

```text
markets:                    500
market indexes:           0–499
update groups:               10
rows per update group:       50
update interval:          250 ms
run duration:               60 s
theoretical cycles:         240

data generation:
deterministic formulas

external data:
none

randomness:
none

shared runtime data package:
none
```

The benchmark implementations must satisfy this contract before performance measurements are considered valid.
