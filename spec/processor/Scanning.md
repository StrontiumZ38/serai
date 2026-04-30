# Scanning

Only blocks with finality, either actual or sufficiently probabilistic, are
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
