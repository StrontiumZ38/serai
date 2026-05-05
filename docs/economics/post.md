---
title: Post-Economic Security
layout: default
nav_order: 3
parent: Economics
---
# Post-Economic Security Era

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses can be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

## Basic Overview

The post-economic security era is the 'normal' state of operations for Serai. This state, which is only changed to once economic
security is reached, is the final state for the protocol's economics (barring future upgrades to the protocol).

As the post-economic security era represents everything beyond the [Genesis era](/docs/genesis.md) and [Pre-economic security era](/docs/pre.md), it can be expected
that the majority of Serai's operational existence takes place in this state. Rules for the post-economic era for liquidity provision,
the addition of coins, fee burning, reward distribution, and emissions are therefore expected to apply beyond Serai reaching
economic security. 

## In-depth Information

### Liquidity Providers

```
GENESIS_TRICKLE_FEED = 180 days
```

Liquidity may be added as the capacity allows.

When genesis liquidity is removed, the provider receives
`days_since_economic_security().min(GENESIS_TRICKLE_FEED) / GENESIS_TRICKLE_FEED`
of the additional sriXYZ/airdropped SRI, whereas previously the provider would receive
no such rewards.

### Addition of Coins

While liquidity may only be added as per the capacity in the economic security,
this leaves the minting of coins undiscussed. The goal of Serai, in general, is
to always allow minting coins as necessary to perform swaps and ensure the
pools' quotes are consistent (which requires the ability to add and remove
liquidity, interact with external entities, and perform swaps). Unfortunately,
the ability for a malicious validator set to arbitrarily mint sriEXT would
allow them to drain the corresponding liquidity pool of its SRI. This offers a
profit incentive of approximately twice the value of the external coins in the
liquidity pool, even though economic security is calculated based solely on the
value of those coins.

To mitigate this, once a network has achieved economic security, the minting of
_any_ external coins is only allowed so long as the associated validator set is
able to provide security for them _sans additional buffer_. This also means
additional liquidity will be rejected before minting of coins at all is
rejected, allowing swaps to continue to be enabled even when adding liquidity
no longer is.

This does mean, for a validator set whose economic security has low capacity,
floating coins (coins added to the network but outside of a liquidity pool) can
further endanger the economic security. To this end, it's left to the
participants who have added coins to perform their own risk assessment and
remove them per their evaluation.

As a malicious validator set who does arbitrarily mint sriEXT to swap for SRI
would drive the quote down, raising the capacity, the economic security oracle
will only consider the _highest_ observed median price over an extended window
of time.

Distinctly, two side effects can be noted:

- The potential inability to add coins, to swap them to SRI, enacts a
  circuit breaker such that the radical decline in value for a coin may not be
  recognizable on the Serai network.

- An adversary who pays the opportunity cost of adding coins to Serai, and
  bears the associated risk, is able to tie up capacity _without_ performing a
  service such as providing liquidity. This is unfortunate but accepted for the
  time being, given that a network which is unable to fulfill its purpose can be
  retired in favor of a new ruleset, as possible via Signals.

### Emissions

```
BLOCK_REWARD = 20,000,000 SRI / (365 days / TARGET_BLOCK_TIME)
```

`BLOCK_REWARD * SERAI_VALIDATORS_DESIRED_PERCENTAGE` is distributed to the Serai
validator set.

External networks have their proportions decided equivalently to the proportions
of their fees. Once the network's proportion is decided, a proportion between
the pool and the validators is decided.

```
DESIRED_UNUSED_CAPACITY = 0.1
CURRENT_DISTRIBUTION = (used_capacity() / capacity_of_network()).min(1)
DESIRED_DISTRIBUTION = 1 - DESIRED_UNUSED_CAPACITY
```

The rewards are distributed between between validators and the liquidity pools
according to the following ratio.

`DESIRED_UNUSED_CAPACITY : (((1 - CURRENT_DISTRIBUTION) * DESIRED_DISTRIBUTION) / CURRENT_DISTRIBUTION)`

The intent is that as the distribution between usage and capacity skews from
the desired distribution, rewards shift to incentivize accordingly.
