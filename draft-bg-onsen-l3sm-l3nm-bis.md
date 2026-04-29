---
title: "A YANG Network Data Model for Layer 3 VPNs"
abbrev: "ietf-l3vpn-ntw-bis"
category: info

docname: draft-bg-onsen-l3sm-l3nm-bis-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operationalizing Network workgroup: WG Working Groupamp; SErvice abstractioNs"
keyword:
 - VPNs
 - Network Connectivity
 - Service Provision
venue:
  group: "Operationalizing Network   group: ONSENamp; SErvice abstractioNs"
  type: "Working Group"
  mail: "onions@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/onions/"
  github: "sbarguil/LxNM-Updates"
  latest: "https://sbarguil.github.io/LxNM-Updates/draft-bg-onsen-l3sm-l3nm-bis.html"

author:
 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: oscar.gonzalezdedios@telefonica.com
 -
    fullname: Samier Barguil
    organization: Nokia
    email: samier.barguil_giraldo@nokia.com

normative:

informative:


--- abstract

   Several years have passed since the publication of the L3NM YANG data
   model, which has since been widely adopted in the industry.
   Implementations in network controllers have revealed gaps in {
   {!RFC9182}}. This document updates the YANG data models to address
   those gaps.

--- middle

# Introduction

   The Layer 3 Network Model (L3NM) {{?RFC9182}} defines a network model
   that can be used for communication between customers and service
   providers to orchestrate the lifecycle of L3VPN services. The model
   focuses on describing the network view of Virtual Private Network
   (VPN) services and provides an abstracted view of the requested
   services.

   Since the publication of the YANG model, it has been adopted by
   several service providers to automate the deployment of VPN services.
   Based on the feedback received from these deployments, the model
   requires updates to enhance the functionalities offered by the
   network YANG data models. The issues reported and covered in this
   document are the following:

   * **BFD parameterization of static routes:**
     + The L3NM YANG data model allows static routes to be managed
       within a VPN. That is, for a particular VPN service, new IPv4
       and IPv6 static routes can be added, modified, or deleted. The
       data model allows the operator to specify whether BFD is desired
       for a static route. When a controller derives the device
       configuration of the static route, it must select a particular
       BFD configuration, typically from a predefined template.
       Operators have requested the ability to customize the main BFD
       parameters per service — for example, to allow faster detection
       for critical services. The new requirement is the ability to
       specify the intended BFD configuration in IPv4 and IPv6 static
       routes, including `required-min-rx-interval` and `multiplier`.
       <check here — confirm whether `desired-min-tx-interval` should
       also be included, as it is typically paired with
       `required-min-rx-interval` in BFD configurations>

   * **Management of VLAN 0 in tagged interfaces:** The LxNM YANG
     models define a range for `cvlan` between 1 and 4094. VLAN 0
     should also be supported, as it is used in operational
     deployments. <check here — consider citing IEEE 802.1Q to ground
     the VLAN 0 use case>

   * **Missing BGP intended configuration blocks (related to
     Attachment Circuits):**
     + There are several BGP configuration blocks required to manage
       BGP-based services that are currently present in the AC model
       but not in L3NM:
       - BGP peer group creation
       - BGP redistribution rules
     + A pointer to an ACL for attaching a forwarding filter is also
       missing. <check here — confirm whether this belongs as a
       sub-bullet of BGP configuration blocks or as a separate bullet,
       since ACLs are not strictly BGP-related>

   * **SRv6 support for L3VPN:** SRv6-based BGP services, including
     L3VPN, whose procedures are defined in {{?RFC9252}}. <check here
     — sentence is incomplete; suggest something like "...require
     corresponding L3NM extensions to support their configuration.">

   * **Improving multicast support:**
     + For L3VPN with multicast, an implementation has reported that
       vendor-specific augmentations were added to include various
       profiles (e.g., I-PMSI and S-PMSI). As of today, there is no
       IETF YANG module that supports full MVPN, S-PMSI, and I-PMSI
       under L3NM directly. Standardized profiles need to be added.
       <check here — original mentioned "Cisco MVPN augmentation" by
       name; IETF drafts generally avoid naming specific vendors, so I
       genericized it. Restore if you have a specific reason to cite
       the vendor.>

   * **Inter-AS VPN guidance:** Extend guidance on how the network
     models can be used to operationalize Inter-AS VPN options (A, B,
     and C, as defined in {{?RFC4364}}) within the L3NM framework.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Operational Considerations

TBC per {{?I-D.opsarea-rfc5706bis}}.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
