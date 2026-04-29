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
  group: "Operationalizing Network group: ONSENamp; SErvice abstractioNs"
  type: "Working Group"
  mail: "onsen@ietf.org"
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

# Terminology

   {::boilerplate bcp14-tagged}

   The terminology for describing YANG modules is defined in
   {{!RFC7950}}.
   The meaning of the symbols in the tree diagrams is defined in
   {{?RFC8340}}.

   In addition to the terms defined in {{!RFC8519}}, this document makes use of the following term:all capitals, as shown here.

## Acronyms and Abbreviations

   The following acronyms and abbreviations are used in this document


# Operational Considerations

   This revision of the L3NM module introduces two categories of
   changes that operators and implementers need to consider when
   upgrading from {{?RFC9182}}:

   * **Backward-compatible additions**, which can be deployed without
     affecting existing clients.
   * **Non-backward-compatible changes**, which require coordinated
     updates between L3NM clients and servers.

## Backward-Compatible Additions

The new groupings introduced in this document
<check here -- list the relevant sections, e.g., "(Sections 2, 4,
and 5)"> are non-mandatory: all new containers and leaves are
optional and, where applicable, carry default values consistent
with the referenced standards.

Existing L3NM clients that do not populate the new nodes continue
to operate unchanged against a server implementing this revision.
Servers MAY apply default behavior (e.g., a predefined BFD
template) when the optional containers are absent, preserving the
semantics of {{?RFC9182}}. Operators can therefore upgrade the
server side independently of client updates and adopt the new
capabilities incrementally on a per-service basis.

## Non-Backward-Compatible Changes

   The change of `import-policy` (and, where applicable,
   `export-policy`) from a `leaf` to a `leaf-list` is **not**
   backward compatible in the strict YANG sense ({{?RFC7950}},
   Section 11). The cardinality of the node is altered, and the
   encoded form of the data changes from a single value to a list.

   Operationally, this means:

   * Existing L3NM clients that encode `import-policy` as a single
     string MUST be updated to encode it as a list, even when only
     one policy is configured.
   * Servers implementing this revision will not accept the
     single-value encoding produced by clients conformant with
     {{?RFC9182}}.
   * Mixed-version deployments (e.g., a {{?RFC9182}} client paired
     with a server implementing this revision) will fail validation
     on this node until the client is updated.

   This change is retained in the revision for the following reasons:

   * The single-leaf form is too restrictive to represent common
     operator intent, where multiple ordered policies are applied to
     a single VPN network access.
   * The migration path is mechanical: existing single-value
     configurations can be expressed as a one-element list without
     semantic loss.
   * No information is lost, and no existing semantics are
     redefined.

## Migration Guidance

   Operators upgrading from an L3NM deployment based on
   {{?RFC9182}} SHOULD plan the migration in two stages:

   1. **Server upgrade.** Deploy servers implementing this revision.
      Existing clients continue to operate against the new server
      for all backward-compatible nodes (Section N.1).
   2. **Client update for non-backward-compatible nodes.** Update
      client encodings for the affected nodes (Section N.2) before
      provisioning new services that rely on the updated cardinality
      or, in the case of `import-policy`, before any reconfiguration
      of the affected nodes on existing services.

   <check here -- confirm whether you want to include guidance for a
   transition period during which both encodings are tolerated. Some
   bis revisions include a `deviation` or recommend that servers
   accept both forms during migration; others enforce a hard cutover.
   Worth aligning with the WG.>

   Implementers SHOULD document the version of the L3NM module they
   support and clearly indicate whether their implementation is
   compatible with {{?RFC9182}}, this revision, or both.

# Security Considerations

TODO Security

# IANA Considerations

This document has no IANA actions.

# Overall Structure of the Enhanced L3NM Module

## BFD parameterization of static routes

   The L3NM YANG data model {{?RFC9182}} allows static routes to be
   managed within a VPN service. For a particular VPN service, IPv4
   and IPv6 static routes can be added, modified, or deleted, and the
   data model allows the operator to indicate whether BFD is desired
   for a given static route. However, the current model only signals
   the *intent* to enable BFD; it does not allow the operator to
   parameterize the BFD session itself.

   In practice, when a controller derives the device configuration
   from such a static route, it must select a BFD configuration --
   typically from a predefined template. Operators have requested the
   ability to customize the main BFD parameters per service so that,
   for example, faster failure detection can be applied to critical
   services without affecting the default behavior of others.

   This document extends the L3NM with a new grouping that allows the
   intended BFD parameters to be expressed directly in the IPv4 and
   IPv6 static route configuration of a VPN network access.

### New Grouping: `bfd-static-routes`

   A new grouping, `bfd-static-routes`, is defined in the
   `ietf-l3vpn-ntw` module. It carries the intended BFD configuration
   to be applied when BFD is enabled for a static route:


   The semantics of each leaf follow {{?RFC5880}}:

   * `bfd-session-name`: an operator-assigned identifier used to
     reference and track the BFD session associated with the static
     route.
   * `desired-min-tx-interval`: the minimum interval, in
     milliseconds, between transmissions of BFD Control packets, as
     desired by the local system.
   * `required-min-rx-interval`: the minimum interval, in
     milliseconds, between received BFD Control packets that the PE
     is required to support.
   * `local-multiplier`: the detection time multiplier, used by the
     remote system to compute the detection time for the session.

   The `bfd-static-routes` grouping is edfined and used within the IPv4 and
   IPv6 LAN configuration under the VPN network access in the L3NM,
   so that BFD parameters can be specified per static route.

## Multiple Routing Policies per VPN Network Access

   The L3NM YANG data model {{?RFC9182}} allows an operator to associate
   a routing policy with a VPN network access through the
   `import-policy` and `export-policy` leaves. Each leaf is currently
   defined as a single `string`, which restricts the operator to
   referencing exactly one policy per direction.

   In practice, network devices commonly support attaching multiple
   routing policies to a service, evaluated in the order in which they
   are listed. Modeling this as a single leaf forces operators to
   either collapse multiple intents into a single composite policy or
   maintain external orchestration to handle the per-service ordering
   of policies. Both approaches reduce flexibility and increase the
   risk of configuration drift between the service model and the
   device.

   This revision of the L3NM module changes the affected nodes from
   `leaf` to `leaf-list`, allowing multiple routing policies to be
   referenced per VPN network access while preserving evaluation
   order.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
