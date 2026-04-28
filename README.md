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

## Knot

A set of tests running against the `knotd` authoritative server is available under "test-k.deleg.top".
It has been verified with Akamai's DELEG-enabled test resolver and a forthcoming DELEG-enabled resolver from ICANN.
The software that created this testbed is also forthcoming from ICANN. 

If you have a DELEG-enabled resolver, you can try each of the following, looking for the expected value in the answer.

For example, sending a query for TXT records ns-and-server-name.test-k.deleg.top with a current resolver will give you:

    ns-and-server-name.test-k.deleg.top. TXT 30 "43002"
    ns-and-server-name.test-k.deleg.top. TXT 30 "Not DELEG-enabled for ns-and-server-name.test-k.deleg.top, from ns1"

The same query with a DELEG-enabled resolver will give you:

    ns-and-server-name.test-k.deleg.top. TXT 30 "93120"
    ns-and-server-name.test-k.deleg.top. TXT 30 "DELEG-enabled for ns-and-server-name.test-k.deleg.top gotten by server-name, from ns2"

The current set of tests are:
~~~
Found expected result, 17532, for txt-test.test-k.deleg.top txt
Found expected result, 62358, for just-ns.test-k.deleg.top txt +de
Found expected result, 43002, for ns-and-server-name.test-k.deleg.top txt
Found expected result, 93120, for ns-and-server-name.test-k.deleg.top txt +de
Found expected result, 12773, for ns-and-server-ipv4.test-k.deleg.top txt
Found expected result, 53911, for ns-and-server-ipv4.test-k.deleg.top txt +de
Found expected result, 44992, for ns-and-include-delegparam.test-k.deleg.top txt
Found expected result, 04723, for ns-and-include-delegparam.test-k.deleg.top txt +de
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
