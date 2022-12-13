---
title: "A YANG Data Model for Attachment Circuits"
abbrev: "A YANG Model for ACs"
category: std

docname: draft-boro-opsawg-teas-attachment-circuit-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operations and Management Area Working Group"
keyword:
 - Slice Service
 - L3VPN
 - L2VPN

author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
    email: mohamed.boucadair@orange.com

 -
    fullname: Richard Roberts
    organization: Juniper
    email: rroberts@juniper.net

 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: oscar.gonzalezdedios@telefonica.com

 -
    fullname: Samier Barguil Giraldo
    organization: Nokia
    email: samier.barguil_giraldo@nokia.com

normative:

informative:


--- abstract

This document specifies a service YANG data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., Network Slice Service).

--- middle

# Introduction

This document specifies a service YANG data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., Network Slice Service).

TBC

An AC can be bound to a single or multiple SAPs {{!I-D.ietf-opsawg-sap}}.

 The YANG data model in this document conform to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses the following terms:

Network controller:
: Denotes a functional entity responsible for the management of the service provider network.

Service orchestrator:
: Refers to a functional entity that interacts with the customer of a network service. The service orchestrator is typically responsible for the attachment circuits, the Provider Edge (PE) selection, and requesting the activation of the requested service to a network controller.

Service provider network:
: A network that is able to provide network services (e.g., Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., Network Slice Services).


# Sample Uses of the Attachment Circuit Data Models

~~~~
┌───────┐                      ┌──────────────────────┐
│       ├────────┐             │                      │
│  CE1  │        │             │                      │
└───────┘        │             │                      │
                 ├─────────────┤        PE            │
┌───────┐        │             │                      │
│       │        │             │                      │
│  CE2  ├────────┘             │                      │
└───────┘                      └──────────────────────┘

┌───────┐                      ┌──────────────────────┐
│       │                      │                      │
│  CE1  ├──────────────────────┤                      │
└───────┘                      │                      │
                               │        PE            │
┌───────┐                      │                      │
│       ├──────────────────────┤                      │
│  CE2  │                      │                      │
└───────┘                      └──────────────────────┘

~~~~


## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, the same attachment circuit may be used to host multiple services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can the request a base attachment circuit to be put in place, and then refer to that base AC when requesting services that are bound to that AC.

~~~~
                          +---------------+
                          |   Customer    |
                          +-------+-------+
          Customer Service Model  |
        e.g., slice-svc, ac-svc   |
                          +-------+-------+
                          |    Service    |
                          | Orchestration |
                          +-------+-------+
           Network Model          |
     e.g., l3vpn-ntw, sap, ac-ntw |
                          +-------+-------+
                          |   Network     |
                          | Orchestration |
                          +-------+-------+
    Network Configuration Model   |
                      +-----------+-----------+
                      |                       |
             +--------+------+       +--------+------+
             |    Domain     |       |     Domain    |
             | Orchestration |       | Orchestration |
             +---+-----------+       +--------+------+
  Device         |        |                   |
  Configuration  |        |                   |
  Model          |        |                   |
            +----+----+   |                   |
            | Config  |   |                   |
            | Manager |   |                   |
            +----+----+   |                   |
                 |        |                   |
                 | NETCONF/CLI..................
                 |        |                   |
