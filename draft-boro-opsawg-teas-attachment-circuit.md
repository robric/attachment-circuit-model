---
title: "A YANG Service Data Model for Attachment Circuits"
abbrev: "ACSM"
category: std

docname: draft-boro-opsawg-teas-attachment-circuit-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "OPSAWG"
keyword:
 - Slice Service
 - L3VPN
 - L2VPN

author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
    role: editor
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

 -
    fullname: Bo Wu
    organization: Huawei Technologies
    email: lana.wubo@huawei.com

normative:
  ISO10589:
    title: Information technology - Telecommunications and information exchange between systems - Intermediate System to Intermediate System intra-domain routeing information exchange protocol for use in conjunction with the protocol for providing the connectionless-mode network service (ISO8473)
    author:
      org: ISO
    date: 2002
    target: https://www.iso.org/standard/30932.html

informative:


--- abstract

This document specifies a YANG service data model for Attachment Circuits (ACs). The model can be used for the provisioning of ACs prior or during service provisioning (e.g., Network Slice Service).

The model is designed with the intent to be reusable. Whether a service model reuses structures defined in the AC service model or simply include an AC reference is a design choice of these service models. Relying upon the AC model to manage ACs over which a service is delivered has the merit to decorrelate the management of a service vs. upgrade the AC components to reflect recent AC technologies or features.

Each AC is identified with a unique identifier within a domain. The mapping between this AC and a PE that terminates the AC is hidden to the application/customer that makes use of the AC service model. Such an information is internal to the network controller. Thus, the details about the (network node-specific) attachment interfaces are not exposed in this service model.

--- middle

# Introduction

This document specifies a YANG service data model for managing attachment circuits (ACs) that are exposed by a network to its customers (e.g., an enterprise site, a network function, a hosting infrastructure, a peer network provider) . The model can be used for the provisioning of ACs prior or during advanced service provisioning (e.g., Network Slice Service).

Also, the model is designed with the intent to be reusable. Whether a service model reuses structures defined in the AC service model or simply includes an AC reference (that was communicated during AC instantiation) is a design choice of these service models. Relying upon the AC service model to manage ACs over which services are delivered has the merit to decorrelate the management of a service vs. upgrade the AC components to reflect recent AC technologies or new features (e.g., new encryption scheme, additional routing protocol).

Each AC is identified with a unique identifier within a domain. From a network provider standpoint, an AC can be bound to a single or multiple SAPs {{!I-D.ietf-opsawg-sap}}. Likewise, a SAP can be bound to one or multiple ACs. However, the mapping between this AC and a local PE that terminates the AC is hidden to the application that makes use of the AC service model. This information is internal to the Network controller. As such, the details about the (node-specific) attachment interfaces are not exposed in this service model.

 The YANG data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

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

{{uc}} depictes two target topology flavors that may host ACs. A CE may be a physical node or a logical entity. The same AC request may include one or multiple ACs that may belong to one or both of these flavors. For the sake of simplfying the illustration, only a subset of these ACs is shown in {{uc}}.

CEs may be dedicated to one single service or host multiple services (e.g., service functions {{?RFC7665}}). A single AC (as seen by a network provider) may be bound to one or multiple peer SAPs.

~~~~ aasvg
┌───────┐                      ┌──────────────────────┐
│       ├────────┐             │                      │
│  CE1  │        │             │                      │
└───────┘        │             │                      │
                 ├─────────────┤      Network         │
┌───────┐        │             │                      │
│       │        │             │                      │
│  CE2  ├────────┘             │                      │
└───────┘                      └──────────────────────┘

┌───────┐                      ┌──────────────────────┐
│       │                      │                      │
│  CE1  ├──────────────────────┤                      │
└───────┘                      │                      │
                               │      Network         │
┌───────┐                      │                      │
│       ├──────────────────────┤                      │
│  CE2  │                      │                      │
└───────┘                      └──────────────────────┘
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, the same attachment circuit may be used to host multiple services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can the request a base attachment circuit to be put in place, and then refer to that base AC when requesting services that are bound to that AC.

