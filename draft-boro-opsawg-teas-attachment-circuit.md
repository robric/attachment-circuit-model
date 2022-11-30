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
area: OPS
workgroup: OPSAWG Working Group
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


# Sample Uses of the Attachment Circuit Data Model

# Description of the Attachment Circuit YANG Module

## Overall Structure of the Module

TBC TBC

~~~~
xxxx
~~~~
{: #tree title="Overall Tree Structure"}

## XXXX

## XXXX

#  Attachment Circuit YANG Module

This module uses types defined in XXX.

~~~~
<CODE BEGINS> file "ietf-ac@2022-11-30.yang"
module ietf-ac {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:ietf-ac";
  prefix ac;

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
         "RFC xxxx: A YANG Data Model for Attachment Circuits"";
     }

  TBC
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