~~~~
{: #u-ex title="An Example of AC Model Usage"}


# Description of the Attachment Circuit YANG Module


## Overall Structure of the Module

~~~~
module: ietf-ac-svc
  +--rw specific-provisioning-profiles
  |  ...
  +--rw service-provisioning-profiles
  |  ...
  +--rw attachment-circuits
     +--rw ac-global-profile* [id]
     |  ...
     +--rw ac-node-group* [id]
     |  ...
     +--rw ac* [id]
        +--rw peer-sap-id*         string
        +--rw profile-id*
        |       -> /attachment-circuits/ac-global-profile/id
        +--rw id                   string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  ...
        +--rw oam
        |  ...
        +--rw security
           ...
~~~~
{: #o-svc-tree title="Overall AC Service Tree Structure"}

## Service Profiles

~~~~
module: ietf-ac-svc
  +--rw specific-provisioning-profiles
  |  +--rw valid-provider-identifiers
  |     +--rw external-connectivity-identifier* [id]
  |     |       {external-connectivity}?
  |     |  +--rw id    string
  |     +--rw encryption-profile-identifier* [id]
  |     |  +--rw id    string
  |     +--rw qos-profile-identifier* [id]
  |     |  +--rw id    string
  |     +--rw bfd-profile-identifier* [id]
  |     |  +--rw id    string
  |     +--rw forwarding-profile-identifier* [id]
  |     |  +--rw id    string
  |     +--rw routing-profile-identifier* [id]
  |        +--rw id    string
  +--rw service-provisioning-profiles
  |  +--rw service-profile-identifier* [id]
  |     +--rw id    string
  +--rw attachment-circuits
     +--rw ac-global-profile* [id]
     |  ...
     +--rw ac-node-group* [id]
     |  ...
     +--rw ac* [id]
        ...
~~~~
{: #gp-svc-tree title="Service Proviles"}

## Attachment Circuits Profiles

## Node-Specific Profiles

## Attachment Circuits

~~~~
module: ietf-ac-svc
  +--rw specific-provisioning-profiles
  |  ...
  +--rw service-provisioning-profiles
  |  ...
  +--rw attachment-circuits
     +--rw ac-global-profile* [id]
     |  ...f
     +--rw ac-node-group* [id]
     |  ...
     +--rw ac* [id]
        +--rw peer-sap-id*         string
        +--rw profile-id*
        |       -> /attachment-circuits/ac-global-profile/id
        +--rw id                   string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  ...
        +--rw oam
        |  ...
        +--rw security
           ...
~~~~
{: #ac-svc-tree title="cccc Tree Structure"}


~~~~
module: ietf-ac-svc
  +--rw specific-provisioning-profiles
  |  ...
  +--rw service-provisioning-profiles
  |  ...
  +--rw attachment-circuits
     +--rw ac-global-profile* [id]
     |  +--rw id                   string
     |  +--rw l2-connection
     |  |  +--rw encapsulation
     |  |     +--rw type?              identityref
     |  |     +--rw dot1q
     |  |     |  +--rw tag-type?   identityref
     |  |     |  +--rw cvlan-id?   uint16
     |  |     +--rw priority-tagged
     |  |     |  +--rw tag-type?   identityref
     |  |     +--rw qinq
     |  |        +--rw tag-type?   identityref
     |  |        +--rw svlan-id    uint16
     |  |        +--rw cvlan-id    uint16
     |  +--rw ip-connection
     |  +--rw routing-protocols
     |  |  +--rw routing-protocol* [id]
     |  |     +--rw id                  string
     |  |     +--rw type?               identityref
     |  |     +--rw routing-profiles* [id]
     |  |     |  +--rw id      leafref
     |  |     |  +--rw type?   identityref
     |  |     +--rw bgp
     |  |     |  +--rw peer-groups
     |  |     |     +--rw peer-group* [name]
     |  |     |        +--rw name              string
     |  |     |        +--rw peer-as           inet:as-number
     |  |     |        +--rw address-family?   identityref
     |  |     +--rw ospf
     |  |     |  +--rw address-family?   identityref
     |  |     |  +--rw area-id           yang:dotted-quad
     |  |     |  +--rw metric?           uint16
     |  |     +--rw isis
     |  |     |  +--rw address-family?   identityref
     |  |     |  +--rw area-address      l3nm:area-address
     |  |     +--rw rip
     |  |     |  +--rw address-family?   identityref
     |  |     +--rw vrrp
     |  |        +--rw address-family?   identityref
     |  +--rw oam
     |  |  +--rw bfd {vpn-common:bfd}?
     |  |     +--rw holdtime?   uint32
     |  +--rw security
     |     +--rw encryption {vpn-common:encryption}?
     |     |  +--rw enabled?   boolean
     |     |  +--rw layer?     enumeration
     |     +--rw encryption-profile
     |        +--rw (profile)?
     |           +--:(customer-profile)
     |              +--rw customer-key-chain?
     |                      key-chain:key-chain-ref
     +--rw ac-node-group* [id]
     |  +--rw id    string
     +--rw ac* [id]
        +--rw peer-sap-id*         string
        +--rw profile-id*
        |       -> /attachment-circuits/ac-global-profile/id
        +--rw id                   string
        +--rw l2-connection
        |  +--rw encapsulation
        |  |  +--rw type?              identityref
        |  |  +--rw dot1q
        |  |  |  +--rw tag-type?   identityref
        |  |  |  +--rw cvlan-id?   uint16
        |  |  +--rw priority-tagged
        |  |  |  +--rw tag-type?   identityref
        |  |  +--rw qinq
        |  |     +--rw tag-type?   identityref
        |  |     +--rw svlan-id    uint16
        |  |     +--rw cvlan-id    uint16
        |  +--rw (l2-service)?
        |  |  +--:(l2-tunnel-service)
        |  |  |  +--rw l2-tunnel-service
        |  |  |     +--rw type?         identityref
        |  |  |     +--rw pseudowire
        |  |  |     |  +--rw vcid?      uint32
        |  |  |     |  +--rw far-end?   union
        |  |  |     +--rw vpls
        |  |  |     |  +--rw vcid?      uint32
        |  |  |     |  +--rw far-end*   union
        |  |  |     +--rw vxlan
        |  |  |        +--rw vni-id             uint32
        |  |  |        +--rw peer-mode?         identityref
        |  |  |        +--rw peer-ip-address*   inet:ip-address
        |  |  +--:(l2vpn)
        |  |     +--rw l2vpn-id?            vpn-common:vpn-id
        |  +--rw bearer-reference?          string
        |          {vpn-common:bearer-reference}?
        +--rw ip-connection
        |  +--rw ipv4 {vpn-common:ipv4}?
        |  |  +--rw local-address?
        |  |  |       inet:ipv4-address
        |  |  +--rw prefix-length?                           uint8
        |  |  +--rw address-allocation-type?
        |  |  |       identityref
        |  |  +--rw (allocation-type)?
        |  |     +--:(dynamic)
        |  |     |  +--rw (address-assign)?
        |  |     |  |  +--:(number)
        |  |     |  |  |  +--rw number-of-dynamic-address?   uint16
        |  |     |  |  +--:(explicit)
        |  |     |  |     +--rw customer-addresses
        |  |     |  |        +--rw address-pool* [pool-id]
        |  |     |  |           +--rw pool-id          string
        |  |     |  |           +--rw start-address
        |  |     |  |           |       inet:ipv4-address
        |  |     |  |           +--rw end-address?
        |  |     |  |                   inet:ipv4-address
        |  |     |  +--rw (provider-dhcp)?
        |  |     |  |  +--:(dhcp-service-type)
        |  |     |  |     +--rw dhcp-service-type?
        |  |     |  |             enumeration
        |  |     |  +--rw (dhcp-relay)?
        |  |     |     +--:(customer-dhcp-servers)
        |  |     |        +--rw customer-dhcp-servers
        |  |     |           +--rw server-ip-address*
        |  |     |                   inet:ipv4-address
        |  |     +--:(static-addresses)
        |  |        +--rw primary-address?
        |  |        |       -> ../address/address-id
        |  |        +--rw address* [address-id]
        |  |           +--rw address-id          string
        |  |           +--rw customer-address?   inet:ipv4-address
        |  +--rw ipv6 {vpn-common:ipv6}?
        |     +--rw local-address?
        |     |       inet:ipv6-address
        |     +--rw prefix-length?                           uint8
        |     +--rw address-allocation-type?
        |     |       identityref
        |     +--rw (allocation-type)?
        |        +--:(dynamic)
        |        |  +--rw (address-assign)?
        |        |  |  +--:(number)
        |        |  |  |  +--rw number-of-dynamic-address?   uint16
        |        |  |  +--:(explicit)
        |        |  |     +--rw customer-addresses
        |        |  |        +--rw address-pool* [pool-id]
        |        |  |           +--rw pool-id          string
        |        |  |           +--rw start-address
        |        |  |           |       inet:ipv6-address
        |        |  |           +--rw end-address?
        |        |  |                   inet:ipv6-address
        |        |  +--rw (provider-dhcp)?
        |        |  |  +--:(dhcp-service-type)
        |        |  |     +--rw dhcp-service-type?
        |        |  |             enumeration
        |        |  +--rw (dhcp-relay)?
        |        |     +--:(customer-dhcp-servers)
        |        |        +--rw customer-dhcp-servers
        |        |           +--rw server-ip-address*
        |        |                   inet:ipv6-address
        |        +--:(static-addresses)
        |           +--rw primary-address?
        |           |       -> ../address/address-id
        |           +--rw address* [address-id]
        |              +--rw address-id          string
        |              +--rw customer-address?   inet:ipv6-address
        +--rw routing-protocols
        |  +--rw routing-protocol* [id]
        |     +--rw id                  string
        |     +--rw type?               identityref
        |     +--rw routing-profiles* [id]
        |     |  +--rw id      leafref
        |     |  +--rw type?   identityref
        |     +--rw static
        |     |  +--rw cascaded-lan-prefixes
        |     |     +--rw ipv4-lan-prefixes* [lan next-hop]
        |     |     |       {vpn-common:ipv4}?
        |     |     |  +--rw lan         inet:ipv4-prefix
        |     |     |  +--rw lan-tag?    string
        |     |     |  +--rw next-hop    union
        |     |     |  +--rw status
        |     |     |     +--rw admin-status
        |     |     |     |  +--rw status?        identityref
        |     |     |     |  +--rw last-change?   yang:date-and-time
        |     |     |     +--ro oper-status
        |     |     |        +--ro status?        identityref
        |     |     |        +--ro last-change?   yang:date-and-time
        |     |     +--rw ipv6-lan-prefixes* [lan next-hop]
        |     |             {vpn-common:ipv6}?
        |     |        +--rw lan         inet:ipv6-prefix
        |     |        +--rw lan-tag?    string
        |     |        +--rw next-hop    union
        |     |        +--rw status
        |     |           +--rw admin-status
        |     |           |  +--rw status?        identityref
        |     |           |  +--rw last-change?   yang:date-and-time
        |     |           +--ro oper-status
        |     |              +--ro status?        identityref
        |     |              +--ro last-change?   yang:date-and-time
        |     +--rw bgp
        |     |  +--rw peer-groups
        |     |  |  +--rw peer-group* [name]
        |     |  |     +--rw name              string
        |     |  |     +--rw peer-as           inet:as-number
        |     |  |     +--rw address-family?   identityref
        |     |  +--rw neighbor* [remote-address]
        |     |     +--rw remote-address    inet:ip-address
        |     |     +--rw peer-group?
        |     |     |       -> ../../peer-groups/peer-group/name
        |     |     +--rw peer-as           inet:as-number
        |     |     +--rw address-family?   identityref
        |     |     +--rw status
        |     |        +--rw admin-status
        |     |        |  +--rw status?        identityref
        |     |        |  +--rw last-change?   yang:date-and-time
        |     |        +--ro oper-status
        |     |           +--ro status?        identityref
        |     |           +--ro last-change?   yang:date-and-time
        |     +--rw ospf
        |     |  +--rw address-family?   identityref
        |     |  +--rw area-id           yang:dotted-quad
        |     |  +--rw metric?           uint16
        |     |  +--rw status
        |     |     +--rw admin-status
        |     |     |  +--rw status?        identityref
        |     |     |  +--rw last-change?   yang:date-and-time
        |     |     +--ro oper-status
        |     |        +--ro status?        identityref
        |     |        +--ro last-change?   yang:date-and-time
        |     +--rw isis
        |     |  +--rw address-family?   identityref
        |     |  +--rw area-address      l3nm:area-address
        |     |  +--rw status
        |     |     +--rw admin-status
        |     |     |  +--rw status?        identityref
        |     |     |  +--rw last-change?   yang:date-and-time
        |     |     +--ro oper-status
        |     |        +--ro status?        identityref
        |     |        +--ro last-change?   yang:date-and-time
        |     +--rw rip
        |     |  +--rw address-family?   identityref
        |     |  +--rw status
        |     |     +--rw admin-status
        |     |     |  +--rw status?        identityref
        |     |     |  +--rw last-change?   yang:date-and-time
        |     |     +--ro oper-status
        |     |        +--ro status?        identityref
        |     |        +--ro last-change?   yang:date-and-time
        |     +--rw vrrp
        |        +--rw address-family?   identityref
        |        +--rw status
        |           +--rw admin-status
        |           |  +--rw status?        identityref
        |           |  +--rw last-change?   yang:date-and-time
        |           +--ro oper-status
        |              +--ro status?        identityref
        |              +--ro last-change?   yang:date-and-time
        +--rw oam
        |  +--rw bfd {vpn-common:bfd}?
        |     +--rw holdtime?   uint32
        |     +--rw status
        |        +--rw admin-status
        |        |  +--rw status?        identityref
        |        |  +--rw last-change?   yang:date-and-time
        |        +--ro oper-status
        |           +--ro status?        identityref
        |           +--ro last-change?   yang:date-and-time
        +--rw security
           +--rw encryption {vpn-common:encryption}?
           |  +--rw enabled?   boolean
           |  +--rw layer?     enumeration
           +--rw encryption-profile
              +--rw (profile)?
                 +--:(customer-profile)
                    +--rw customer-key-chain?
                            key-chain:key-chain-ref
~~~~
{: #d-svc-tree title="Overall AC Service Tree Structure"}

### Layer 2 Connection Structure

### Layer 3 Connection Structure

### Routing

### OAM

### Security


# YANG Module

This module uses types defined in XXX.

~~~~~~~~~~
<CODE BEGINS> file "ietf-ac-svc@2022-11-30.yang"
{::include ./yang/ietf-ac-svc.yang}
<CODE ENDS>
~~~~~~~~~~


# Security Considerations

   The YANG module specified in this document define a schema for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   There are a number of data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   These are the subtrees and data nodes and their sensitivity/
   vulnerability in the "ietf-ac-svc" module:

   * TBC
   * TBC

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability in the "ietf-ac-svc" module:

   * TBC
   * TBC



# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

~~~~
   Name:  ietf-ac-svc
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Prefix:  ac
   Reference:  RFC xxxx
~~~~

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Contributors
{:numbered="false"}

TODO contribute.
