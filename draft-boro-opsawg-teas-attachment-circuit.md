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
workgroup: "Operations and Management Area Working Group"
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

informative:


--- abstract

This document specifies a YANG service data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., Network Slice Service).

--- middle

# Introduction

This document specifies a YANG service data model for attachment circuits. The model can be used for the provisioning attachment circuits prior or during service provisioning (e.g., Network Slice Service).

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

## Examples

## Request An AC over An Existing Bearer

An example of a request to create a simple AC over an existing bearer is shown in {{ac-b}}.

~~~~
{::include ./json-examples/simple-ac-existing-bearer.json}
~~~~
{: #ac-b title="Example of a Message Body to Request an AC over an Existing Bearer"}


## Request An AC for a Knwon Peer SAP

An example of a request to create a simple AC, when the peer SAP is known, is shown in {{ac-known-ps}}.

~~~~
{::include ./json-examples/simple-ac-known-peer-sap.json}
~~~~
{: #ac-known-ps title="Example of a Message Body to Request an AC with a Peer SAP"}

## One CE, Two ACs

An example of a request to create two ACs to service the same CE on the same link is shown in {{two-acs-same-ce}}. This example assumes that static addressing is used for the ACs.

~~~~
{::include ./json-examples/two-acs-same-ce.json}
~~~~
{: #two-acs-same-ce title="Example of a Message Body to Request Two ACes on The Same Link"}

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

A customer may request adding a new AC by simply referring to an existing per-node AC profile as shown in {{add-ac-same-ce-node-profile}}. This AC inherites all the data that was enclosed in the indicated per-node AC profile (IP addressing, routing, etc.).

~~~~
{::include ./json-examples/add-ac-same-ce-node-profile.json}
~~~~
{: #add-ac-same-ce-node-profile title="Example of a Message Body to Add a new AC over an existing link (Node Profile)"}

## Multiple CEs

{{network-example}} shows an example of CEs that are interconnected by a service provider network.

~~~~

                   +-----+   +--------------+   +-----+
      +----+       | PE1 |===|              |===| PE3 |       +----+
      | CE1+-------+     |   |              |   |     +-------+ CE3|
      +----+       +-----+   |              |   +-----+       +----+
                             |     Core     |
      +----+       +-----+   |              |   +-----+       +----+
      |CE2 +-------+     |   |              |   |     +-------+ CE4|
      +----+       | PE2 |===|              |===| PE4 |       +----+
                   +-----+   +--------------+   +-----+
~~~~
{: #network-example title="Network Topology Example"}

{{multiple-sites}} depicts an example of the message body of a request to instantiate these the various ACs that are shown in  {{network-example}}.

~~~~
{::include ./json-examples/multiple-ce-with-profile.json}
~~~~
{: #multiple-sites title="Example of a Message Body to of Creating Multiple ACs bound to Multiple CEs"}

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
     +--rw ac* [id]
        ...
~~~~
{: #gp-svc-tree title="Service Proviles"}

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
{::include ./yang/ac-svc-without-groupings.txt}
~~~~
{: #d-svc-tree title="AC Service Tree Structure"}


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
