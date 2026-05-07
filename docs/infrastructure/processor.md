---
title: Processor
layout: default
nav_order: 2
parent: Infrastructure
---

# Processor

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses may be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

The processor performs several important tasks with regards to the external
network. Each of them are documented in the following sections.

## Key Generation

This library implements the Distributed Key Generation (DKG) for the Serai
protocol. Two invocations of DKG are performed, one for Ristretto
(to have a key to oraclize values onto the Serai blockchain with) and one for
the external network's curve.

This library is interacted with via the `serai_processor_messages::key_gen` API.

The validator group collectively generates a shared key in which no single validator knows the 
full private key. This results in `ThresholdKeys<Ristretto>` for Serai internal consensus
and `ThresholdKeys<N::Curve>` for interaction with external chains.

To achieve this, participating validators first initiate commitment generation via `CoordinatorMessage::GenerateKey`.
The parameters of this key generation are defined by `ThresholdParams`, which defines how many
validators are involved, how many are required, and identifies the individual validator by index.
These parameters must be consistent across participants, otherwise key generation will fail.

During key generation, the validator generates a random polynomial and commitments to its coefficients, 
publishing them as curve points.

These commitments are exchanged following `CoordinatorMessage::Commitments`.
Each participating validator receives the commitments from all other validators and generates encrypted 
secret shares for each.

Finally, through `CoordinatorMessage::Shares`, each participating validator receives all shares and verifies them 
against commitments. When the verification passes, it combines them into its final key share. This ensures 
no single validator has access to the full private key.

## Scanning

Scanning allows the Serai processors to extract relevant data from an external network and report
the indexed data to the coordinator. Only blocks with finality, either actual or sufficiently probabilistic, are
operated upon. This is referred to as a block with `CONFIRMATIONS`
confirmations, the block itself being the first confirmation.

For chains that promise finality on a known schedule, `CONFIRMATIONS` is set to
`1` and each group of finalized blocks is treated as a single block, with the
tail block's hash representing the entire group.

For chains that offer finality on an unknown schedule, `CONFIRMATIONS` is
still set to `1`, yet blocks aren't aggregated into a group: they're handled
individually, though only once finalized. This allows networks that reach
finalization erratically to not have to agree on when finalizations were formed,
only that the blocks contained have a finalized descendant.

This scanner has two distinct roles:

1) Scanning blocks for received outputs contained within them
2) Scanning blocks for the completion of eventualities

While these can be optimized into a single structure, they are written as two
distinct structures (with the associated overhead) for clarity and simplicity
reasons.

Outputs on the external network's block are filtered by target, with Serai scanning for outputs sent to active 
multisig keys relevant to the Serai network. The handling of outputs is dependent on the lifecycle 
stage of the receiving multisig, with some outputs being reported immediately, queued for later reporting, or ignored.

Internal Serai-derived outputs are excluded from reporting (this includes change outputs, branch outputs, 
and forwarding outputs) as they cannot be reliably validated at this stage. Instead, they are handled by later 
Eventuality processing logic, which can determine whether such outputs were created by the protocol.

This separation avoids risks of accepting fake 'change' outputs, the accumulation of worthless outputs, and 
potential for insolvency bugs.

### Conditions Causing a `Batch`

`Batch`s are only created for blocks where achieving ordering is beneficial.
These are:

- Blocks that contain transactions relevant to Serai
- Blocks in which a new multisig activates
- Blocks in which a prior multisig retires

### Waiting for `Batch` Inclusion

Once a `Batch` is created, it is expected to eventually be included on Serai.
If the `Batch` isn't included within `CONFIRMATIONS` blocks of its creation, the
scanner will wait until its inclusion before scanning
`batch_block + CONFIRMATIONS`.

## Signing Batches

## Planning Transactions

## Cosigning