{{u-ex}} shows the positioning of the AC service model is the overall service delivery process.

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
{: #u-ex title="An Example of AC Model Usage" artwork-align="center"}

## Examples

This section includes a non-exhaustive list of examples to illustrate the use of the AC service model.

### Request A New Bearer {#ex-create-bearer}

An example of a request message body to create a bearer is shown in {{create-bearer}}.

~~~~
{::include ./json-examples/simple-bearer-create.json}
~~~~
{: #create-bearer title="Example of a Message Body to Create A New Bearer"}

A bearer-reference is then generated by the controller for this bearer. {{#get-bearer}} shows the example of the response message body that is sent by the controller as a response to GET request:

~~~~
{::include ./json-examples/get-bearer-reference.json}
~~~~
{: #get-bearer title="Example of a Message Body to Retrieve the Bearer Reference"}

### Request An AC over An Existing Bearer

An example of  a request message body to create a simple AC over an existing bearer is shown in {{ac-b}}. The bearer reference is assumed to be known to both the customer and the network provider. Such a reference can be retrieved, e.g., following the example described in {{ex-create-bearer}}.

~~~~
{::include ./json-examples/simple-ac-existing-bearer.json}
~~~~
{: #ac-b title="Example of a Message Body to Request an AC over an Existing Bearer"}

### Request An AC for a Knwon Peer SAP

An example of a request to create a simple AC, when the peer SAP is known, is shown in {{ac-known-ps}}. In this example, the peer SAP identifier points to an identifier of a service function. The (topological) location of that service function is assumed to be known to the network controller. For example, this can be determined as part of an on-demand procedure to instantiate a service function in a cloud. That instantiated service function can be granted a connectivity service via the provider network.

~~~~
{::include ./json-examples/simple-ac-known-peer-sap.json}
~~~~
{: #ac-known-ps title="Example of a Message Body to Request an AC with a Peer SAP"}

### One CE, Two ACs

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce}}. This example assumes that static addressing is used for both ACs.

~~~~
{::include ./json-examples/two-acs-same-ce.json}
~~~~
{: #two-acs-same-ce title="Example of a Message Body to Request Two ACes on The Same Link"}

### Illustrate the Use of Global Profiles

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce-profile}}. Unlike {{two-acs-same-ce}}, this example factorizes some of the redundant data.

~~~~
{::include ./json-examples/two-acs-same-ce-profile.json}
~~~~
{: #two-acs-same-ce-profile title="Example of a Message Body to Request Two ACes on The Same Link (Global Profile)"}

### Illustrate the Use of Per-Node Profiles

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce-node-profile}}. Unlike {{two-acs-same-ce}}, this example factorizes all redundant data.

~~~~
{::include ./json-examples/two-acs-same-ce-node-profile.json}
~~~~
{: #two-acs-same-ce-node-profile title="Example of a Message Body to Request Two ACes on The Same Link (Node Profile)"}

A customer may request adding a new AC by simply referring to an existing per-node AC profile as shown in {{add-ac-same-ce-node-profile}}. This AC inherites all the data that was enclosed in the indicated per-node AC profile (IP addressing, routing, etc.).

~~~~
{::include ./json-examples/add-ac-same-ce-node-profile.json}
~~~~
{: #add-ac-same-ce-node-profile title="Example of a Message Body to Add a new AC over an existing link (Node Profile)"}

### Multiple CEs

{{network-example}} shows an example of CEs that are interconnected by a service provider network.

~~~~ aasvg

                   +----------------------------------+
      +----+       |                                  |       +----+
      | CE1+-------+                                  +-------+ CE3|
      +----+       |                                  |       +----+
                   |              Network             |
      +----+       |                                  |       +----+
      |CE2 +-------+                                  +-------+ CE4|
      +----+       |                                  |       +----+
                   +----------------------------------+
~~~~
{: #network-example title="Network Topology Example" artwork-align="center"}

{{multiple-sites}} depicts an example of the message body of a request to instantiate the various ACs that are shown in {{network-example}}.

~~~~
{::include ./json-examples/multiple-ce-with-profile.json}
~~~~
{: #multiple-sites title="Example of a Message Body to of Creating Multiple ACs bound to Multiple CEs"}

# Description of the Attachment Circuit YANG Module


## Overall Structure of the Module

The overall tree structure of the AC service module is shown in {{o-svc-tree}}.

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
{: #o-svc-tree title="Overall AC Service Tree Structure" artwork-align="center"}

The full tree structure is provided in {{full-tree}}.

Each AC is identified with a unique identifier within a domain. The mapping between this AC and a local PE that terminates the AC is hidden to the application that makes use of the AC service model. This information is internal to the Network controller. As such, the details about the (node-specific) attachment interfaces are not exposed in this service model.

## Service Profiles

### Description

The 'specific-provisioning-profiles' container ({{gp-svc-tree}}) can be used by a service provider to maintain a set of specific profiles that are similar to those defined in {{!RFC9181}}. The exact definition of the profiles is local to each service provider. The model only includes an identifier for these profiles in order to facilitate identifying and binding local policies when building an AC.

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
     +--rw ac* [name]
        ...
~~~~
{: #gp-svc-tree title="Service Proviles" artwork-align="center"}

As shown in {{gp-svc-tree}}, two profile types can be defined: 'specific-provisioning-profiles' and 'service-provisioning-profiles'. Whether only specific profiles, service profiles, or a combination thereff are used is local to each service provider.

The following specific rovisioning profiles can be defined:

'external-connectivity-identifier':
: Refers to a profile that defines the external connectivity provided to a site that is connected via an AC. External connectivity may be access to the Internet or restricted connectivity, such as access to a public/private cloud.

'encryption-profile-identifier':
: Refers to a set of policies related to the encryption setup that can be applied when provisioning an AC.

'qos-profile-identifier':
: Refers to a set of policies, such as classification, marking, and actions (e.g., {{?RFC3644}}).

'bfd-profile-identifier':
: Refers to a set of Bidirectional Forwarding Detection (BFD) policies {{!RFC5880}} that can be invoked when building an AC.

'forwarding-profile-identifier':
: Refers to the policies that apply to the forwarding of packets conveyed within an AC. Such policies may consist, for example, of applying Access Control Lists (ACLs).

'routing-profile-identifier':
: Refers to a set of routing policies that will be invoked (e.g., BGP policies) when building an AC.

### Referencing Service/Specific Profiles

All these profiles are uniquely identified by the NETCONF/RESTCONF server by an identifier. To ease referencing these profiles by other data models, specific typedefs are defined for each of these profiles. Likewise, an attachment circuit referenc typedef is defiened when referencing a (global) attachment circuit by its name is required. These typedefs SHOULD be used when other modules need a reference to one of these profiles or attahment circuits.

## Attachment Circuits Profiles

## Node-Specific Profiles

## Attachment Circuits

The structure of 'attachment-circuits' is shown in {{ac-svc-tree}}.

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
     +--rw ac* [name]
        +--rw customer-name?        string
        +--rw description?          string
        +--rw requested-ac-start?   yang:date-and-time
        +--rw requested-ac-stop?    yang:date-and-time
        +--ro actual-ac-start?      yang:date-and-time
        +--ro actual-ac-stop?       yang:date-and-time
        +--rw peer-sap-id*          string
        +--rw ac-global-profile*    ac-global-profile-reference
        +--rw ac-node-profile*      ac-node-group-reference
        +--rw name                  string
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
{: #ac-svc-tree title="Overall Attachment Circuits Tree Structure" artwork-align="center"}


### Layer 2 Connection Structure

As shown in the tree depicted in {{l2-svc-tree}}, the 'l2-connection' container defines service parameters to enable such connectivity at Layer 2.

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
     +--rw ac* [name]
        ...
        +--rw name                  string
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
        |  +--rw bearer-reference?          string {vpn-common:bearer-reference}?
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  ...
        +--rw oam
        |  ...
        +--rw security
           ...
~~~~
{: #l2-svc-tree title="Layer 2 Connection Tree Structure" artwork-align="center"}


### Layer 3 Connection Structure

As shown in the tree depicted in {{l3-svc-tree}}, the 'l3-connection' container defines service parameters to enable Layer 3 connectivity for an AC.

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
     +--rw ac* [name]
        ...
        +--rw name                  string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  +--rw ipv4 {vpn-common:ipv4}?
        |  |  +--rw local-address?                           inet:ipv4-address
        |  |  +--rw prefix-length?                           uint8
        |  |  +--rw address-allocation-type?                 identityref
        |  |  +--rw (allocation-type)?
        |  |     +--:(dynamic)
        |  |     |  +--rw (address-assign)?
        |  |     |  |  +--:(number)
        |  |     |  |  |  +--rw number-of-dynamic-address?   uint16
        |  |     |  |  +--:(explicit)
        |  |     |  |     +--rw customer-addresses
        |  |     |  |        +--rw address-pool* [pool-id]
        |  |     |  |           +--rw pool-id          string
        |  |     |  |           +--rw start-address    inet:ipv4-address
        |  |     |  |           +--rw end-address?     inet:ipv4-address
        |  |     |  +--rw (provider-dhcp)?
        |  |     |  |  +--:(dhcp-service-type)
        |  |     |  |     +--rw dhcp-service-type?           enumeration
        |  |     |  +--rw (dhcp-relay)?
        |  |     |     +--:(customer-dhcp-servers)
        |  |     |        +--rw customer-dhcp-servers
        |  |     |           +--rw server-ip-address*   inet:ipv4-address
        |  |     +--:(static-addresses)
        |  |        +--rw address* [address-id]
        |  |           +--rw address-id          string
        |  |           +--rw customer-address?   inet:ipv4-address
        |  +--rw ipv6 {vpn-common:ipv6}?
        |     +--rw local-address?                           inet:ipv6-address
        |     +--rw prefix-length?                           uint8
        |     +--rw address-allocation-type?                 identityref
        |     +--rw (allocation-type)?
        |        +--:(dynamic)
        |        |  +--rw (address-assign)?
        |        |  |  +--:(number)
        |        |  |  |  +--rw number-of-dynamic-address?   uint16
        |        |  |  +--:(explicit)
        |        |  |     +--rw customer-addresses
        |        |  |        +--rw address-pool* [pool-id]
        |        |  |           +--rw pool-id          string
        |        |  |           +--rw start-address    inet:ipv6-address
        |        |  |           +--rw end-address?     inet:ipv6-address
        |        |  +--rw (provider-dhcp)?
        |        |  |  +--:(dhcp-service-type)
        |        |  |     +--rw dhcp-service-type?           enumeration
        |        |  +--rw (dhcp-relay)?
        |        |     +--:(customer-dhcp-servers)
        |        |        +--rw customer-dhcp-servers
        |        |           +--rw server-ip-address*   inet:ipv6-address
        |        +--:(static-addresses)
        |           +--rw address* [address-id]
        |              +--rw address-id          string
        |              +--rw customer-address?   inet:ipv6-address
        +--rw routing-protocols
        |  ...
        +--rw oam
        |  ...
        +--rw security
           ...
~~~~
{: #l3-svc-tree title="Layer 3 Connection Tree Structure" artwork-align="center"}

### Routing

As shown in the tree depicted in {{rtg-svc-tree}}, the 'routing-protocols' container defines th erequired parameters to enable the required routing features for an AC. One or more routing protocols can be associated with an AC.  Such routing protocols are then enabled between a PE and the CE. Each routing instance is uniquely identified to accommodate scenarios where multiple instances of the same routing protocol have to be configured on the same link.

In addition to static routing, the module supports the following routing protocols:

* BGP {{!RFC4271}}
* OSPF {{!RFC4577}} or {{!RFC6565}}
* IS-IS {{ISO10589}}{{!RFC1195}}{{!RFC5308}}
* RIP {{!RFC2453}}

The model also supports the Virtual Router Redundancy Protocol (VRRP) {{!RFC5798}} on an AC.

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
     +--rw ac* [name]
        ...
        +--rw name                  string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  +--rw routing-protocol* [id]
        |     +--rw id                  string
        |     +--rw type?               identityref
        |     +--rw routing-profiles* [id]
        |     |  +--rw id      routing-profile-reference
        |     |  +--rw type?   identityref
        |     +--rw static
        |     |  +--rw cascaded-lan-prefixes
        |     |     +--rw ipv4-lan-prefixes* [lan next-hop] {vpn-common:ipv4}?
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
        |     |     +--rw ipv6-lan-prefixes* [lan next-hop] {vpn-common:ipv6}?
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
        |     |  |     +--ro local-address?    inet:ip-address
        |     |  |     +--rw peer-as           inet:as-number
        |     |  |     +--rw address-family?   identityref
        |     |  +--rw neighbor* [remote-address]
        |     |     +--rw remote-address    inet:ip-address
        |     |     +--ro local-address?    inet:ip-address
        |     |     +--rw peer-group?       -> ../../peer-groups/peer-group/name
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
        |     |  +--rw area-address      area-address
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
        |  ...
        +--rw security
           ...
~~~~
{: #rtg-svc-tree title="Routing Tree Structure" artwork-align="center"}

For all supported routing protocols, 'address-family' indicates whether IPv4, IPv6, or both address families are to be activated. For example, this parameter is used to determine whether RIPv2 {{!RFC2453}}, RIP Next Generation (RIPng), or both are to be enabled {{!RFC2080}}.


### OAM

As shown in the tree depicted in {{oam-svc-tree}}, the 'oam' container defines OAM-related parameters of an AC.

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
     +--rw ac* [name]
        ...
        +--rw name                  string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  ...
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
           ...
~~~~
{: #oam-svc-tree title="OAM Tree Structure" artwork-align="center"}

### Security

As shown in the tree depicted in {{sec-svc-tree}}, the 'security' container defines sercurity parameters of an AC.

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
     +--rw ac* [name]
        ...
        +--rw name                  string
        +--rw l2-connection
        |  ...
        +--rw ip-connection
        |  ...
        +--rw routing-protocols
        |  ...
        +--rw oam
        |  ...
        +--rw security
           +--rw encryption {vpn-common:encryption}?
           |  +--rw enabled?   boolean
           |  +--rw layer?     enumeration
           +--rw encryption-profile
              +--rw (profile)?
                 +--:(customer-profile)
                    +--rw customer-key-chain?   key-chain:key-chain-ref
~~~~
{: #sec-svc-tree title="Security Tree Structure" artwork-align="center"}

# YANG Module

This module uses types defined in {{!RFC6991}}, {{!RFC9181}}, and {{!RFC8177}}.

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

# Full Tree {#full-tree}

~~~~
{::include ./yang/ac-svc-without-groupings.txt}
~~~~
{: #d-svc-tree title="AC Service Tree Structure" artwork-align="center"}


# Acknowledgments
{:numbered="false"}

TODO acknowledge.

# Contributors
{:numbered="false"}

TODO contribute.
