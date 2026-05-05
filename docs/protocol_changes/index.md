---
title: Protocol Changes
layout: default
nav_order: 5
---

# Protocol Changes

Note that [Notices and Disclaimers](/NOTICES-AND-DISCLAIMERS.md) apply to the following.
While guidance to the most relevant clauses can be found within the provided information,
the entirety of Notices and Disclaimers should be considered applicable. 

The protocol, as written and provided, has no central authority, organization, or actors (such as
liquidity providers/validators) who can compel new protocol rules. The Serai
protocol operates strictly as-written with all granted functionality and declared rules
present. See [Notices and Disclaimers §§2–4](/NOTICES-AND-DISCLAIMERS.md).

Validators are explicitly granted the ability to signal for two actions to occur:

### 1) Halt Another Validator Set

This is expected to occur if another validator set turns malicious and is the
expected incident response in order to apply an economic penalty of ideally
greater value than damage caused. Halting a validator set prevents further
publication of `Batch`s, preventing improper actions on the Serai blockchain,
and preventing validators from unstaking (as unstaking only occurs once future
validator sets have accepted responsibility, and accepting responsibility
requires `Batch` publication). This effectively burns the malicious validators'
stake.

See [Notices and Disclaimers §§2–3, 5](/NOTICES-AND-DISCLAIMERS.md).

### 2) Retire the Protocol

A supermajority of validators may favor a signal (an opaque 32-byte ID) (See [Notices and Disclaimers §6](/NOTICES-AND-DISCLAIMERS.md)). A
common signal gaining sufficient favor will cause the protocol to stop producing
blocks in two weeks.

Nodes are expected to, as individual entities, hard fork to new consensus rules
(See [Notices and Disclaimers §6](/NOTICES-AND-DISCLAIMERS.md)).
These rules presumably will remove the rule to stop producing blocks in two
weeks, they may declare new validators, and they may declare new functionality
entirely. See [Notices and Disclaimers §§ 2-3, 6](/NOTICES-AND-DISCLAIMERS.md).

While nodes individually hard fork, across every hard fork the state of the
various `sriXYZ` coins (such as `sriBTC`, `sriETH`, `sriDAI`, and `sriXMR`)
remains intact (unless the new rules modify such state; see [Notices and Disclaimers §§ 2-3, 6](/NOTICES-AND-DISCLAIMERS.md)).
These coins can still be burned with instructions (unless the new rules prevent that) and if a
validator set doesn't send `XYZ` as expected, they can be halted (effectively
burning their `SRI` stake). Accordingly, every node decides if and how to future
participate, with the abilities and powers they declare themselves to have.
See [Notices and Disclaimers §§ 2-7](/NOTICES-AND-DISCLAIMERS.md).
