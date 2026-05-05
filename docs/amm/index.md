---
title: Automatic Market Makers
layout: default
nav_order: 2
---

# Automated Market Makers

## Basic Overview

Automated Market Makers (AMMs) are programs that allow users to swap digital coins
and/or tokens through liquidity pools, eliminating the need for traditional counterparties.
Algorithms calculate conversion rates based on internal supply and demand, as determined
by pool liquidity, and may incorporate information provided by price oracles. Serai only uses
oraclization during genesis (covered in more detail in Liquidity). The AMM fills the user's
swap request based on these calculations by drawing from and adding to the relevant
liquidity pools.

## Liquidity

This section provides only basic information on liquidity. For more detailed information, see [Economics](/spec/Economics.md).

### Initial Liquidity Provision

Initially, liquidity on Serai is provided by validators in the `GENESIS_LIQUIDITY_TIME` (30 days).
Address generation for external coin deposits (BTC, ETH, DAI, and XMR) is handled via
DKG performed by genesis nodes. During this period, only external coin liquidity can be contributed to pools
as SRI (Serai's native coin) is exclusively endogenous and yet to exist.

At the conclusion of `GENESIS_LIQUIDITY_TIME`, `GENESIS_SRI` is minted to the amount
of `100,000,000.000000000 SRI`. At this time, oraclization determining the value of
liquidity provided to pools takes place and the genesis SRI is distributed to LPs accordingly.

### Liquidity Provision and Incentive

Beyond the initial genesis period, liquidity pools may be stocked by Liquidity Providers (LPs)
who provide additional liquidity with which swap requests are filled. This process involves LPs
providing validators with an external coin, for example BTC, and validators minting and providing the LP
with sriBTC in return. That sriBTC is designed to be redeemable, at the LP's discretion, for the
initially provided amount of BTC.

To incentivize LPs providing liquidity, once economic security is reached, a 50% portion of fees
charged on swaps (with fees set at 0.3% on BTC, ETH, and DAI, and 1% on XMR) is
reserved for distribution to LPs, with the remainder being burned. This fee burning is intended to
offset inflationary pressure created by the consistent SRI emissions beyond `GENESIS_SRI` minting.

More detailed information on LP rewards across economic security periods can be found in [Economics](/spec/Economics.md).

### Symmetric Liquidity

Serai uses a symmetric liquidity pool with the `xy=k` formula.

Concentrated liquidity would presumably offer less slippage on swaps, and there are
[discussions to evolve to a concentrated liquidity/order book environment](https://github.com/serai-dex/serai/issues/420).
Unfortunately, it effectively requires active management of provided liquidity.
This disenfranchises small liquidity providers who may not have the knowledge
and resources necessary to perform such management. Since Serai is expected to
have a community-bootstrapped start, starting with concentrated liquidity would
accordingly be contradictory.

## Slippage

Slippage is a feature of AMMs in which the amount of a swap, upon execution, deviates from the expected
output. This can occur due to shallow pool depth (low liquidity), and general price
volatility.

It should be noted that, as use of Serai is not limited or otherwise moderated beyond what is
expressly allowable within the bounds of its code, liquidity provision, price volatility,
and general supply and demand factors may vary at any given time.

## Abritrage

Arbitrage is a practice in which discrepencies in values across distinct markets at a given time are capitalized upon for
a profit. While strictly independent from Serai and its governing codebase, this form of market pressure
can be expected to aid in the regulation of supply and demand for external coins within Serai's network.
