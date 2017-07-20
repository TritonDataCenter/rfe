---
authors: Dan McDonald <danmcd@joyent.com>
state: predraft
discussion: https://github.com/joyent/rfe/issues?q=%22RFE+1%22
---

<!--
    This Source Code Form is subject to the terms of the Mozilla Public
    License, v. 2.0. If a copy of the MPL was not distributed with this
    file, You can obtain one at http://mozilla.org/MPL/2.0/.
-->

<!--
    Copyright 2017 Joyent, Inc.
-->

# RFE 1 The Router Network Object for Triton


## Related Work

* IKEv2


## Introduction

On the JPC today, we have Fabric Networks <link> and traditional VLANs
<link>.  Customers would like to be able to join these at the IP level.  The
traditional means for doing this is the router.  This RFE will describe the
Router network object for Triton.


## The Problem(s)

Many problems fall out of this situation.  Among them

* The abstraction layer at which to solve this is likely an API of which I
  have no experience.  :-/

* We wish to join two or more Fabric Networks together.  Maybe even two or
  more traditional VLANs.

* Ideally we'd like to have redundant links between the two.

* How much of this joining is behind-the-scenes vs. API-visible?

* Do we wish to have a Router object that allows NAT-ing to the Internet?


## What We Want

First, a simple Router object that would join two links, either Fabric, VLAN,
or even both.  They can/should have distinct IP prefixes from each other (see
one of the Fringe Ideas for a potential solution, though).  Specifying the
UUID (and type?) of each link object should be sufficient for the front end.

Behind the scenes, there are these connectivity possibilities with which to
contend:

### Intra-Data-Center routing.

This should be relatively easy, and may not even involve any tunneling.

### Inter-Data-Center routing.

This will require more thought, including the requirement for tunneling (so
one does not need dedicated inter-DC links).  A goal should be to reduce the
number of public IP addresses required to construct these tunnels.  Clever
use of NAT, clever use of IP-in-IP tunnels, or both, should help.

### On-Premise Triton to DC routing.

Once the inter-DC problems are solved, most, if not all, of them can be
generalized to on-premise Triton to a JPC location.

### Triton to other-cloud routing.

Both JPC customers and Triton on-premise customers may wish to bring other
clouds to bear.  Enabling routing between a Triton network and another cloud
will provide many advantages, including getting a toehold in places otherwise
reluctant to adopt JPC or Triton.


## Fringe ideas.

### NAT to resolve same-prefix conflicts

Consider two networks that both use prefix 10.1.1.0/24.  If someone wishes to
join them purely at the IP leve, they are out of luck.  A clever use of NAT,
however, where one side's 10.1.1.0/24 becomes another side's 10.2.2.0/24 may
be a useful tool to have in the Triton Router object.

