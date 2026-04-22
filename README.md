This page holds lists of software and services that have at least provisional support for DELEG.
It also has pointers to testing resources.

# FOSS software

## Knot

The `knotd` authoritative server and `kdig` DNS client in this
[experimental branch](https://gitlab.nic.cz/knot/knot-dns/-/tree/deleg_experimental)
are DELEG-enabled.

Last checked 2026-04-22

## BIND9

The `named` authoritative server and resolver, and `dig` DNS client in this
[experimental branch](https://gitlab.isc.org/isc-projects/bind9/-/tree/each-deleg-auth)
are DELEG-enabled.

Last checked 2026-04-22

## dnspython

The dnspython library has DELEG support in this
[experimental branch](https://github.com/rthalley/dnspython/tree/deleg).

# Proprietary software

## Akamai

An internal build of Akamai's authoritative server and resolver are DELEG-enabled.
See the testing resources below for a method to test them.

# Testing resources

## Akamai

An internal build of Akamai's authoritative server and resolver are DELEG-enabled.
The following are best-effort test setups using that software.

"deleg.rwdns.de." has zones underneath it (neatly named "zone1", "zone2", and so on)
that are delegated with DELEG some to different servers.
Each zone has a TXT record in it.

A DELEG-aware resolver will get a different response than a DELEG-unaware resolver.
For example, zone2.deleg.rwdns.de on a DELEG-unaware resovler gets:

    zone2.deleg.rwdns.de. TXT 3600 "zone2 via NS"

while a DELEG-aware resolver gets:

    zone2.deleg.rwdns.de. TXT 3600 "zone2 via DELEG"

You can use the Akamai DELEG-aware resolver by querying cs8.res.rwdns.de over DoH.
For example:

    dig +dnssec +https=/dns-query @cs8.res.rwdns.de txt zone2.deleg.rwdns.de

The testable zones are shown [here](akamai-testing-layout.png).

Comments on this test setup should be sent to [Ralf Weber](mailto:dns@fl1ger.de).

