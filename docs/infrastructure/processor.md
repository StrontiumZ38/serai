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

The processor is a service which has an instance spawned per network. The processor performs 
several important tasks with regards to the external network. Each of them are documented in
the following sections.

## Key Generation

Threshold key generation allows validators to collectively derive threshold keys for both internal Serai operations
and interaction with external networks while avoiding any participant holding the complete private key. This 
multisig-style architecture enables secure distributed signing, validator rotation, and custody of external-network coins
on the Serai network.

This library implements a custom eVRF-based Distributed Key Generation protocol (DKG) for the Serai
protocol. The eVRF-based DKG replaced FROST's PedPoP during Serai's development to better meet 
the following objectives:

1) Allow for threshold participation rather than requiring all participants be online to perform key generation
2) Allow for one-round messaging (as opposed to PedPoP's two-round) to mitigate the risk of a protocol
   stop in the case of participant inactivity

Two invocations of DKG are performed, one for Ristretto
(to have a key to oraclize values onto the Serai blockchain with) and one for
the external network's curve.

This library is interacted with via the `serai_processor_messages::key_gen` API.

The validator group collectively generates a shared key in which no single validator knows the 
full private key. This results in `ThresholdKeys<Ristretto>` for interaction with the  Serai blockchain
and `ThresholdKeys<N::Curve>` for interaction with external chains.

To achieve this, participating validators first initiate commitment generation via `CoordinatorMessage::GenerateKey`.
The parameters of this key generation are defined by `ThresholdParams`, which defines how many
validators are involved, how many are required, and identifies the individual validator by index.
These parameters must be consistent across participants, otherwise key generation will fail.

During key generation, each validator generates a random polynomial and commitments to its coefficients, 
publishing them as elliptic curve points.

These commitments are exchanged following `CoordinatorMessage::Commitments`.
Each participating validator receives the commitments from all other validators and generates encrypted 
secret shares for each.

Finally, through `CoordinatorMessage::Shares`, each participating validator receives all shares and verifies them 
against commitments. Once verification passes, it combines them into its final key share. This ensures 
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

Batch signing allows validators to collectively authorize `Batch` publication onto the Serai 
blockchain. These `Batch`s contain indexed external-network activity observed and processed
by the Serai processors.

### External Network Block

When the external network has a new block, which is considered finalized
(either due to being literally finalized or due to having a sufficient amount
of confirmations), it's scanned.

Outputs to the key of Serai's multisig are saved to the database. Outputs that
newly transfer into Serai are used to build `Batch`s for the block. The
processor then begins a threshold signature protocol with its key pair's
Ristretto key to sign the `Batch`s.

The `Batch`s are each sent to the coordinator in a
`substrate::ProcessorMessage::Batch`, enabling the coordinator to know what
`Batch`s *should* be published to Serai. After each
`substrate::ProcessorMessage::Batch`, the preprocess for the first instance of
its signing protocol is sent to the coordinator in a
`coordinator::ProcessorMessage::BatchPreprocess`.

### Batch Preprocesses

On `coordinator::CoordinatorMessage::BatchPreprocesses`, the processor
continues the specified batch signing protocol, sending
`coordinator::ProcessorMessage::BatchShare` to the coordinator.

### Batch Shares

On `coordinator::CoordinatorMessage::BatchShares`, the processor
completes the specified batch signing protocol. If successful, the processor
stops signing for this batch and sends
`substrate::ProcessorMessage::SignedBatch` to the coordinator.

### Batch Re-attempt

On `coordinator::CoordinatorMessage::BatchReattempt`, the processor will create
a new instance of the batch signing protocol. The new protocol's preprocess is
sent to the coordinator in a `coordinator::ProcessorMessage::BatchPreprocess`.

## Planning Transactions

Transaction planning determines which external-network transactions must be created
in response to newly finalized outputs and `Burn` events on Serai. This process
schedules the required payments and prepares them for threshold signing.

### Substrate Block

On `substrate::CoordinatorMessage::SubstrateBlock`, the processor:

1) Marks all blocks, up to the external block now considered finalized by
   Serai, as having had their batches signed.
2) Adds the new outputs from newly finalized blocks to the scheduler, along
   with the necessary payments from `Burn` events on Serai.
3) Sends a `substrate::ProcessorMessage::SubstrateBlockAck`, containing the IDs
   of all plans now being signed for, to the coordinator.
4) Sends `sign::ProcessorMessage::Preprocess` for each plan now being signed
   for.

## Cosigning

Cosigning is the threshold-signing process through which validators collectively 
authorize and publish external-network transactions. This process ensures no individual
validator can independently produce valid transaction signatures.

### Sign Preprocesses

On `sign::CoordinatorMessage::Preprocesses`, the processor continues the
specified transaction signing protocol, sending `sign::ProcessorMessage::Share`
to the coordinator.

### Sign Shares

On `sign::CoordinatorMessage::Shares`, the processor completes the specified
transaction signing protocol. If successful, the processor stops signing for
this transaction and publishes the signed transaction. Then
`sign::ProcessorMessage::Completed` is sent to the coordinator to be
broadcasted to all validators so everyone can observe that the signing attempt 
completed, producing a signed and published transaction.

### Sign Re-attempt

On `sign::CoordinatorMessage::Reattempt`, the processor will create a new
a new instance of the transaction signing protocol if it hasn't already
completed/observed completion of an instance of the signing protocol. The new
protocol's preprocess is sent to the coordinator in a
`sign::ProcessorMessage::Preprocess`.

### Sign Completed

On `sign::CoordinatorMessage::Completed`, the processor verifies the included
transaction hash actually refers to an accepted transaction that completes the
plan it was supposed to. If so, the processor stops locally signing for the
transaction and emits `sign::ProcessorMessage::Completed` if it hasn't previously.
