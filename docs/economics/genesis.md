---
title: Genesis
layout: default
nav_order: 1
parent: Economics
---
# Genesis Era

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses can be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

## Basic Overview

The network starts with the "Genesis" era, where the goal of the network is to
attract the liquidity necessary to facilitate swaps. This period will last for
30 days and will let anyone add liquidity to the protocol. Only with its
conclusion will SRI start being distributed.

After the Genesis era, the network enters the ["Pre-Economic Security" era](/docs/pre.md).

## In-depth Information

At genesis, a set of genesis nodes (presumably sufficiently
trusted community leaders) will start the network. These genesis nodes will perform a DKG and publish the initial
addresses for Serai (over Bitcoin, Ethereum, and Monero) to receive coins with
(BTC, ETH, DAI, and XMR, further referred to with indifference as XYZ).

```
GENESIS_SRI = 100,000,000.000000000 SRI
GENESIS_LIQUIDITY_TIME = 30 days
```

Over `GENESIS_LIQUIDITY_TIME`, any user will be able to provide XYZ. At the end
of `GENESIS_LIQUIDITY_TIME`, the validators will oraclize the value of 1 XYZ
in terms of 0.00000001 BTC (except for BTC). With the value of `sriXYZ`
considered equivalent to the value of `XYZ`, the value of each pool is
thus determined, and `GENESIS_SRI` is proportionately distributed.

With the SRI distributed to the pools, the required amount of SRI for a
validator set to be considered as economically secure can be calculated (as
detailed in following sections). The amount of SRI which must be allocated for
a key share is determined such that for `g` genesis validators,
`(((2 * g) + 1) * allocation_per_key_share) > economic_security_requirement`.
This ensures at least one genesis validator is required to participate in every
signing operation until the network is economically secure. It is potentially
excessive in that liquidity may be added to a pool during genesis, then removed
pre-economic security, without the allocation per key share value decreasing
proportionately, however. This is accepted as an oddity.

Genesis is now complete. Allocating stake and swaps become available.
