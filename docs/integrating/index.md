---
title: Integrating with Serai
layout: default
nav_order: 7
has_children: true
---
# Integrating With Serai

> Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses may be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

Integration of exogenous chains, beyond those included in Serai's initial launch plan 
(i.e., Bitcoin, Ethereum, DAI Stablecoin, and Monero), must follow the launch version
of Serai, excluding them from genesis.

Code implementation may or may not be directly handled by the Serai developers, and as
a consensus threshold is required for Serai to operate, validators will need to choose to
operationally support any post-launch integration. Factors such as hardware requirements and economic
feasibility are likely factors in determination of whether a specific integration will gain
validator support.

## Code Contribution

External developers may develop their own code implementation supporting their external coin and
submit their contribution via Git for review. If the code is deemed by Serai developers to be 
sufficiently viable, secure, and tested, integration to Serai's core codebase may take place.

Serai's GitHub and source can be found at: [Serai DEX Github](https://github.com/serai-dex/serai).

## Integration Requests

As an alternative to contribution, proposals for integration to be handled or aided by Serai
developers may be proposed to the Serai developers to be accepted or dismissed at their sole 
discretion and under terms of their choosing.

Outside of specific knowledge of the codebase and operational parameters of Serai, support of
new exogenous chain integration falls to validator consensus.

## Validator Consensus

As Serai is open-source and operates by code-governed communal consensus, implementation of new
chains and their coins relies entirely on support by Serai validators.

Even in the case of Serai developers handling the coding of an exogenous coin's integration,
the final say on whether that code (and thus the exogenous coin) is supported falls to the
consensus of validators and not the developers. In this sense, no guarantees can be given toward 
the acceptance and support of an exogenous chain and its coin by any party.

## Factors That Influence Integration Viability

Viability of additional exogenous coins for addition to Serai is influenced by several factors.

### Architecture

Chains running on architecture similar to Serai or its initially supported chains will more
likely find compatibility with existing solutions rather than needing new solutions. This does
not imply that chains with vastly different architecture cannot be integrated, only that more
work will likely be required in their implementation.

### Storage Feasibility

Where exogenous chains are to be supported by validators, storage requirements will need to be
considered. Chains with excessive storage requirements are less likely to find support from
validators, particularly if they're less popular.

Additionally, chains that use mechanisms like pruning may introduce additional challenges.

### Liquidity and Demand

Where insufficient demand and liquidity exists for a coin to feasibly fill swap orders for 
supporting validators, the likelihood of finding validator support falls.

The relationship between resource requirements for validators and access to incentives should
be considered when thinking of seeking integration of an exogenous coin on Serai.
