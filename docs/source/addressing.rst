.. # Copyright (c) 2015 Tigera, Inc. All rights reserved.
   #
   #    Licensed under the Apache License, Version 2.0 (the "License"); you may
   #    not use this file except in compliance with the License. You may obtain
   #    a copy of the License at
   #
   #         http://www.apache.org/licenses/LICENSE-2.0
   #
   #    Unless required by applicable law or agreed to in writing, software
   #    distributed under the License is distributed on an "AS IS" BASIS,
   #    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or
   #    implied. See the License for the specific language governing
   #    permissions and limitations under the License.

Addressing and Connectivity Overview
====================================

This diagram shows elements of a Calico network in a simplified way that allows
us to focus on how IP addresses are assigned to workloads, and whether and how
workloads have connectivity to and from the Internet (as well as to other
workloads within the data center).

.. figure:: _static/calico-connectivity.png
   :alt: Calico addressing and external connectivity

Network provisioning and IP addresses
-------------------------------------

In current Calico, the ranges from which IP addresses may be assigned to
workloads are all provisioned by the data center operator.  These ranges are
all shared, in the sense that IP addresses from them might be allocated to
workloads for different tenants, and the overall IPv4 and IPv6 address spaces
are 'flat', in that any IPv4 address can in principle - meaning subject to
security policy - communicate with any other IPv4 address, and similarly for
IPv6.

.. note:: A future Calico release will add private address spaces with IPv4
          address ranges that can be provisioned by tenants, and potentially
          overlap with each other and with the shared IPv4 address space.  See
          :doc:`overlap-ips` for our thoughts on how that will work.  (Private
          networks do not need IPv6 address ranges, because the shared IPv6
          address space is already sufficient for all needs.)

Within the shared address spaces there may be ranges of IP addresses (both v4
and v6) that are routable from outside the data center.  Other shared network
IP address ranges will not be routable from outside: they are
potentially accessible from all other workloads within the data center, but not
from the Internet.  This is all under the control of the data center operators,
as it is they who provision the shared network.

Outbound and inbound connectivity to and from the Internet
----------------------------------------------------------

Subject to security configuration, *all* forms of IP addressing can initiate
*outbound* connections to outside the data center.  In the IPv4 case, the
border gateway must NAT the source address of outbound packets, so that
responses to them are routed back to the data center.

In the diagram above, 10.65/16 and 2001:db8:a41:2/64 are IPv4 and IPv6 subnets
that are not routable from outside the data center, and 102.25.78/24 and
2001:cf2:45:1/64 are subnets that *are* routable from outside.  For a workload
to be accessible from outside the data center, it simply needs one of its vNICs
to be given an IP address from one of the externally routable ranges.

The following table summarizes the properties of the IP addresses in the
diagram.

+--------------------+-------------------------+----------------------+
|         IP address | Routable from Internet? | Can access Internet? |
+====================+=========================+======================+
|         10.65.0.18 | No                      | Yes                  |
+--------------------+-------------------------+----------------------+
| 2001:db8:a41:2::12 | No                      | Yes                  |
+--------------------+-------------------------+----------------------+
|        102.25.78.2 | Yes                     | Yes                  |
+--------------------+-------------------------+----------------------+
|  2001:cf2:45:1::3a | Yes                     | Yes                  |
+--------------------+-------------------------+----------------------+
