---
title: Cross-Chain Architecture
layout: default
nav_order: 3
---

# Cross-Chain Architecture

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses can be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable.

Serai is designed with two fundamental tenets in mind regarding cross-chain interactions:

1) Derive external input/output information deterministically
2) Asynchronously perform safely and consistently when interfacing across chains

## Deterministic Validation

Serai approaches external-network coordination deterministically. Rather than validators proposing
specific outputs or transaction inputs for consensus, validators must only agree on finalized
external blocks, from which outputs and transaction inputs can be derived algorithmically.

For more detailed information on Serai's block validation, see [Processor](/INFRASTRUCTURE/PROCESSOR.md).

## Asynchronicity

A challenge with cross-chain interoperability is that interfacing chains do not
share a unified measure of time and progress independently. Block production times
and confirmation requirements make it difficult to properly determine whether a
delayed action has failed or is simply still pending.

Fallback behavior, such as `If A fails, then B`, incorrectly assuming failure of `A` after a set
period may yet result in both `A` and `B` occurring after a delay, opening
the door to unintended outcomes that may present security issues. Asynchronous systems cannot safely assume
delayed actions will never occur.

Serai is designed to achieve deterministic and consistent outcomes despite independently progressing
chains and delayed communication between participants, preventing cases where mutually exclusive
actions can both occur.
