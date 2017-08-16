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

* Fabric Networks


## Introduction

On the JPC today, we have Joyent-SDC networks, Fabric Networks <link>, and
VLANs over either of the aforementioned <link>.  Customers would like to be
able to join at least some of these at the IP level.  The traditional means
for doing this is the router.  This RFE will describe the Router network
object for Triton.


## The Problems

Many problems fall out of this situation.  Among them

* We wish to join two or more Fabric Networks together.  Maybe even two or
  more traditional VLANs.

* Ideally we'd like to have redundant links between the two.

* How much of this joining is behind-the-scenes vs. API-visible?

* Fabric networks and VLANs allow specification of a default route.  Do we
  overload this to mean "router object address"?  Or do we introduce a new
  concept to both VLANs and fabric networks?

## What We Have

* Today, behind the scenes, if a fabric network needs to reach the Internet,
  underlying code in VMAPI establishes, or joins, a behind-the-scenes NAT
  zone.  See here:  https://github.com/joyent/sdc-vmapi/blob/master/lib/workflows/fabric-common.js#L201

* We also have a well-known JPC private network, but that may not be
  something we wish to allow on a router object. (At least not immediately.)


## What We Want

A simple Router object that would join two links, either Fabric, VLAN,
or even both.  They can/should have distinct IP prefixes from each other (see
one of the Future and Even Fringe Ideas for a potential solution, though).
Specifying the UUID (and type?) of each link object should be sufficient for
the front end.

Using the examples of
https://github.com/joyent/sdc-vmapi/blob/master/docs/index.md#specifying-networks-for-a-vm,
perhaps something like:

    [
      { "router_uuid": "<UUID>" },
      { "joined_networks",
        "<network1-UUID>", "<network2-UUID>",.... },
    ]

### Parameters for connectivity scenarios

There are four parameters for connecting different networks.  Some of them
are binary choices, some are not.

#### Intra-DC vs. Inter-DC

Networks can be located either on the same Triton Data Center, or on distinct
ones.  Other factors (see below) may come into play as well, but
inter-data-center traffic will require additional protections.  Networks on
the same DC may not need additional protections, but could benefit from them
if defense-in-depth is a concern.  (NOTE:  I'm assuming intra-DC can still
span distinct compute-nodes.)

#### Same Ownership vs. Different Ownership

Distinct Triton owners of distinct networks may, just like in the real world,
wish to connect networks they own to each other.  As with other parameters,
this one does not stand by itself.  The implementation details may widely
differ between, for example, intra-DC/different-owners
vs. inter-DC/different-owners.

None of those matter to a user or API consumer, however.

#### Same UFDS vs. Different UFDS

Beyond the ownership parameter, where two distinct owners likely are in the
same UFDS, it is also possible that two Triton deployments may have two
distinct UDFSes. For example, an on-site Triton deployment containing its own
UDFS may wish to connect to the JPC, or some other Triton-backed cloud.

#### Network Type: JPC, AWS, Generic

This parameter becomes relevant when Inter-DC comes into play, but is beyond
the knowledge of Triton at both ends.  A peer network may not be in a Triton
cloud at all.  It could be another cloud, such as AWS's VPC.  It could be a
single-remote-node (not unlike a secure remote-access client).  It could also
be a generic OS installation that requires some amount of configuration,
which could be provided by Triton.


===================== (Cut up to and including here.) =====================
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
===================== (Cut up to and including here.) =====================

### Triton to other-cloud routing.

Both JPC customers and Triton on-premise customers may wish to bring other
clouds to bear.  Enabling routing between a Triton network and another cloud
will provide many advantages, including getting a toehold in places otherwise
reluctant to adopt JPC or Triton.

Additionally, a router object MAY allow fabric network traffic it routes to
escape to the Internet, like a home NAT box would.  This would enable more
Triton instances to access external sources for software updates, and other
Internet services.


## Customer Expectations

Anything we produce needs to make sure it does not unpleasantly surprise the
customer or disappoint them.

* High Assurance: HA is a vague term, but we should assume it includes
  concepts like No Single Point of Failure.

* Performance: Today, NAT zones are regarded as "zero-cost" additions to a
  compute node AND to a packet's path.  A functionally richer router zone
  may introduce measurable cost either to the compute node's CPU (encrypting
  or running routing algorithms) or to network traffic (higher latency and/or
  lower bandwidth).  Some of these costs are unavoidable, but should be
  measured, and optimized where possible.


## Future and Even Fringe Ideas.

### NAT to resolve same-prefix conflicts

Consider two networks that both use prefix 10.1.1.0/24.  If someone wishes to
join them purely at the IP leve, they are out of luck.  A clever use of NAT,
however, where one side's 10.1.1.0/24 becomes another side's 10.2.2.0/24 may
be a useful tool to have in the Triton Router object.

### Routing between multiple Router objects

One may wish to form an entire network of more than just two LANs.  How would
one do that?  What sort of routing protocol(s) would we need?  Even for a
single Router object, dual-redundancy may mean using a routing protocol
ANYWAY.  Is it easier, harder, or orthogonal when one add routing between
multiple router objects?

### Routing between networks from distinct customers.

Feedback from earlier versions of this included:

	This router object may have some role in our efforts to provide
	services, or to enable Triton as a platform on which others can
	provide services. For that, we may need to route between fabric
	networks owned by different users. I imagine most of that will be
	dependent on RBACv2, but I wanted to raise it for consideration here
	as well.

So naturally depending on RBACv2 (See
https://github.com/joyent/rfd/tree/master/rfd/0048 ), the ability to route
between distinct customers is desirable.  The same-prefix problem mentioned
above might be a useful tool for this problem as well.

