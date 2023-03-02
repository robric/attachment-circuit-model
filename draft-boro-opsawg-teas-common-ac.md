---
title: "A Common YANG Data Model for Attachment Circuits"
abbrev: "Common Attachment Circuit YANG"
category: std

docname: draft-boro-opsawg-teas-common-ac-latest
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

normative:
  ISO10589:
    title: Information technology - Telecommunications and information exchange between systems - Intermediate System to Intermediate System intra-domain routeing information exchange protocol for use in conjunction with the protocol for providing the connectionless-mode network service (ISO8473)
    author:
      org: ISO
    date: 2002
    target: https://www.iso.org/standard/30932.html

informative:


--- abstract

The document specifies a common Attachment Circuits (ACs) YANG module, which is designed with the intent to be reusable. Whether a service model reuses structures defined in the AC models or simply include an AC reference is a design choice of these service models. Relying upon the AC service model to manage ACs over which a service is delivered has the merit to decorrelate the management of a service vs. upgrade the AC components to reflect recent AC technologies or features.

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

The model also include a common module ("ietf-ac-common") which is designed with the intent to be reusable. Likewise, the "ietf-ac-svc" includes a set of reusable groupings. Whether a service model reuses structures defined in the "ietf-ac-svc" or simply includes an AC reference (that was communicated during AC service instantiation) is a design choice of these service models. Relying upon the AC service model to manage ACes over which services are delivered has the merit to decorrelate the management of the (core) service vs. upgrade the AC components to reflect recent AC technologies or new features (e.g., new encryption scheme, additional routing protocol). **This document favors the approach of completely relying upon the AC service model instead of duplicating into specific modules of advanced services that are delivered over an Attachment Circuit.**


The YANG data models in this document conform to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.


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
: A network that is able to provide network services (e.g., Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., Network Slice Services).

# Sample Uses of the Data Models

## ACs Terminated by One or Multiple Customer Devices

{{uc}} depicts two target topology flavors that involve ACs. These topologies are characterized as follows:

* A Customer Terminating Point (CTP) may be a physical node or a logical entity. A CTP is seen by the network as a peer SAP.

* The same AC request may include one or multiple ACs that may belong to one or both of these flavors. For the sake of simplfying the illustration, only a subset of these ACs is shown in {{uc}}.

* CTPs may be dedicated to one single service or host multiple services (e.g., service functions {{?RFC7665}}).

* A single AC (as seen by a network provider) may be bound to one or multiple peer SAPs (e.g., CTP#1 and CTP#2). For example, and as discussed in {{!RFC4364}}, multiple CTPs (CEs) can be attached to a PE over the same attachment circuit. This is typically implemented if the layer 2 infrastructure between the CTP and the network provides a multipoint service.

* The same CTP may terminate multiple ACs. These ACes may be over the same or distinct bearers.

* The customer may request protection schemes where the ACs bound to a customer endpoints are terminated by the same PE (e.g., CTP#3), distinct PEs (e.g., CTP#34), etc.

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

# Description of the AC Common YANG Module

## Tree Structure

The full tree of the "ietf-ac-common" module is shown in {{ac-common-full-tree}}.

~~~~
{::include ./yang/full-trees/ac-common-with-groupings.txt}
~~~~
{: #ac-common-full-tree title="AC Common Full Tree Structure" artwork-align="center"}

# Common Attachment Circuit YANG Module

This module uses types defined in {{!RFC6991}}, {{!RFC8177}}, and  {{!RFC9181}}.

~~~~~~~~~~
<CODE BEGINS> file "ietf-ac-common@2022-11-30.yang"
{::include ./yang/ietf-ac-common.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

   The YANG module specified in this document defines schema for data
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

   The "ietf-ac-common" module defines a set of identities, types, and
   groupings.  These nodes are intended to be reused by other YANG
   modules.  The module by itself does not expose any data nodes that
   are writable, data nodes that contain read-only state, or RPCs.  

   YANG modules that use the groupings that are defined in this document
   should identify the corresponding security considerations.  For
   example, reusing some of these groupings will expose privacy-related
   information (e.g., 'ipv6-lan-prefixes' or 'ipv4-lan-prefixes').  Disclosing such information may
   be considered a violation of the customer-provider trust
   relationship.

   Several grouping ('bgp-authentication', 'ospf-authentication', 'isis-authentication', and 'rip-authentication') rely
   upon {{!RFC8177}} for authentication purposes.  As such, modules that will reuse these grouping
   will inherit the security considerations discussed in Section 5 of
   {{!RFC8177}}.  Also, these groupings support supplying explicit keys as
   strings in ASCII format.  The use of keys in hexadecimal string
   format would afford greater key entropy with the same number of key-
   string octets.  However, such a format is not included in this
   version of the common AC model, because it is not supported by the underlying
   device modules (e.g., {{?RFC8695}}).


# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-common
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

~~~~
   Name:  ietf-ac-common
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-common
   Prefix:  ac-common
   Reference:  RFC xxxx
~~~~

--- back

# Acknowledgments
{:numbered="false"}

TBC.