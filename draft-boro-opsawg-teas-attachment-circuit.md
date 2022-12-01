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

normative:

informative:


--- abstract

This document specifies a YANG data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., L3VPN, L2VPN, Network Slice Service).

--- middle

# Introduction

This document specifies a YANG data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., L3VPN, L2VPN, Network Slice Service).

TBC

 The YANG data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

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


# Sample Uses of the Attachment Circuit Data Model

## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, the same attachment circuit may be used to host multiple services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can the request a base attachment circuit to be put in place, and then refer to that base AC when requesting services that are bound to that AC.

TBC.



# Description of the Attachment Circuit YANG Module

## Overall Structure of the Module

TBC TBC

~~~~
module: ietf-ac
  +--rw ac* [id]
     +--rw node-id?                    string
     +--rw local-ac-id?                string
     +--rw parent-termination-point?   nt:tp-id
     +--rw id                          string
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
{: #otree title="Overall Tree Structure"}


## AC Grouping

~~~~
  grouping ac-nw
    +-- id?                  string
    +-- l2-connection
    |  ...
    +-- ip-connection
    |  ...
    +-- routing-protocols
    |  ...
    +-- oam
    |  ...
    +-- security
       ...
~~~~
{: #ac-gp title="AC Grouping"}

## AC per Node Grouping

~~~~
  grouping ac-node
    +-- node-id?                    string
    +-- local-ac-id?                string
    +-- parent-termination-point?   nt:tp-id
    +-- id?                  string
    +-- l2-connection
    |  ...
    +-- ip-connection
    |  ...
    +-- routing-protocols
    |  ...
    +-- oam
    |  ...
    +-- security
       ...
~~~~
{: #acn-gp title="AC per Node Grouping"}

## Layer 2 Connection Structure

~~~~
  grouping l2-connection
    +-- encapsulation
    |  +-- encap-type?        identityref
    |  +-- dot1q
    |  |  +-- tag-type?         identityref
    |  |  +-- cvlan-id?         dot1q-types:vlanid
    |  |  +-- tag-operations
    |  |     +-- (op-choice)?
    |  |     |  +--:(pop)
    |  |     |  |  +-- pop?         empty
    |  |     |  +--:(push)
    |  |     |  |  +-- push?        empty
    |  |     |  +--:(translate)
    |  |     |     +-- translate?   empty
    |  |     +-- tag-1?             dot1q-types:vlanid
    |  |     +-- tag-1-type?        dot1q-types:dot1q-tag-type
    |  |     +-- tag-2?             dot1q-types:vlanid
    |  |     +-- tag-2-type?        dot1q-types:dot1q-tag-type
    |  +-- priority-tagged
    |  |  +-- tag-type?   identityref
    |  +-- qinq
    |     +-- tag-type?         identityref
    |     +-- svlan-id          dot1q-types:vlanid
    |     +-- cvlan-id          dot1q-types:vlanid
    |     +-- tag-operations
    |        +-- (op-choice)?
    |        |  +--:(pop)
    |        |  |  +-- pop?         uint8
    |        |  +--:(push)
    |        |  |  +-- push?        empty
    |        |  +--:(translate)
    |        |     +-- translate?   uint8
    |        +-- tag-1?             dot1q-types:vlanid
    |        +-- tag-1-type?        dot1q-types:dot1q-tag-type
    |        +-- tag-2?             dot1q-types:vlanid
    |        +-- tag-2-type?        dot1q-types:dot1q-tag-type
    +-- (l2-service)?
    |  +--:(l2-tunnel-service)
    |  |  +-- l2-tunnel-service
    |  |     +-- type?         identityref
    |  |     +-- pseudowire
    |  |     |  +-- vcid?      uint32
    |  |     |  +-- far-end?   union
    |  |     +-- vpls
    |  |     |  +-- vcid?      uint32
    |  |     |  +-- far-end*   union
    |  |     +-- vxlan
    |  |        +-- vni-id             uint32
    |  |        +-- peer-mode?         identityref
    |  |        +-- peer-ip-address*   inet:ip-address
    |  +--:(l2vpn)
    |     +-- l2vpn-id?            vpn-common:vpn-id
    +-- l2-termination-point?      string
    +-- local-bridge-reference?    string
    +-- bearer-reference?          string
            {vpn-common:bearer-reference}?
~~~~
{: #l2-tree title="Layer 2 Connection Tree Structure"}

## Layer 3 Connection Tree Structure

~~~~
  grouping ip-connection
    +-- l3-termination-point?   string
    +-- ipv4 {vpn-common:ipv4}?
    |  +-- local-address?
    |  |       inet:ipv4-address
    |  +-- prefix-length?                                 uint8
    |  +-- address-allocation-type?                       identityref
    |  +-- (allocation-type)?
    |     +--:(provider-dhcp)
    |     |  +-- dhcp-service-type?                       enumeration
    |     |  +-- (service-type)?
    |     |     +--:(relay)
    |     |     |  +-- server-ip-address*
    |     |     |          inet:ipv4-address
    |     |     +--:(server)
    |     |        +-- (address-assign)?
    |     |           +--:(number)
    |     |           |  +-- number-of-dynamic-address?   uint16
    |     |           +--:(explicit)
    |     |              +-- customer-addresses
    |     |                 +-- address-pool* [pool-id]
    |     |                    +-- pool-id?         string
    |     |                    +-- start-address    inet:ipv4-address
    |     |                    +-- end-address?     inet:ipv4-address
    |     +--:(dhcp-relay)
    |     |  +-- customer-dhcp-servers
    |     |     +-- server-ip-address*   inet:ipv4-address
    |     +--:(static-addresses)
    |        +-- primary-address?
    |        |       -> ../address/address-id
    |        +-- address* [address-id]
    |           +-- address-id?         string
    |           +-- customer-address?   inet:ipv4-address
    +-- ipv6 {vpn-common:ipv6}?
       +-- local-address?                 inet:ipv6-address
       +-- prefix-length?                 uint8
       +-- address-allocation-type?       identityref
       +-- (allocation-type)?
          +--:(provider-dhcp)
          |  +-- provider-dhcp
          |     +-- dhcp-service-type?
          |     |       enumeration
          |     +-- (service-type)?
          |        +--:(relay)
          |        |  +-- server-ip-address*
          |        |          inet:ipv6-address
          |        +--:(server)
          |           +-- (address-assign)?
          |              +--:(number)
          |              |  +-- number-of-dynamic-address?   uint16
          |              +--:(explicit)
          |                 +-- customer-addresses
          |                    +-- address-pool* [pool-id]
          |                       +-- pool-id?         string
          |                       +-- start-address
          |                       |       inet:ipv6-address
          |                       +-- end-address?
          |                               inet:ipv6-address
          +--:(dhcp-relay)
          |  +-- customer-dhcp-servers
          |     +-- server-ip-address*   inet:ipv6-address
          +--:(static-addresses)
             +-- primary-address?         -> ../address/address-id
             +-- address* [address-id]
                +-- address-id?         string
                +-- customer-address?   inet:ipv6-address
~~~~
{: #l3-tree title="Layer 3 Connection Tree Structure"}

## Routing Tree Structure

~~~~
  grouping routing
    +-- routing-protocol* [id]
       +-- id?       string
       +-- type?     identityref
       +-- static
       |  +-- cascaded-lan-prefixes
       |     +-- ipv4-lan-prefixes* [lan next-hop] {vpn-common:ipv4}?
       |     |  +-- lan?          inet:ipv4-prefix
       |     |  +-- lan-tag?      string
       |     |  +-- next-hop?     union
       |     |  +-- bfd-enable?   boolean {vpn-common:bfd}?
       |     |  +-- metric?       uint32
       |     |  +-- preference?   uint32
       |     |  +-- status
       |     |     +-- admin-status
       |     |     |  +-- status?        identityref
       |     |     |  +-- last-change?   yang:date-and-time
       |     |     +--ro oper-status
       |     |        +--ro status?        identityref
       |     |        +--ro last-change?   yang:date-and-time
       |     +-- ipv6-lan-prefixes* [lan next-hop] {vpn-common:ipv6}?
       |        +-- lan?          inet:ipv6-prefix
       |        +-- lan-tag?      string
       |        +-- next-hop?     union
       |        +-- bfd-enable?   boolean {vpn-common:bfd}?
       |        +-- metric?       uint32
       |        +-- preference?   uint32
       |        +-- status
       |           +-- admin-status
       |           |  +-- status?        identityref
       |           |  +-- last-change?   yang:date-and-time
       |           +--ro oper-status
       |              +--ro status?        identityref
       |              +--ro last-change?   yang:date-and-time
       +-- bgp
       |  +-- description?              string
       |  +-- local-as?                 inet:as-number
       |  +-- peer-as                   inet:as-number
       |  +-- address-family?           identityref
       |  +-- local-address?            union
       |  +-- neighbor*                 inet:ip-address
       |  +-- multihop?                 uint8
       |  +-- as-override?              boolean
       |  +-- allow-own-as?             uint8
       |  +-- prepend-global-as?        boolean
       |  +-- send-default-route?       boolean
       |  +-- site-of-origin?           rt-types:route-origin
       |  +-- ipv6-site-of-origin?      rt-types:ipv6-route-origin
       |  +-- redistribute-connected* [address-family]
       |  |  +-- address-family?   identityref
       |  |  +-- enable?           boolean
       |  +-- bgp-max-prefix
       |  |  +-- max-prefix?          uint32
       |  |  +-- warning-threshold?   decimal64
       |  |  +-- violate-action?      enumeration
       |  |  +-- restart-timer?       uint32
       |  +-- bgp-timers
       |  |  +-- keepalive?   uint16
       |  |  +-- hold-time?   uint16
       |  +-- authentication
       |  |  +-- enable?            boolean
       |  |  +-- keying-material
       |  |     +-- (option)?
       |  |        +--:(ao)
       |  |        |  +-- enable-ao?          boolean
       |  |        |  +-- ao-keychain?        key-chain:key-chain-ref
       |  |        +--:(md5)
       |  |        |  +-- md5-keychain?       key-chain:key-chain-ref
       |  |        +--:(explicit)
       |  |        |  +-- key-id?             uint32
       |  |        |  +-- key?                string
       |  |        |  +-- crypto-algorithm?   identityref
       |  |        +--:(ipsec)
       |  |           +-- sa?                 string
       |  +-- status
       |     +-- admin-status
       |     |  +-- status?        identityref
       |     |  +-- last-change?   yang:date-and-time
       |     +--ro oper-status
       |        +--ro status?        identityref
       |        +--ro last-change?   yang:date-and-time
       +-- ospf
       |  +-- address-family?   identityref
       |  +-- area-id           yang:dotted-quad
       |  +-- metric?           uint16
       |  +-- sham-links {vpn-common:rtg-ospf-sham-link}?
       |  |  +-- sham-link* [target-site]
       |  |     +-- target-site?   string
       |  |     +-- metric?        uint16
       |  +-- max-lsa?          uint32
       |  +-- authentication
       |  |  +-- enable?            boolean
       |  |  +-- keying-material
       |  |     +-- (option)?
       |  |        +--:(auth-key-chain)
       |  |        |  +-- key-chain?          key-chain:key-chain-ref
       |  |        +--:(auth-key-explicit)
       |  |        |  +-- key-id?             uint32
       |  |        |  +-- key?                string
       |  |        |  +-- crypto-algorithm?   identityref
       |  |        +--:(ipsec)
       |  |           +-- sa?                 string
       |  +-- status
       |     +-- admin-status
       |     |  +-- status?        identityref
       |     |  +-- last-change?   yang:date-and-time
       |     +--ro oper-status
       |        +--ro status?        identityref
       |        +--ro last-change?   yang:date-and-time
       +-- isis
       |  +-- address-family?   identityref
       |  +-- area-address      l3nm:area-address
       |  +-- level?            identityref
       |  +-- metric?           uint16
       |  +-- mode?             enumeration
       |  +-- authentication
       |  |  +-- enable?            boolean
       |  |  +-- keying-material
       |  |     +-- (option)?
       |  |        +--:(auth-key-chain)
       |  |        |  +-- key-chain?          key-chain:key-chain-ref
       |  |        +--:(auth-key-explicit)
       |  |           +-- key-id?             uint32
       |  |           +-- key?                string
       |  |           +-- crypto-algorithm?   identityref
       |  +-- status
       |     +-- admin-status
       |     |  +-- status?        identityref
       |     |  +-- last-change?   yang:date-and-time
       |     +--ro oper-status
       |        +--ro status?        identityref
       |        +--ro last-change?   yang:date-and-time
       +-- rip
       |  +-- address-family?   identityref
       |  +-- timers
       |  |  +-- update-interval?     uint16
       |  |  +-- invalid-interval?    uint16
       |  |  +-- holddown-interval?   uint16
       |  |  +-- flush-interval?      uint16
       |  +-- default-metric?   uint8
       |  +-- authentication
       |  |  +-- enable?            boolean
       |  |  +-- keying-material
       |  |     +-- (option)?
       |  |        +--:(auth-key-chain)
       |  |        |  +-- key-chain?          key-chain:key-chain-ref
       |  |        +--:(auth-key-explicit)
       |  |           +-- key?                string
       |  |           +-- crypto-algorithm?   identityref
       |  +-- status
       |     +-- admin-status
       |     |  +-- status?        identityref
       |     |  +-- last-change?   yang:date-and-time
       |     +--ro oper-status
       |        +--ro status?        identityref
       |        +--ro last-change?   yang:date-and-time
       +-- vrrp
          +-- address-family?       identityref
          +-- vrrp-group?           uint8
          +-- backup-peer?          inet:ip-address
          +-- virtual-ip-address*   inet:ip-address
          +-- priority?             uint8
          +-- ping-reply?           boolean
          +-- status
             +-- admin-status
             |  +-- status?        identityref
             |  +-- last-change?   yang:date-and-time
             +--ro oper-status
                +--ro status?        identityref
                +--ro last-change?   yang:date-and-time
~~~~
{: #rtg-tree title="Routing Connection Tree Structure"}


#  Attachment Circuit YANG Module

This module uses types defined in XXX.

~~~~
<CODE BEGINS> file "ietf-ac@2022-11-30.yang"
module ietf-ac {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-ac";
  prefix ac;

  import ietf-vpn-common {
    prefix vpn-common;
    reference
      "RFC 9181: A Common YANG Data Model for Layer 2 and Layer 3
                 VPNs";
  }
  import ietf-l3vpn-ntw {
    prefix l3nm;
    reference
      "RFC 9182: A YANG Network Data Model for Layer 3 VPNs";
  }
  import ietf-inet-types {
    prefix inet;
    reference
      "RFC 6991: Common YANG Data Types, Section 4";
  }
  import ietf-yang-types {
    prefix yang;
    reference
      "RFC 6991: Common YANG Data Types, Section 3";
  }

  organization
    "IETF OPSAWG (Operations and Management Area Working Group)";
  contact
    "WG Web:   <https://datatracker.ietf.org/wg/opsawg/>
     WG List:  <mailto:opsawg@ietf.org>

     Author:   Mohamed Boucadair
            <mailto:mohamed.boucadair@orange.com>
     Author:   Richard Roberts
           <mailto:rroberts@juniper.net>";
  description
    "This YANG module defines a generic YANG model for
     the configuration of attachment circuits.

     Copyright (c) 2022 IETF Trust and the persons identified as
     authors of the code.  All rights reserved.

     Redistribution and use in source and binary forms, with or
     without modification, is permitted pursuant to, and subject
     to the license terms contained in, the Revised BSD License
     set forth in Section 4.c of the IETF Trust's Legal Provisions
     Relating to IETF Documents
     (https://trustee.ietf.org/license-info).

     This version of this YANG module is part of RFC xxx; see the
     RFC itself for full legal notices.";

  revision 2022-11-30 {
    description
      "Initial revision.";
    reference
      "RFC xxxx: A YANG Data Model for Attachment Circuits";
  }

  grouping ac {
    description
      "Grouping for Attachment Circuits.";
    leaf id {
      type string;
      description
        "An identifier of the AC.";
    }
    container connection {
      description
        "Defines Layer 2 protocols and parameters that
         are required to enable AC connectivity.";
      container encapsulation {
        description
          "Container for Layer 2 encapsulation.";
        leaf type {
          type identityref {
            base vpn-common:encapsulation-type;
          }
          default "vpn-common:priority-tagged";
          description
            "Encapsulation type.  By default, the type
             of the tagged interface is
             'priority-tagged'.";
        }
        container dot1q {
          when "derived-from-or-self(../type, "
             + "'vpn-common:dot1q')" {
            description
              "Only applies when the type of the
               tagged interface is 'dot1q'.";
          }
          description
            "Tagged interface.";
          leaf tag-type {
            type identityref {
              base vpn-common:tag-type;
            }
            default "vpn-common:c-vlan";
            description
              "Tag type.  By default, the tag type is
               'c-vlan'.";
          }
          leaf cvlan-id {
            type uint16 {
              range "1..4094";
            }
            description
              "VLAN identifier.";
          }
        }
        container priority-tagged {
          when "derived-from-or-self(../type, "
             + "'vpn-common:priority-tagged')" {
            description
              "Only applies when the type of
               the tagged interface is
               'priority-tagged'.";
          }
          description
            "Priority tagged.";
          leaf tag-type {
            type identityref {
              base vpn-common:tag-type;
            }
            default "vpn-common:c-vlan";
            description
              "Tag type.  By default, the tag type is
               'c-vlan'.";
          }
        }
        container qinq {
          when "derived-from-or-self(../type, "
             + "'vpn-common:qinq')" {
            description
              "Only applies when the type of the
               tagged interface is 'qinq'.";
          }
          description
            "Includes QinQ parameters.";
          leaf tag-type {
            type identityref {
              base vpn-common:tag-type;
            }
            default "vpn-common:s-c-vlan";
            description
              "Tag type.";
          }
          leaf svlan-id {
            type uint16;
            mandatory true;
            description
              "Service VLAN (S-VLAN) identifier.";
          }
          leaf cvlan-id {
            type uint16;
            mandatory true;
            description
              "Customer VLAN (C-VLAN) identifier.";
          }
        }
      }
      choice l2-service {
        description
          "The Layer 2 connectivity service can be
           provided by indicating a pointer to an
           L2VPN or by specifying a Layer 2 tunnel
           service.";
        container l2-tunnel-service {
          description
            "Defines a Layer 2 tunnel termination.
             It is only applicable when a tunnel is
             required.  The supported values are
             'pseudowire', 'vpls', and 'vxlan'.  Other
             values may be defined, if needed.";
          leaf type {
            type identityref {
              base l3nm:l2-tunnel-type;
            }
            description
              "Selects the tunnel termination option
               for each AC Endpoint.";
          }
          container pseudowire {
            when "derived-from-or-self(../type, "
               + "'pseudowire')" {
              description
                "Only applies when the Layer 2 service
                 type is 'pseudowire'.";
            }
            description
              "Includes pseudowire termination
               parameters.";
            leaf vcid {
              type uint32;
              description
                "Indicates a pseudowire (PW) or
                 virtual circuit (VC) identifier.";
            }
            leaf far-end {
              type union {
                type uint32;
                type inet:ip-address;
              }
              description
                "Neighbor reference.";
              reference
                "RFC 8077: Pseudowire Setup and
                           Maintenance Using the Label
                           Distribution Protocol
                           (LDP), Section 6.1";
            }
          }
          container vpls {
            when "derived-from-or-self(../type, "
               + "'vpls')" {
              description
                "Only applies when the Layer 2 service
                 type is 'vpls'.";
            }
            description
              "VPLS termination parameters.";
            leaf vcid {
              type uint32;
              description
                "VC identifier.";
            }
            leaf-list far-end {
              type union {
                type uint32;
                type inet:ip-address;
              }
              description
                "Neighbor reference.";
            }
          }
          container vxlan {
            when "derived-from-or-self(../type, "
               + "'vxlan')" {
              description
                "Only applies when the Layer 2 service
                 type is 'vxlan'.";
            }
            description
              "VXLAN termination parameters.";
            leaf vni-id {
              type uint32;
              mandatory true;
              description
                "VXLAN Network Identifier (VNI).";
            }
            leaf peer-mode {
              type identityref {
                base vpn-common:vxlan-peer-mode;
              }
              default "vpn-common:static-mode";
              description
                "Specifies the VXLAN access mode.  By
                 default, the peer mode is set to
                 'static-mode'.";
            }
            leaf-list peer-ip-address {
              type inet:ip-address;
              description
                "List of a peer's IP addresses.";
            }
          }
        }
        case l2vpn {
          leaf l2vpn-id {
            type vpn-common:vpn-id;
            description
              "Indicates the L2VPN service associated
               with an Integrated Routing and Bridging
               (IRB) interface.";
          }
        }
      }
      leaf l2-termination-point {
        type string;
        description
          "Specifies a reference to a local Layer 2
           termination point, such as a Layer 2
           sub-interface.";
      }
      leaf local-bridge-reference {
        type string;
        description
          "Specifies a local bridge reference to
           accommodate, for example, implementations
           that require internal bridging.
           A reference may be a local bridge domain.";
      }
      leaf bearer-reference {
        if-feature "vpn-common:bearer-reference";
        type string;
        description
          "This is an internal reference for the
           service provider to identify the bearer
           associated with this VPN.";
      }
      container lag-interface {
        if-feature "vpn-common:lag-interface";
        description
          "Container for configuration of Link
           Aggregation Group (LAG) interface
           attributes.";
        leaf lag-interface-id {
          type string;
          description
            "LAG interface identifier.";
        }
        container member-link-list {
          description
            "Container for the member link list.";
          list member-link {
            key "name";
            description
              "Member link.";
            leaf name {
              type string;
              description
                "Member link name.";
            }
          }
        }
      }
    }
  }
}
~~~~



# Security Considerations

   The YANG module specified in this document defines a schema for data
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
   vulnerability in the "ietf-ac" module:

   * TBC
   * TBC

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability:

   * TBC
   * TBC

# IANA Considerations

   IANA has registered the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

   URI:  urn:ietf:params:xml:ns:yang:ietf-ac
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.

   IANA has registered the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

   Name:  ietf-ac
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac
   Prefix:  ac
   Reference:  RFC xxxx


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
