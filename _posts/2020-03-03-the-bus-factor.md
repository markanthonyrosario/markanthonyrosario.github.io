---
layout: post
title: The Bus Factor
categories: [Productivity]
tags: [productivity]
description: tldr; if a key person gets hit buy a bus, can operations continue?
---

from [wikipedia](https://en.wikipedia.org/wiki/Bus_factor)

> The bus factor is a measurement of the risk resulting from information and capabilities not being shared among team members, derived from the phrase "in case they get hit by a bus." It is also known as the bread truck scenario, lottery factor, truck factor,[1] bus/truck number, or lorry factor.

> The concept is similar to the much older idea of key person risk, but considers the consequences of losing key technical experts, versus financial or managerial executives (who are theoretically replaceable at an insurable cost). Personnel must be both key and irreplaceable to contribute to the bus factor; losing a replaceable or non-key person would not result in a bus-factor effect.


tldr; if a key person is hit by a bus, can operations continue?

**Sample Scenario #1**
there one key person doing a role without backups, 
this results in a bus factor of 1, with a fault tolerance of 0
if that  person gets hit by a bus, there is 0 recovery, operations is affected

**Sample Scenario #2**
there are two key people doing a role with absolute information,
this results in a bus factor of 2, with a fault tolerance of 1
we can get by if 1 gets hit by a bus, 
operations run as usual

as an team,department, organization-in-general we should be aware of this concept,and actively discuss how to increase it.
