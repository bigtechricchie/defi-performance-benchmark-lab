# Benchmark Specification

## 1. Purpose

This document defines the baseline workload, measurement rules, fairness constraints, and validity requirements for the DeFi Performance Benchmark Lab.

Its purpose is to ensure that Angular 22 and SolidJS are compared using the same workload and measurement conditions.

The benchmark must measure implementation behaviour rather than framework reputation, assumptions, or selectively chosen scenarios.

Framework-specific optimisation experiments may be introduced later, but the initial baseline comparison must remain structurally equivalent.

---

## 2. Benchmark Question

The baseline experiment asks:

> How do Angular 22 and SolidJS behave when rendering and repeatedly updating the same representative DeFi-style market interface under equivalent production conditions?

The experiment is not intended to establish that either framework is universally faster or more suitable for all applications.

Results apply only to the workload, environment, framework versions, implementation choices, and measurement methodology documented by this repository.

---

## 3. Baseline Workload

The initial benchmark represents a market-oriented DeFi interface containing:

```text
500 market rows
```

Each market row contains:

```text
asset symbol
asset price
supply APY
borrow APY
available liquidity
market utilisation
```

The benchmark also contains a small portfolio summary containing:

```text
wallet balance
supplied amount
borrowed amount
collateral value
health factor
```

The baseline must render the same information in both implementations.

---

## 4. Dataset

The benchmark must use deterministic synthetic data.

Real blockchain or market data must not be used for the baseline because external services would introduce:

- network variability;
- provider latency;
- rate limiting;
- data availability differences;
- non-deterministic market movement.

The benchmark dataset must therefore be generated locally from a fixed specification.

Both framework implementations must receive equivalent initial values.

The generated dataset must be reproducible between runs.

Random values must not be generated from uncontrolled randomness during measurement.

If pseudo-random generation is later used, it must use a fixed seed and the same generation rules for both implementations.

---

## 5. Update Workload

The benchmark should test repeated partial data updates rather than replacing the entire dataset on every cycle.

The initial update scenario is:

```text
total market rows:       500
rows changed per cycle:   50
update interval:         250 ms
```

Each update cycle modifies the same categories of market values in both implementations.

Representative changes may include:

```text
asset price
supply APY
borrow APY
available liquidity
market utilisation
```

The sequence of affected rows and resulting values must be deterministic.

The benchmark must not allow each framework implementation to independently choose which rows to update.

---

## 6. Update Duration

The repeated-update workload should run for:

```text
60 seconds
```

per measured run unless this specification is deliberately revised in a later commit.

This duration is intended to expose:

- repeated rendering behaviour;
- memory growth;
- garbage-collection activity;
- CPU usage;
- responsiveness under sustained updates.

---

## 7. Baseline User Interaction

The first benchmark should include one deterministic interaction scenario in addition to background market updates.

The initial interaction is:

```text
select one market row
```

The selected row should display a small detail area containing the same market information in both implementations.

The interaction should occur while repeated market updates are active.

The benchmark should measure whether sustained reactive updates materially affect interaction responsiveness.

More complex actions such as:

```text
sorting
filtering
searching
routing
forms
wallet interaction
```

are excluded from the first baseline.

They may be introduced as separate benchmark scenarios later.

---

## 8. Rendering Requirements

Both implementations must render all 500 baseline market rows.

The baseline must not use:

```text
virtual scrolling
pagination
windowing
server-side rendering
partial rendering based on viewport visibility
```

unless these become the subject of a later dedicated comparison.

This restriction exists so that the initial benchmark compares equivalent rendered workloads.

---

## 9. Styling Requirements

The baseline should use minimal static native CSS.

Styling should be sufficient to create:

- a readable table or table-like market layout;
- a small portfolio summary;
- visible row selection;
- basic responsive containment where necessary.

The benchmark must not introduce:

```text
component libraries
CSS-in-JS
runtime styling systems
animation libraries
utility CSS frameworks
```

