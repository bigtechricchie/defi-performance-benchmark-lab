# DeFi Performance Benchmark Lab

A reproducible frontend performance benchmark comparing Angular 22 and SolidJS under the same representative DeFi-style user-interface workload.

Built by Edward Siwek.

## Purpose

This project exists to answer one engineering question:

> How do Angular 22 and SolidJS behave under the same high-frequency financial-interface workload?

The goal is to measure rather than assume.

The benchmark will compare both frameworks using equivalent application behaviour, equivalent data, equivalent update patterns, and the same measurement environment.

This repository is not intended to prove that one framework is universally better than another.

It is intended to produce transparent, reproducible evidence about their behaviour under a specific workload.

## Initial Benchmark Scenario

The benchmark will eventually simulate a DeFi market interface containing approximately 100–500 market rows.

Representative market data may include:

- asset symbol;
- asset price;
- supply APY;
- borrow APY;
- available liquidity;
- market utilisation.

Representative portfolio data may include:

- wallet balance;
- supplied amount;
- borrowed amount;
- collateral value;
- health factor.

The workload will include repeated updates to selected values so that rendering and reactive-update behaviour can be observed under load.

## Planned Measurements

The benchmark is expected to examine metrics such as:

- production JavaScript size;
- initial rendering behaviour;
- memory consumption;
- CPU utilisation;
- long tasks;
- DOM update behaviour;
- interaction responsiveness;
- network behaviour where relevant.

The exact benchmark methodology will be defined before framework-specific implementation begins.

## Fair-Comparison Principles

The Angular 22 and SolidJS implementations must use equivalent:

- benchmark data;
- number of rendered records;
- update frequencies;
- update sequences;
- user interactions;
- production-build conditions;
- browser environment;
- test hardware;
- measurement procedures.

Framework-specific optimisations may be evaluated later, but the baseline comparison must first remain structurally equivalent.

## Initial Non-Goals

The initial benchmark will not include:

- blockchain connectivity;
- wallet integration;
- Ethereum RPC calls;
- smart contracts;
- backend services;
- real market data;
- authentication;
- charting libraries;
- component libraries;
- CSS-in-JS;
- runtime styling frameworks.

These concerns would introduce additional variables before the frontend-framework baseline is understood.

## Engineering Principles

Development follows these principles:

- keep dependencies minimal and justify every addition;
- introduce abstractions only after a concrete requirement is proven;
- keep Git commits small, atomic, and reviewable;
- prefer explicit ownership, clear boundaries, and descriptive names;
- treat security and simplicity as complementary engineering goals;
- inspect generated framework output before adopting or modifying it;
- keep generated build artifacts out of source control;
- pin tools and direct dependency versions exactly where practical;
- prefer static, native CSS over runtime styling dependencies;
- measure performance rather than relying on framework reputation or assumptions.

Every directory, abstraction, dependency, and configuration layer should have a demonstrated reason to exist.

## Development Approach

The project will be built incrementally.

Each meaningful change should:

1. solve one concrete problem;
2. explain why the change is required;
3. remain independently reviewable;
4. include appropriate validation or testing;
5. be reviewed before the next change begins.

Generated framework applications will be inspected before their output is accepted into the repository.

## Current Status

Project initialization.

No Angular or SolidJS application has been generated yet.

The benchmark methodology and workload contract will be defined before implementation begins.
