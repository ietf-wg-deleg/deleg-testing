This page holds lists of software and services that have at least provisional support for DELEG.
It also has pointers to testing resources.

# FOSS software

## Knot

The `knotd` authoritative server and `kdig` DNS client in this
[experimental branch](https://gitlab.nic.cz/knot/knot-dns/-/tree/deleg_experimental)
are DELEG-enabled.
Last checked 2026-05-05.

## BIND9

The `named` authoritative server and resolver, and `dig` DNS client in this
[experimental branch](https://gitlab.isc.org/isc-projects/bind9/-/tree/each-deleg-auth)
are DELEG-enabled.
Last checked 2026-05-05.

## dnspython

The dnspython library has DELEG support in this
[experimental branch](https://github.com/rthalley/dnspython/tree/deleg).
Last checked 2026-05-05.

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

## Knot and BIND9

A set of tests running against the `knotd` and `named` authoritative servers is available under "test-knot.deleg.top" and "test-bind.deleg.top", respectively.
It has been verified with Akamai's DELEG-enabled test resolver and a forthcoming DELEG-enabled resolver from ICANN.
The software that created this testbed is available from the ICANN [deleg-testbed-setup repo](https://github.com/icann/deleg-testbed-setup)

If you have a DELEG-enabled resolver, you can try each of the following, looking for the expected value in the answer in the "zones-data.txt" file in the repo.

For example, sending a query for TXT records ns-and-server-name.test-knot.deleg.top with a current resolver will give you:

    ns-and-server-name.test-knot.deleg.top. TXT 30 "43002"
    ns-and-server-name.test-knot.deleg.top. TXT 30 "Not DELEG-enabled for ns-and-server-name.test-knot.deleg.top, from ns1"

The same query with a DELEG-enabled resolver will give you:

    ns-and-server-name.test-knot.deleg.top. TXT 30 "93120"
    ns-and-server-name.test-knot.deleg.top. TXT 30 "DELEG-enabled for ns-and-server-name.test-knot.deleg.top gotten by server-name, from ns2"

The current set of tests are:
~~~
txt-test.test-knot.deleg.top txt
just-ns.test-knot.deleg.top txt +de
ns-and-server-name.test-knot.deleg.top txt
ns-and-server-name.test-knot.deleg.top txt +de
ns-and-server-ipv4.test-knot.deleg.top txt
ns-and-server-ipv4.test-knot.deleg.top txt +de
ns-and-include-delegparam.test-knot.deleg.top txt
ns-and-include-delegparam.test-knot.deleg.top txt +de
txt-test.test-bind.deleg.top txt
just-ns.test-bind.deleg.top txt +de
ns-and-server-name.test-bind.deleg.top txt
ns-and-server-name.test-bind.deleg.top txt +de
ns-and-server-ipv4.test-bind.deleg.top txt
ns-and-server-ipv4.test-bind.deleg.top txt +de
ns-and-include-delegparam.test-bind.deleg.top txt
ns-and-include-delegparam.test-bind.deleg.top txt +de
~~~

## CZ.NIC

Similarly to "zone[1-8].deleg.rwdns.de.", CZ.NIC provides bunch of testing zones
delegated various ways with or without DELEG, NS and DS records. The zones are
powered by DELEG-aware authoritative servers (Knot DNS built from the development
branch):

    ns.d.xdp.cz.
    ns-ds.d.xdp.cz.
    deleg.d.xdp.cz.
    deleg-ds.d.xdp.cz.
    deleg-ns-ds.d.xdp.cz.

The tricky part is that some nameservers (`dns1.d.xdp.cz.`) serve only the parent
zone (`d.xdp.cz.`), some (`dns3.d.xdp.cz.`) only the subzones (listed above), and
some (`dns4.d.xdp.cz.`) all of them at once (demonstrating the specific behavior
of DELEG parent-side-ness).
