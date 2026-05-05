---
title: Pre-Economic Security
layout: default
nav_order: 2
parent: Economics
---
# Pre-Economic Security Era

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses can be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

## Basic Overview

Pre-economic security is the state Serai will be in following its [Genesis Era](/docs/genesis.md). This period is
defined by the protocol having not yet reached a state of economic security.

{: .definition-title }
> Definition: Economic Security
>
> Economic security is derived from it being unprofitable to misbehave.
> This is due to the economic penalty that is expected to occur when the value of misbehavior exceeds the value gained.
> Accordingly, rational actors would behave properly, causing the protocol to
> maintain its integrity.
>
> For Serai specifically, the stake required to produce unintended signatures
> must exceed the value accessible via producing unintended signatures.

With liquidity provided, and swaps enabled, the goal is to have validators stake
sufficiently for economic security to be achieved. This is primarily via
offering freshly minted, staked SRI to would-be validators who decide to swap
external coins for their stake.

## In-depth Information

### Liquidity Providers

Liquidity may not be added to the pools during this era.

Due to Serai's pools premised upon the `x * y = k` formula, for
`M XYZ : N SRI`, the only way for the XYZ portion to decrease is for SRI
exogenous to the pool to be introduced. If SRI is swapped out from the pools,
and swapped back in, it has a neutral effect (slightly in favor to the pool due
to fees) and accordingly isn't considered exogenous.

Exogenous SRI has four possible sources:

1) Circulating SRI

    There will be no emissions of SRI during this era which aren't immediately
    allocated as stake.

2) Removed liquidity

    All liquidity removed during this era will burn the SRI airdropped to it in
    order to form the liquidity position.

3) Removed stake

    Due to the lack of unused capacity in the economic security, SRI cannot be unstaked.
    If any individual network has achieved unused capacity, unstaking is still
    not allowed so long as any remaining network has yet to achieve unused capacity.

5) Intra-pool SRI movement

    For coins sriXYZ, sriABC, the sriXYZ pool may `+sriXYZ, -SRI`. This enables
    `+SRI, -sriABC` in the sriABC pool. To resolve this, each pool tracks
    `+-sriXYZ` and `+-sriSRI` received from such swaps. When an sriXYZ
    liquidity provider removes their liquidity, they do not receive the
    additional sriXYZ in question. When an sriABC liquidity provider removes
    their liquidity, they do receive the SRI in question. This enables them to
    swap it to the sriXYZ and recoup approximate value, barring fees,
    sriABC-sriXYZ price fluctuations, slippage, etc.

Accordingly, exogenous SRI is considered managed, with the intention being for
genesis liquidity providers who remove their liquidity during the pre-economic
security era to receive value at least approximate to the amount of sriXYZ
initially added as liquidity.

### Swap to Staked SRI

At the median quote, any external actor may swap XYZ to SRI outside of the
pools. This SRI would be freshly minted and immediately staked to a validator
within a set for an external network. The XYZ received would be used to form
protocol-owned liquidity, yet removed genesis liquidity will explicitly not
have their XYZ increased in response to this _despite_ the pool's XYZ
increasing (while its SRI remains constant). This prevents an adversary from
providing liquidity at genesis, swapping to staked SRI, before removing their
liquidity to earn staked SRI at a discount proportional to the percentage of
liquidity they provided.

This policy, combined with the lack of emissions and fees to liquidity
providers in the pre-economic security era, leaves the incentive for liquidity
providers as the airdropped SRI.

### Emissions

Emissions only start after genesis.

```
INITIAL_PERIOD = 30 days
INITIAL_PERIOD_REWARDS = INITIAL_PERIOD * (100,000 SRI / 1 day)
INITIAL_PERIOD_REWARD_PER_BLOCK = INITIAL_PERIOD_REWARDS / TARGET_BLOCK_TIME
LITERAL_STAKE_REQUIRED = 1.5 * sri_in_pools()
EXTERNAL_STAKE_BUFFER = 0.2
EXTERNAL_STAKE_REQUIRED = LITERAL_STAKE_REQUIRED * (1 + EXTERNAL_STAKE_BUFFER)
SERAI_VALIDATORS_DESIRED_PERCENTAGE = 0.2
STAKE_DESIRED = EXTERNAL_STAKE_REQUIRED / (1 - SERAI_VALIDATORS_DESIRED_PERCENTAGE)
SERAI_VALIDATORS_STAKE_DESIRED = SERAI_VALIDATORS_DESIRED_PERCENTAGE * STAKE_DESIRED
SECURE_BY = 365 days
```

During the pre-economic security era, the block reward from genesis till the
end of `INITIAL_PERIOD` is fixed to `INITIAL_REWARD`. During this time, the
Serai validators receive exactly
`SERAI_VALIDATORS_DESIRED_PERCENTAGE * INITIAL_REWARD` while the validators for
external networks split the remainder proportional to their distance to economic
security.

After the initial period, the block reward is defined as
`DISTANCE_TO_ECONOMIC_SECURITY / blocks_until(SECURE_BY)`. This intends to
ensure economic security by (approximately) the specified date. While achieving
economic security by minting SRI as emissions is undesirable, the amount so
minted is a function of the (lack of) interest in staking. For the Serai
validator set, which does not have a literal evaluation of
`DISTANCE_TO_ECONOMIC_SECURITY` available, `SERAI_VALIDATORS_STAKE_DESIRED` is
used as the value required to be considered economically secure.
