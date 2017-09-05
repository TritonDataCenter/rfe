---
authors: Dan McDonald <danmcd@joyent.com>
state: predraft
discussion: https://github.com/joyent/rfe/issues/4
---

<!--
    This Source Code Form is subject to the terms of the Mozilla Public
    License, v. 2.0. If a copy of the MPL was not distributed with this
    file, You can obtain one at http://mozilla.org/MPL/2.0/.
-->

<!--
    Copyright 2017 Joyent, Inc.
-->

# RFE 2 The Remote Network Object.


## Related Work

* RFE 1 - https://github.com/joyent/rfe/rfe/0001/

* IKEv2

* Fabric Networks


## Introduction

Alongside existing Triton network objects, a router object, as described in
RFE 1, may need to connect to network objects outside a Triton Data Center's
(DC's) object space.  These _external networks_ should be reachable via a
Triton Remote Network Object, which can attach to a Router Object, or
possibly even an instance.


## The Problems

There are networks beyond the control of a Triton DC.  The External Network
Object encapsulates such networks.  The one invariant of all types of
external networks is an IP prefix of nodes that can be reached via this
external network.  A Router Object will insure attached IP prefixes are
disjoint, or in the future use tricks to avoid such collisions.

Some networks may also be distinct Triton DCs, which may provide an
opportunity for smaller amounts of manual configuration, so long as some
level of trust existed between the two Triton DCs, sharing the same UFDS, for
example.

Some networks may be other clouds, such as AWS, Azure, or Google Cloud.  Each
has their own heterogenous connectivity solution, and an External Network
Object representing one of these may require more or less manual
configuration than a remote Triton DC peer.

Some networks may be VPN appliances, or some other sort of other network
device, even a remote work-at-home single workstation.  It is likely these
will require manual configuration.


## What We Have

The Router Object is a sufficient prequisite for a Remote Network Object, but
it is strictly not necessary.  Given today a NAT object is created behind the
scenes for Fabric Networks, a Remote Network Object may be able to attach
directly to an instance via a behind the scenes NAT object.


## What We Want

A Remote Network object would instantiate as one of several types (some may
think of them as Templates).  Possible types include: Triton DC, AWS, Google
Cloud, or Manual.  A Remote Network object, regardless of type, would take as
a parameter one or more IP prefixes.

A Triton DC Remote Network Object would have less configuration required,
perhaps merely one or more remote pairs of {IP address, UDP port} that can be
reached by the local Triton instance's external IP.  Or even merely the name
of a Triton DC if it happens to share a same UFDS with the local one.  To
avoid NAT complications, a Triton DC set up to serve out Remote Network
Objects (via its Router Object) should have a public IP with a range of UDP
ports reserved for serving Remote Network Objects.

An AWS, Google Cloud, or some other cloud type would have some amount of
configuration information similar to whatever the other cloud requires.
(MORE.)

A manual Remote Network will require much configuration, based on many
factors.

* Encapsulation Type - Multiple technologies connecting internal networks
  over a potentially hostile external network exist.

** IPsec-protected IP tunneling.

** IP-in-dTLS.

** (MORE)

* Routing protocol information. AWS's VPC, for example, use BGP to insure
  connectivity paired-for-redundancy tunnels.  Such information may be needed
  for a manual Remote Network as well.

* (MORE)


## Customer Expectations

Remote Network objects should feel mostly like conventional Network Objects
such as Fabric Networks.  Only the complications introduced by the peer
should affect the user experience.  A Triton peer (same-UDFS or not) should
be the least complex Remote Network object to configure.

