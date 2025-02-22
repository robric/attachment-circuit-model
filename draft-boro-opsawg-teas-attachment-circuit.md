---
title: "YANG Data Models for 'Attachment Circuits'-as-a-Service (ACaaS)"
abbrev: "ACaaS"
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
    role: editor
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

contributor:
  -
    name: Victor Lopez
    org: Nokia
    email: victor.lopez@nokia.com

  -
    name: Ivan Bykov
    org: Ribbon Communications
    email: Ivan.Bykov@rbbn.com

  -
    name: Qin Wu
    org: Huawei
    email: bill.wu@huawei.com

  -
    name: Ogaki Kenichi
    org: KDDI
    email: ke-oogaki@kddi.com

normative:

informative:

  AC-SVC-Tree:
    title: Full Service Attachment Circuit Tree Structure
    date: 2023
    target: https://raw.githubusercontent.com/boucadair/attachment-circuit-model/main/yang/full-trees/ac-svc-without-groupings.txt

  AC-SVC-GRP:
    title: Reusable Groupings in Service Attachment Circuits
    date: 2023
    target: https://raw.githubusercontent.com/boucadair/attachment-circuit-model/main/yang/full-trees/ac-svc-groupings.txt

--- abstract

This document specifies a YANG service data model for Attachment Circuits (ACs). This model can be used for the provisioning of ACs prior or during service provisioning (e.g., Network Slice Service). The document specifies also a module that updates other service and network modules with the required information to bind specific services to ACs that are created using the AC service model.

Also, the document specifies a set of reusable groupings. Whether a service model reuses structures defined in the AC models or simply include an AC reference is a design choice of these service models. Relying upon the AC service model to manage ACs over which a service is delivered has the merit to decorrelate the management of a service vs. upgrade the AC components to reflect recent AC technologies or features.

--- middle

# Introduction

## Scope and Intended Use

Connectivity services are provided by networks to customers via dedicated terminating points (e.g., service functions, customer edges (CEs), peer ASBRs, data centers gateways, Internet Exchange Points). A connectivity service is basically about ensuring data transfer received from (or destined to) a given terminating point to (or from) other terminating points that belong to the same customer/service, an interconnection node, or an ancillary node. A set of objectives for the connectivity service may eventually be negotiated and agreed upon between a customer a network provider. For that data transfer to take place within the provider network, it is assumed that adequate setup is provisioned over the links that connect customer terminating points and a provider network so that data can be successfully exchanged over these links. The required setup is referred to in this document as Attachment Circuits (ACs), while the underlying link is referred to as "bearers".

This document adheres to the definition of an Attachment Circuit as provided in Section 1.2 of {{!RFC4364}}, especially:

> Routers can be attached to each other, or to end systems, in a
   variety of different ways: PPP connections, ATM Virtual Circuits
   (VCs), Frame Relay VCs, ethernet interfaces, Virtual Local Area
   Networks (VLANs) on ethernet interfaces, GRE tunnels, Layer 2
   Tunneling Protocol (L2TP) tunnels, IPsec tunnels, etc.  We will use
   the term "attachment circuit" to refer generally to some such means
   of attaching to a router.  An attachment circuit may be the sort of
   connection that is usually thought of as a "data link", or it may be
   a tunnel of some sort; what matters is that it be possible for two
   devices to be network layer peers over the attachment circuit.

When a customer requests a new value-added service, the service can be bound to existing attachment circuits or trigger the instantiation of new attachment circuits. The provisioning of an value-added service should, thus, accommodate both deployments.

Also, because the instantiation of an attachment circuit requires coordinating the provisioning of endpoints that might not belong to the same administrative entity (customer vs. provider or distinct operational teams within the same provider, etc.), **programmatic means to expose 'attachment circuits'-as-a-service will greatly simplify the provisioning of value added services** that will be delivered over an attachment circuits.

This document specifies a YANG service data model ("ietf-ac-svc") for managing attachment circuits that are exposed by a network to its customers (e.g., an enterprise site, a network function, a hosting infrastructure, a peer network provider). The model can be used for the provisioning of ACs prior or during advanced service provisioning (e.g., Network Slice Service).

The "ietf-ac-svc" includes a set of reusable groupings. Whether a service model reuses structures defined in the "ietf-ac-svc" or simply includes an AC reference (that was communicated during AC service instantiation) is a design choice of these service models. Relying upon the AC service model to manage ACes over which services are delivered has the merit to decorrelate the management of the (core) service vs. upgrade the AC components to reflect recent AC technologies or new features (e.g., new encryption scheme, additional routing protocol). **This document favors the approach of completely relying upon the AC service model instead of duplicating data nodes into specific modules of advanced services that are delivered over an Attachment Circuit.**