during the baseline comparison.

Visual design should remain intentionally simple so that framework behaviour, rather than styling infrastructure, remains the principal variable.

---

## 10. Dependency Rules

Each implementation should use only dependencies required by its framework-generated application and the benchmark itself.

Additional dependencies must be justified before introduction.

The baseline must not add libraries for:

```text
state management
data generation
benchmark timing
HTTP communication
charting
formatting
UI components
```

when equivalent behaviour can reasonably be implemented using framework or platform-native capabilities.

Direct dependency versions must be pinned exactly.

Lockfiles must be committed.

---

## 11. Production Builds

Performance measurements must use production builds.

Development-server measurements must not be treated as representative benchmark results.

Both framework implementations must be built using their documented production build configuration.

The exact build commands and relevant configuration must be recorded alongside the benchmark results.

---

## 12. Measurement Environment

Comparative runs must use the same:

```text
physical machine
operating system
browser
browser version
power configuration
display configuration where relevant
repository revision
measurement procedure
```

The environment used for published results must be recorded.

At minimum, the benchmark record should include:

```text
CPU
installed memory
operating system and version
browser and version
Node.js version
package-manager version
Angular version
SolidJS version
```

Where practical, relevant hardware details such as CPU model should be included.

---

## 13. Browser State

Measured runs should use a clean and consistent browser state.

To reduce unrelated variation:

- unnecessary browser tabs should be closed;
- unnecessary extensions should be disabled where practical;
- browser developer tooling should be configured consistently;
- background workloads should be minimised;
- the same browser profile or equivalent clean profiles should be used.

The benchmark must document any tooling whose presence can materially affect runtime behaviour.

---

## 14. Warm-Up

The first page load after a build or browser restart may include effects that differ from subsequent runs.

The measurement procedure should therefore distinguish between:

```text
cold-load measurements
```

and:

```text
steady-state workload measurements
```

The baseline repeated-update test should perform one unrecorded warm-up run before measured repeated-update runs begin.

Cold-start behaviour should be measured separately rather than mixed into steady-state measurements.

---

## 15. Number of Runs

Each framework should complete:

```text
1 warm-up run
5 measured runs
```

for each baseline scenario.

A single run must not be presented as representative performance evidence.

The same run count and procedure must be used for both frameworks.

---

## 16. Measurement Order

To reduce systematic bias caused by machine temperature, background activity, or run order, benchmark execution should alternate frameworks where practical.

For example:

```text
Angular warm-up
SolidJS warm-up

Angular run 1
SolidJS run 1

SolidJS run 2
Angular run 2

Angular run 3
SolidJS run 3
```

The exact sequence should be recorded.

The sequence must not deliberately favour one implementation.

---

## 17. Planned Metrics

The baseline benchmark is expected to collect the following categories of measurements.

### Production Output

Record:

- relevant production JavaScript output size;
- total relevant generated asset size;
- compressed size where measured consistently.

Bundle size calculations must use the same measurement method for both implementations.

### Initial Rendering

Record behaviour associated with rendering the initial 500-row interface.

The exact browser metric used must be documented before published comparison results are produced.

### CPU Behaviour

Observe CPU activity during the sustained 60-second update workload.

Measurements should use the same browser tooling and collection procedure for both implementations.

### Memory Behaviour

Observe memory consumption during:

- initial render;
- sustained updates;
- completion of the workload.

The purpose is not merely to capture a single memory number but to identify material differences or unexpected growth.

### Long Tasks

Observe long-running main-thread tasks during sustained updates where supported by the selected measurement tooling.

### Interaction Responsiveness

Measure or record the responsiveness of the baseline row-selection interaction while background updates are active.

### DOM / Rendering Behaviour

Where measurement tooling permits, record useful evidence concerning:

- rendering work;
- layout;
- style recalculation;
- DOM updates.

These measurements must be interpreted carefully and should not be converted into unsupported conclusions.

