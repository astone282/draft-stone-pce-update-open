---
title: "PCEP extensions for updating Open Message content"
abbrev: "PCEP-UPDATE-OPEN"
category: std

docname: draft-stone-pce-update-open-latest
submissiontype: IETF
number:
date: {DATE}
consensus: true
v: 3
area: "Routing"
workgroup: "Path Computation Element"

author:
 -
    fullname: Andrew Stone
    organization: Nokia
    email: andrew.stone@nokia.com
 -
    fullname: Cheng Li
    organization: Huawei
    email: c.l@huawei.com
 -
    fullname: Samuel Sidor
    organization: Cisco
    email: ssidor@cisco.com

normative:

informative:


--- abstract

This document describes extensions to Path Computation Element Protocol (PCEP) to permit a PCEP Speaker to update information previously exchanged during PCEP Open procedures.


--- middle

# Introduction

The Path Computation Element Communication Protocol (PCEP) [RFC5440] provides mechanisms for Path Computation Elements (PCEs) to perform path computations in response to requests from Path Computation Clients (PCCs).

[RFC5440] outlines the message exchange procedures that PCEP Speakers must follow upon initial connection to establish a PCEP Peer relationship. This procedure includes sending an Open message containing an OPEN Object, which conveys various session characteristics such as protocol timers. The OPEN Object can be extended with TLVs to convey additional session characteristics, such as PCE capabilities (e.g., [RFC8408]) or specific values and ranges (e.g., [RFC8664] and [draft-ietf-pce-controlled-id-space]). This information is exchanged only once per session and cannot be dynamically modified without tearing down and re-establishing the PCEP session, which can be operationally disruptive.

Additionally, [RFC5440] describes a Notification message (PcNtf) containing a NOTIFICATION Object, which a PCEP Speaker may use to notify the other speaker of an event.

This document proposes a generic mechanism that allows a PCEP Speaker to update previously exchanged Open Message information using a PcNtf message. This approach mitigates the need to tear down the session to modify any exchanged information.

Note that [draft-ietf-pce-state-sync] also proposes using PcNtf to relay PcOpen messages between PCEs about each PCE's connected peers. It is anticipated that [draft-ietf-pce-state-sync] will be defined in parallel, with unique object definitions, as the semantics of a PCEP Speaker exchanging its own information differ from exchanging information related to a connected peer.



# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Operational considerations

todo

# PcNtf Message procedures

todo2

# Security Considerations

TODO Security

# Implementation Status
[Note to the RFC Editor - remove this section before publication, as well as remove the reference to RFC 7942.]

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in [RFC7942]. The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs. Please note that the listing of any individual implementation here does not imply endorsement by the IETF. Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors. This is not intended as, and must not be construed to be, a catalog of available implementations or their features. Readers are advised to note that other implementations may exist.

According to [RFC7942], "this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature. It is up to the individual working groups to use this information as they see fit".




# IANA Considerations

TODO IANA


--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge Dhruv Dhody for his discussion and ideas related to this document.