Because the provisioning of an AC requires a bearer to be in place, this document allows customers to manage their bearer requests by means of a new module, called "ietf-bearer-svc". The customers can then retrieve a provider-assigned bearer reference that they will include in their AC service requests.

An AC service request can provide a reference to a bearer or a set of peer SAPs. Both schemes are supported in the AC service model.

Each AC is identified with a unique identifier within a (provider) domain. From a network provider standpoint, an AC can be bound to a single or multiple Service Attachment Points (SAPs) {{?I-D.ietf-opsawg-sap}}. Likewise, the same SAP can be bound to one or multiple ACs. However, the mapping between an AC and a PE in the provider network that terminates that AC is hidden to the application that makes use of the AC service model. Such mapping information is internal to the network controllers. As such, the details about the (node-specific) attachment interfaces are not exposed in the AC service model.

The AC service model **does not make any assumption about the internal structure or even the nature or the services that will be delivered over an attachment circuit**. Customers do not have access to that network view other than the ACes that the ordered. For example, the AC service model can be used to provision a set of ACes to connect multiple sites (Site1, Site2, ..., SiteX) for customer that also requested VPN services. If these provisioning of these services require specific configured on ASBR nodes, such configuration is handled at the network level and is not exposed at the service level to the customer. However, the network controller will have access to such a view as the service points in these ASBRs will be exposed as SAPs with "role" set to "ietf-sap-ntw:nni" {{?I-D.ietf-opsawg-sap}}.

The AC service model can be used in a variety of contexts, such as (but not limited to) those provided in {{examples}}:

* Request an attachment circuit for a known peer SAP ({{ac-no-bearer-peer-sap}}).
* Instantiate multiple attachment circuits over the same bearer ({{sec-ex-one-ce-multi-acs}}).
* Control the precedence over multiple attachment circuits ({{sec-ex-prec}}).
* Bind a slice service to a set of pre-provisioned attachment circuits ({{sec-ex-slice}}).
* Connect a Cloud Infrastructure to a service provider network ({{sec-ex-cloud}}).

The examples use the IPv4 address blocks reserved for documentation {{?RFC5737}}, the IPv6 prefix reserved for documentation {{?RFC3849}}, and the Autonomous System (AS) numbers reserved for documentation {{?RFC5398}}.

The YANG data models in this document conform to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Position ACaaS vs. Other Data Models

The AC model specified in this document **is not a network model** {{?RFC8969}}. As such, the model does not expose details related to specific nodes in the provider's network that terminate an AC. The mapping between an AC as seen by a customer and the network implementation of an AC is maintained by the network controllers, and is not exposed to the customer. Such a mapping can be maintained using a variety of network models, e.g., augmented SAP AC network model {{?I-D.boro-opsawg-ntw-attachment-circuit}}.

The AC service model **is not a device model**. A network provider may use a variety of device models (e.g., Routing management {{?RFC8349}} or BGP {{?I-D.ietf-idr-bgp-model}}) to provision an AC service.

### Why Not Using L2SM as Reference Data Model for ACaaS?

The L2SM {{?RFC8466}} covers some AC-related considerations. Nevertheless, the L2SM structure is too layer 2 centric. For example, the L2SM part does not cover Layer 3 provisioning, which is required for the instantiation of typical ACs.

### Why Not Using L3SM as Reference Data Model for ACaaS?

Similar to the L2NM, the L3SM {{?RFC8299}} covers some AC-related considerations. Nevertheless, the L3SM structure does not adequately cover layer 2 provisioning matters. Moreover, the L3SM is drawn with conventional L3VPN deployments in mind and, as such, has some limitations for instantiating ACs in other deployment contexts (e.g., cloud environments). For example, the L3SM does not allow to provision multiple BGP sessions over the same AC.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses the following terms:

Bearer:
: A physical or logical link that connects a customer node (or site) to a provider network. A bearer can be a wireless or wired link. One or multiple technologies can be used to build a bearer. The bearer type can be specified by a customer.
: The operator allocates a unique bearer reference to identify a bearer within its network (e.g., customer line identifier). Such a reference can be retrieved by a customer and used in subsequent service placement requests to unambiguously identify where a service is to be bound.
: The concept of bearer can be generalized to refer to the required underlying connection for the provisioning of an attachment circuit. One or multiple attachment circuits may be hosted over the same bearer (e.g., multiple VLANs on the same bearer that is provided by a physical link).