---

## 18. Result Reporting

For every metric measured repeatedly, the published result should preserve the individual run values.

Do not publish only the single best run.

Where statistical summaries are useful, the report may include:

```text
median
minimum
maximum
```

Arithmetic mean may also be included where appropriate, but the raw run values should remain available.

The benchmark must not discard slower runs merely because they weaken a preferred conclusion.

---

## 19. Invalid Runs

A run should be considered invalid when a material external condition makes it non-comparable.

Examples may include:

- browser crash;
- benchmark implementation error;
- interrupted workload;
- unexpected operating-system update activity;
- unrelated high-CPU process;
- developer tooling misconfiguration;
- incorrect dataset;
- wrong production build;
- wrong framework version;
- incomplete 60-second workload.

An invalid run should be documented and repeated.

A run must not be discarded solely because its result is unexpectedly slow or fast.

---

## 20. Fairness Constraints

The baseline implementations must remain equivalent in observable behaviour.

Neither implementation may receive an advantage through:

- fewer rendered records;
- fewer updates;
- different update values;
- a lower update frequency;
- omitted UI fields;
- hidden pagination;
- virtual scrolling;
- different interaction requirements;
- development versus production build differences.

Framework-native implementation techniques are allowed where they represent the normal, documented way to implement equivalent behaviour.

The benchmark should not force both frameworks into identical internal programming models.

The requirement is equivalent behaviour and workload, not artificially identical source code.

---

## 21. Framework-Specific Optimisation

The initial baseline should favour clear, idiomatic implementations.

After the baseline has been measured, additional scenarios may investigate framework-specific optimisation techniques.

Such experiments must be reported separately.

For example:

```text
baseline Angular
baseline SolidJS

then later:

optimised Angular
optimised SolidJS
```

An optimised implementation must not silently replace the baseline and be presented as though the methodologies were identical.

---

## 22. Benchmark Integrity

The project must remain neutral with respect to the result.

The repository must not be designed to prove a predetermined conclusion such as:

```text
SolidJS is faster
```

or:

```text
Angular is sufficiently fast
```

A result that contradicts the original architectural expectation is still a valid result.

Unexpected results should be investigated rather than explained away.

---

## 23. Reproducibility

Published benchmark results should eventually include enough information for another engineer to reproduce the experiment.

This should include:

- source revision;
- framework versions;
- Node.js version;
- package-manager version;
- production build commands;
- benchmark procedure;
- benchmark dataset specification;
- update workload;
- browser version;
- hardware information;
- operating-system information;
- recorded raw results.

---

## 24. Baseline Non-Goals

The baseline benchmark does not attempt to measure:

- blockchain latency;
- Ethereum RPC performance;
- wallet performance;
- backend performance;
- network API performance;
- server-side rendering;
- hydration;
- real-time WebSocket infrastructure;
- application security;
- accessibility quality;
- developer productivity;
- maintainability;
- ecosystem maturity;
- framework learning curve.

These may matter when choosing a production framework, but they are outside the initial performance experiment.

---

## 25. Change Control

Changes to the benchmark specification after implementation begins must be deliberate and reviewable.

A methodology change that affects comparability should:

1. explain why the previous specification is insufficient;
2. update both framework implementations where necessary;
3. invalidate or clearly separate incompatible previous measurements;
4. be committed independently from unrelated implementation changes.

The benchmark methodology must not be quietly changed after results are observed.

---

## 26. Initial Baseline Summary

The first benchmark baseline is therefore:

```text
market rows:              500
rows updated per cycle:    50
update interval:          250 ms
sustained workload:        60 s
warm-up runs:               1
measured runs:              5
rendering:             all rows
data source:       deterministic synthetic data
styling:           minimal native CSS
build mode:             production
backend:                     none
blockchain:                  none
wallet integration:         none
```

This specification should be reviewed and accepted before Angular or SolidJS applications are generated.