Network controller:
: Denotes a functional entity responsible for the management of the service provider network.

Service orchestrator:
: Refers to a functional entity that interacts with the customer of a network service. The service orchestrator is typically responsible for the attachment circuits, the Provider Edge (PE) selection, and requesting the activation of the requested service to a network controller.

Service provider network:
: A network that is able to provide network services (e.g., Layer 2 VPN, Layer 3, and Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., Layer 2 VPN, Layer 3, and Network Slice Services).

# Sample Uses of the Data Models

## ACs Terminated by One or Multiple Customer Terminating Points

{{uc}} depicts two target topology flavors that involve ACs. These topologies are characterized as follows:

* A Customer Terminating Point (CTP) may be a physical device or a logical entity. Such a logical entity is typically a software component (e.g., a virtual service function that is hosted within the provider's network or a third-party infrastscruture). A CTP is seen by the network as a peer SAP.

* The same AC service request may include one or multiple ACs that are bound to a single CTP or a plurality of CTPs.

* CTPs may be dedicated to one single connectivity service or host multiple connectivity services (e.g., CTPs as role of service functions {{?RFC7665}}).

* A single AC (as seen by a network provider) may be bound to one or multiple peer SAPs (e.g., CTP#1 and CTP#2 are tagged as peer SAPs for the same AC). For example, and as discussed in {{!RFC4364}}, multiple CTPs (CEs) can be attached to a PE over the same attachment circuit. This is typically implemented if the layer 2 infrastructure between the CTP and the network provides a multipoint service.

* The same CTP may terminate multiple ACs. These ACs may be over the same or distinct bearers.

* The customer may request protection schemes where the ACs bound to a customer endpoints are terminated by the same PE (e.g., CTP#3), distinct PEs (e.g., CTP#34), etc. The network provider uses this request to decide where to terminate the AC in the network provider network and also whether to enable specific capabilities (e.g., Virtual Router Redundancy Protocol (VRRP)).

~~~~ aasvg
┌───────┐                ┌────────────────────┐           ┌───────┐
│       ├──────┐         │                    ├────AC─────┤       │
│ CTP#1 │      │         │                    ├────AC─────┤ CTP#3 |
└───────┘      │         │                    │           └───────┘
               ├───AC────┤     Network        │
┌───────┐      │         │                    │
│       │      │         │                    │           ┌───────┐
│ CTP#2 ├──────┘         │                    │─────AC────┤ CTP#4 │
└───────┘                │                    │           └────+──┘
                         └───────────+────────┘                |
                                     |                         |
                                     └────────────AC───────────┘
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, the same attachment circuit may be used to host multiple connectivity services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a bearer or an attachment circuit to be put in place, and then refer to that bearer or AC when requesting services that are bound to the bearer or AC.

{{u-ex}} shows the positioning of the AC service model is the overall service delivery process.

~~~~
                          +---------------+
                          |   Customer    |
                          +-------+-------+
          Customer Service Model  |
          e.g., slice-svc, ac-svc,|bearer-svc
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
               +--------------------------------+
 +----+ Bearer |                                | Bearer +----+
 |CTP +--------+            Network             +--------+ CTP|
 +----+        |                                |        +----+
               +--------------------------------+
  Site A                                                  Site B
~~~~
{: #u-ex title="An Example of AC Model Usage" artwork-align="center"}

# Description of the Data Models

## The Bearer Service ("ietf-bearer-svc") YANG Module

{{bearer-st}} shows the tree for managing the bearers (that is, the properties of the attachment that are below Layer 3). A bearer can be a wireless or wired link. A reference to a bearer is generated by the operator.
Such a reference can be used, e.g., in a subsequent service request to create an AC. The anchoring of the AC can also be achieved by indicating (with or without a bearer reference), a peer SAP identifier (e.g., an identifier of a Service Function).

~~~~
{::include ./yang/full-trees/bearers-stree.txt}
~~~~
{: #bearer-st title="Bearers Tree Structure" artwork-align="center"}

The same customer site (CE, NF, etc.) can terminate one or multiple bearers; each of them uniquely identified by a reference that is assigned by the network provider. These bearers can terminate on the same or distinct network nodes. CEs that terminate multiple bearers are called multi-homed CEs.

The descriptions of the bearer data nodes are as follows:

'id':
: Used to uniquely identify a bearer. This identifier is typically selected by the client when requesting a bearer.

'description':
: Includes a textual description of the bearer.

'op-comment':
: Includes operational comments that may be useful for managing the bearer (building, level, etc.). No structure is associated with this data node to accommodate all deployments.

'customer-point':
: Specifies the customer terminating point for the bearer. A bearer request can indicate a site, a device, a combination thereof, or a custom information when requesting a bearer. All these schemes are supported in the model.

'requested-type':
: Specifies the requested bearer type (Ethernet, wireless, etc.).

'bearer-reference':
: Returns an internal reference for the service provider to identify the bearer. This reference can be used when requesting services. {{ex-create-bearer}} provides an example about how this reference can be retrieved by a customer. Whether the 'bearer-reference' mirrors the content of the 'id' is deployment specific. The module does not assume nor preclude such schemes.

'status':
: Used to track the overall status of a given bearer. Both operational and administrative status are maintained together with a timestamp. See {{!RFC9181}} for more details.


## The Attachment Circuit Service ("ietf-ac-svc") YANG Module

### Overall Structure

The overall tree structure of the AC service module is shown in {{o-svc-tree}}.

~~~~
{::include ./yang/subtrees/overall-stree.txt}
~~~~
{: #o-svc-tree title="Overall AC Service Tree Structure" artwork-align="center"}

The full ACaaS tree is available at {{AC-SVC-Tree}}. The full reusable groupings defined in the ACaaS module are shown in {{AC-SVC-GRP}}.

Each AC is identified with a unique name ('../ac/name') within a domain. The mapping between this AC and a local PE that terminates the AC is hidden to the application that makes use of the AC service model. This information is internal to the Network controller. As such, the details about the (node-specific) attachment interfaces are not exposed in this service model.

The AC service model uses groupings and types defined in the AC common model {{!I-D.boro-opsawg-teas-common-ac}}. Therefore, the description of these nodes are not reiterated in the following subsections.

### Service Profiles {#sec-profiles}

#### Description

The 'specific-provisioning-profiles' container ({{gp-svc-tree}}) can be used by a service provider to maintain a set of reusable profiles. The profiles definition are similar to those defined in {{!RFC9181}}, including: Quality of Service (QoS),  Bidirectional Forwarding Detection (BFD), forwarding, and routing profiles. The exact definition of the profiles is local to each service provider. The model only includes an identifier for these profiles in order to facilitate identifying and binding local policies when building an AC.

~~~~
{::include ./yang/subtrees/sp-svc-profiles-stree.txt}
~~~~
{: #gp-svc-tree title="Service Profiles" artwork-align="center"}

As shown in {{gp-svc-tree}}, two profile types can be defined: 'specific-provisioning-profiles' and 'service-provisioning-profiles'. Whether only specific profiles, service profiles, or a combination thereof are used is local to each service provider.

The following specific provisioning profiles can be defined:

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

#### Referencing Service/Specific Profiles

All the abovementioned profiles are uniquely identified by the NETCONF/RESTCONF server by an identifier. To ease referencing these profiles by other data models, specific typedefs are defined for each of these profiles. Likewise, an attachment circuit reference typedef is defined when referencing a (global) attachment circuit by its name is required. These typedefs SHOULD be used when other modules need a reference to one of these profiles or attachment circuits.

### Attachment Circuits Profiles {#sec-acp}

The 'ac-group-profile' defines reusable parameters for a set of ACes. Each profile is identified by 'name'. Some of the data nodes can be adjusted at the 'ac'.
These adjusted values take precedence over the global values.  The structure of 'ac-group-profile' is similar to the one used to model each 'ac' ({{ac-svc-tree}}).

### AC Placement Constraints {#sec-pc}

The 'placement-constraints' specifies the placement constraints of an AC. For example, this container can be used to request avoiding to connecting two ACes to the same PE. The full set of supported constraints is defined in {{!RFC9181}} (see 'placement-diversity', in particular).

The structure of 'placement-constraints' is shown in {{precedence-tree}}.

~~~~
{::include ./yang/subtrees/precedence-stree.txt}
~~~~
{: #precedence-tree title="Overall Attachment Circuits Tree Structure" artwork-align="center"}

### Attachment Circuits

The structure of 'attachment-circuits' is shown in {{ac-svc-tree}}.

~~~~
{::include ./yang/subtrees/overall-ac-stree.txt}
~~~~
{: #ac-svc-tree title="Overall Attachment Circuits Tree Structure" artwork-align="center"}

The description of the data nodes is as follows:

'customer-name':
: Indicates the name of the customer who ordered the AC.

'description':
: Includes a textual description of the AC.

'peer-sap-id':
: Includes references to the remote endpoints of an attachment circuit {{?I-D.ietf-opsawg-sap}}.

'ac-group-profile':
: Indicates references to one or more profils that are defined in {{sec-acp}}.

'group':
: Lists the groups to which an AC belongs {{!RFC9181}}. For example, the 'group-id' is used to associate redundancy or protection constraints of ACes. An example is provided in {{sec-ex-prec}}.

'name':
: Associates a name that uniquely identifies an AC within a service provider network.

'l2-connection':
: See {{sec-l2}}.

'l3-connection':
: See {{sec-l3}}.

'routing':
: See {{sec-rtg}}.

'oam':
: See {{sec-oam}}.

'security':
: See {{sec-sec}}.

#### Layer 2 Connection Structure {#sec-l2}

The 'l2-connection' container ({{l2-svc-tree}}) is used to configure the relevant Layer 2 properties of an AC including: encapsulation details and tunnel terminations. For the encapsulation details, the model supports the definition of the type as well as the Identifiers (e.g., VLAN-IDs) of each of the encapsulation-type defined. For the second case, attributes for pseudowire, Virtual Private LAN Service (VPLS), and  Virtual eXtensible Local Area Network (VXLAN) tunnel terminations are included. This structure relies upon the common groupings defined in {{!I-D.boro-opsawg-teas-common-ac}}.

~~~~
{::include ./yang/subtrees/l2-stree.txt}
~~~~
{: #l2-svc-tree title="Layer 2 Connection Tree Structure" artwork-align="center"}



#### Layer 3 Connection Structure {#sec-l3}

The 'l3-connection' container is used to configure the relevant Layer 3 properties of an AC. The model supports the usage of dynamic and static addressing. This structure relies upon the common groupings defined in {{!I-D.boro-opsawg-teas-common-ac}}. Both IPv4 and IPv6 parameters are supported.

{{ipv4-svc-tree}} shows the structure of the IPv4 connection.

~~~~
{::include ./yang/subtrees/ipv4-stree.txt}
~~~~
{: #ipv4-svc-tree title="Layer 3 Connection Tree Structure (IPv4)" artwork-align="center"}

{{ipv6-svc-tree}} shows the structure of the IPv6 connection.

~~~~
{::include ./yang/subtrees/ipv6-stree.txt}
~~~~
{: #ipv6-svc-tree title="Layer 3 Connection Tree Structure (IPv6)" artwork-align="center"}

#### Routing {#sec-rtg}

As shown in the tree depicted in {{rtg-svc-tree}}, the 'routing-protocols' container defines the required parameters to enable the desired routing features for an AC. One or more routing protocols can be associated with an AC.  Such routing protocols will be then enabled between a PE and the customer terminating points. Each routing instance is uniquely identified by the combination of the 'id' and 'type' to accommodate scenarios where multiple instances of the same routing protocol have to be configured on the same link.

In addition to static routing, the module supports BGP, OSPF, IS-IS, and RIP. It also includes a reference to the 'routing-profile-identifier' defined in {{sec-profiles}}, so that additional constraints can be applied to a specific instance of each routing protocol.

~~~~
{::include ./yang/subtrees/rtg-stree.txt}
~~~~
{: #rtg-svc-tree title="Routing Tree Structure" artwork-align="center"}

##### Static Routing

The static tree structure is shown in {{static-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/static-rtg-stree.txt}
~~~~
{: #static-rtg-svc-tree title="Static Routing Tree Structure" artwork-align="center"}

##### BGP

The BGP tree structure is shown in {{bgp-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/bgp-rtg-stree.txt}
~~~~
{: #bgp-rtg-svc-tree title="BGP Tree Structure" artwork-align="center"}

Similar to {{?RFC9182}}, this version of the ACaaS assumes that parameters specific to the TCP-AO are preconfigured as part of the key chain that is referenced in the ACaaS. No assumption is made about how such a key chain is preconfigured. However, the structure of the key chain should cover data nodes beyond those in {{!RFC8177}}, mainly SendID and RecvID (Section 3.1 of {{?RFC5925}}).

##### OSPF

The OSPF tree structure is shown in {{ospf-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/ospf-rtg-stree.txt}
~~~~
{: #ospf-rtg-svc-tree title="OSPF Tree Structure" artwork-align="center"}

#### IS-IS

The IS-IS tree structure is shown in {{isis-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/isis-rtg-stree.txt}
~~~~
{: #isis-rtg-svc-tree title="IS-IS Tree Structure" artwork-align="center"}

#### RIP

The RIP tree structure is shown in {{rip-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/rip-rtg-stree.txt}
~~~~
{: #rip-rtg-svc-tree title="RIP Tree Structure" artwork-align="center"}

'address-family' indicates whether IPv4, IPv6, or both address families are to be activated. For example, this parameter is used to determine whether RIPv2 {{?RFC2453}}, RIP Next Generation (RIPng), or both are to be enabled {{?RFC2080}}.

#### VRRP

The model also supports the Virtual Router Redundancy Protocol (VRRP) {{?RFC5798}} on an AC ({{vrrp-rtg-svc-tree}}).

~~~~
{::include ./yang/subtrees/vrrp-rtg-stree.txt}
~~~~
{: #vrrp-rtg-svc-tree title="VRRP Tree Structure" artwork-align="center"}

#### OAM {#sec-oam}

As shown in the tree depicted in {{oam-svc-tree}}, the 'oam' container defines OAM-related parameters of an AC.

~~~~
{::include ./yang/subtrees/oam-stree.txt}
~~~~
{: #oam-svc-tree title="OAM Tree Structure" artwork-align="center"}

#### Security {#sec-sec}

As shown in the tree depicted in {{sec-svc-tree}}, the 'security' container defines a set of AC security parameters.

~~~~
{::include ./yang/subtrees/security-stree.txt}
~~~~
{: #sec-svc-tree title="Security Tree Structure" artwork-align="center"}

# YANG Modules

## The Bearer Service ("ietf-bearer-svc") YANG Module

This module uses types defined in {{!RFC6991}} and {{!RFC9181}}.

~~~~~~~~~~
<CODE BEGINS> file ietf-bearer-svc@2022-11-30.yang
{::include ./yang/ietf-bearer-svc.yang}
<CODE ENDS>
~~~~~~~~~~

## The AC Service ("ietf-ac-svc") YANG Module

This module uses types defined in {{!RFC6991}}, {{!RFC9181}}, {{!RFC8177}}, and {{!I-D.boro-opsawg-teas-common-ac}}.

~~~~~~~~~~
<CODE BEGINS> file ietf-ac-svc@2022-11-30.yang
{::include ./yang/ietf-ac-svc.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

   The YANG modules specified in this document define schema for data
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

   There are a number of data nodes defined in these YANG modules that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   These are the subtrees and data nodes and their sensitivity/
   vulnerability in the "ietf-ac-svc" module:

   * TBC
   * TBC

   Some of the readable data nodes in these YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability in the "ietf-ac-svc" module:

   'customer-name', 'l2-connection', and 'ip-connection':
   : An attacker can retrieve privacy-related information, which can be used to track a
      customer.  Disclosing such information may be considered a
      violation of the customer-provider trust relationship.

   'keying-material':
   : An attacker can retrieve the cryptographic keys
      protecting the underlying connectivity services (routing, in
      particular).  These keys could be used to inject spoofed routing
      advertisements.

   Several data nodes ('bgp', 'ospf', 'isis', and 'rip') rely
   upon {{!RFC8177}} for authentication purposes.  As such, the AC service module
   inherits the security considerations discussed in Section 5 of
   {{!RFC8177}}.  Also, these data nodes support supplying explicit keys as
   strings in ASCII format.  The use of keys in hexadecimal string
   format would afford greater key entropy with the same number of key-
   string octets.  However, such a format is not included in this
   version of the AC service model, because it is not supported by the underlying
   device modules (e.g., {{?RFC8695}}).


# IANA Considerations

   IANA is requested to register the following URIs in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-bearer-svc
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.

   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG modules in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

~~~~
   Name:  ietf-bearer-svc
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-bearer-svc
   Prefix:  bearer-svc
   Reference:  RFC xxxx

   Name:  ietf-ac-svc
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Prefix:  ac-svc
   Reference:  RFC xxxx
~~~~

--- back

# Examples {#examples}

This section includes a non-exhaustive list of examples to illustrate the use of the service models defined in this document.

## Request A New Bearer {#ex-create-bearer}

An example of a request message body to create a bearer is shown in {{create-bearer}}.

~~~~
{::include ./json-examples/simple-bearer-create.json}
~~~~
{: #create-bearer title="Example of a Message Body to Create A New Bearer"}

A bearer-reference is then generated by the controller for this bearer. {{get-bearer}} shows the example of a response message body that is sent by the controller to reply to a GET request:

~~~~
{::include ./json-examples/get-bearer-reference.json}
~~~~
{: #get-bearer title="Example of a Response Message Body with the Bearer Reference"}

## Request An AC over An Existing Bearer {#ac-bearer-exist}

An example of  a request message body to create a simple AC over an existing bearer is shown in {{ac-b}}. The bearer reference is assumed to be known to both the customer and the network provider. Such a reference can be retrieved, e.g., following the example described in {{ex-create-bearer}} or using other means (including, exchanged out-of-band or via proprietary APIs).

~~~~
{::include ./json-examples/simple-ac-existing-bearer.json}
~~~~
{: #ac-b title="Example of a Message Body to Request an AC over an Existing Bearer"}

## Request An AC for a Known Peer SAP {#ac-no-bearer-peer-sap}

An example of a request to create a simple AC, when the peer SAP is known, is shown in {{ac-known-ps}}. In this example, the peer SAP identifier points to an identifier of a service function. The (topological) location of that service function is assumed to be known to the network controller. For example, this can be determined as part of an on-demand procedure to instantiate a service function in a cloud. That instantiated service function can be granted a connectivity service via the provider network.

~~~~
{::include ./json-examples/simple-ac-known-peer-sap.json}
~~~~
{: #ac-known-ps title="Example of a Message Body to Request an AC with a Peer SAP"}

## One CE, Two ACs {#sec-ex-one-ce-multi-acs}

Let’s consider the example of an eNodeB (CTP) that is directly connected to the access routers of the mobile backhaul (see {{enodeb}}). In this example, two ACs are needed to service the eNodeB.

~~~~ aasvg
+-------------+                  +------------------+
|             |                  | PE               |
|             |                  |  192.0.2.1       |
|   eNodeB    |==================|  2001:db8::1     |
|             |          vlan 1  |                  |
|             |==================|                  |
|             |          vlan 2  |                  |
|             | Direct           |                  |
+-------------+ Routing          |                  |
                                 |                  |
                                 |                  |
                                 |                  |
                                 +------------------+
~~~~
{: #enodeb title="Example of a CE-PE ACs"}

An example of a request to create the ACs to service the eNodeB is shown in {{two-acs-same-ce}}. This example assumes that static addressing is used for both ACs.

~~~~
{::include ./json-examples/two-acs-same-ce.json}
~~~~
{: #two-acs-same-ce title="Example of a Message Body to Request Two ACes on The Same Link"}

## Control Precedence over Multiple ACs {#sec-ex-prec}

When multiple ACs are requested by the same customer (for the same site), the request can tag one of these ACes as "primary" and the other ones as "secondary". An example of such a request is shown in {{ac-precedence}}. In this example, both ACes are bound to the same "group-id", and the "precedence" data node is set as a function of the intended role of each AC (primary or secondary).

~~~~
{::include ./json-examples/ac-precedence.json}
~~~~
{: #ac-precedence title="Example of a Message Body to Associate a Precedence Level with ACes"}


## Illustrate the Use of Global Profiles

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce-profile}}. Unlike {{two-acs-same-ce}}, this example factorizes some of the redundant data.

~~~~
{::include ./json-examples/two-acs-same-ce-profile.json}
~~~~
{: #two-acs-same-ce-profile title="Example of a Message Body to Request Two ACes on The Same Link (Global Profile)"}

## Illustrate the Use of Per-Node Profiles

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce-node-profile}}. Unlike {{two-acs-same-ce}}, this example factorizes all redundant data.

~~~~
{::include ./json-examples/two-acs-same-ce-node-profile.json}
~~~~
{: #two-acs-same-ce-node-profile title="Example of a Message Body to Request Two ACes on The Same Link (Node Profile)"}

A customer may request adding a new AC by simply referring to an existing per-node AC profile as shown in {{add-ac-same-ce-node-profile}}. This AC inherits all the data that was enclosed in the indicated per-node AC profile (IP addressing, routing, etc.).

~~~~
{::include ./json-examples/add-ac-same-ce-node-profile.json}
~~~~
{: #add-ac-same-ce-node-profile title="Example of a Message Body to Add a new AC over an existing link (Node Profile)"}

## Multiple CEs

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
{: #multiple-sites title="Example of a Message Body of a Request to Create Multiple ACs bound to Multiple CEs"}

## Binding Attachment Circuits to an IETF Network Slice {#sec-ex-slice}

This example shows how the AC service model complements {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} to connect a site to a slice service.

First, {{slice-vlan-1}} describes the end-to-end network topology as well the orchestration scopes:

- The topology is made up of two sites (site1 and site2), interconnected via a Transport Network (e.g. IP/MPLS Network). A Network Function is deployed within each site in a dedicated IP Subnet.
- A 5G SMO is responsible for the deployment Network Functions and the indirect management of a local Gateway (i.e., CE device).
- An IETF Network Slice Controller is responsible for the deployment of IETF Network Slices across the TN.

Network Functions are deployed within each site.

~~~~
{::include ./figures/drawing-slice-1.fig}
~~~~
{: #slice-vlan-1 title="An Example of a Network Topology Used to Deploy Slices"}

{{slice-vlan-2}} describes the logical connectivity enforced thanks to both IETF Network Slice and Attachment Circuit models.

~~~~
{::include ./figures/drawing-slice-2.fig}
~~~~
{: #slice-vlan-2 title="Logical Overview"}

{{slice-acs}} shows the message body of the request to create the required ACs using the Attachment Circuit module.

~~~~
{::include-fold ./json-examples/acs-for-slices.json}
~~~~
{: #slice-acs title="Message Body of a Request to Create Required ACs"}

{{slice-prov}} shows the message body of the request to create the a slice service bound to the ACs created using {{slice-acs}}. Only references to these ACs are included in the Slice Service request. This example assumes that the module that "glues" the service/AC is also supported by the NSC.

~~~~
{::include-fold ./json-examples/slice-provisionning.json}
~~~~
{: #slice-prov title="Message Body of a Request to Create a Slice Service Referring to the ACs"}

## Connecting a Virtualized Environment Running in a Cloud Provider {#sec-ex-cloud}

This example ({{cloud-provider-1}}) shows how the AC service model can be used to connect a Cloud Infrastructure to a service provider network. This example makes the following assumptions:

1.	A customer (e.g., Mobile Network Team or partner) has a virtualized infrastructure running in a Cloud Provider. A simplistic deployment is represented here with a set of Virtual Machines running in a Virtual Private Environment. The deployment and management of this infrastructure is achieved via private APIs that are supported by the Cloud Provider: this realization is out of the scope of this document.

1.	The connectivity to the Data Center is achieved thanks to a service based on direct attachment (physical connection), which is delivered upon ordering via an API exposed by the Cloud Provider. When ordering that connection, a unique "Connection Identifier" is generated and returned via the API.

1.	The customer provisions the networking logic within the Cloud Provider based on that unique connection Identifier (i.e., logical interfaces, IP addressing, and routing).


~~~~
{::include ./figures/drawing-cp-1.fig}
~~~~
{: #cloud-provider-1 title="An Example of Realization for Connecting a Cloud Site"}

{{cloud-provider-2}} illustrates the pre-provisioning logic for the physical connection to the Cloud Provider. After this connection is delivered to the service provider, the network inventory is updated with "bearer-reference" set to the value of the "Connection Identifier".

~~~~ aasvg
  Customer                                                       Cloud
Orchestration  DIRECT INTERCONNECTION ORDERING (API)            Provider
               ──────────────────────────────────────────────►

               Connection Created with "Connection ID:1234-56789
               ◄───────────────────────────────────────────────
                                        x
                                        x
                                        x
                                        x

       Physical Connection 1234-56789 is delivered and
                         connected to PE1

       Network  Inventory Upated with:
         bearer-reference: 1234-56789 for PE1/Interface If-A
~~~~
{: #cloud-provider-2 title="Illustration of Pre-provisioning"}

Next, API workflows can be initiated:

* Cloud Provider for the configuration as per (3) above.
* Service provider network via the Attachment Circuit model. This request can be used in conjunction with additional requests based on L3SM (VPN provisioning) or Network Slice Service model (5G hybrid Cloud deployment).

{{cloud-provider-ac}} shows the message body of the request to create the required ACs to connect the Cloud Provider Virtualized (VM) using the Attachment Circuit module. Note that this Cloud Provider mandates the use of MD5 authentication for establishing BGP connections.

> The module supports MD5 to basically accommodate the installed BGP base (including by some Cloud Providers). Note that MD5 suffers from the security weaknesses discussed in Section 2 of {{?RFC6151}} and Section 2.1 of {{?RFC6952}}.

~~~~
{::include-fold ./json-examples/cloud-provider.json}
~~~~
{: #cloud-provider-ac title="Message Body of a Request to Create the ACs for Connecting to the Cloud Provider"}

# Acknowledgments
{:numbered="false"}

Thanks to Kenichi Ogaki for the comments.
