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
  RFC5440:

informative:
  RFC8664:
  RFC8408:
  RFC8231:


--- abstract

This document describes extensions to Path Computation Element Protocol (PCEP) to permit a PCEP Speaker to update information previously exchanged during PCEP Open procedures.


--- middle

# Introduction

The Path Computation Element Communication Protocol (PCEP) [RFC5440] provides mechanisms for Path Computation Elements (PCEs) to perform path computations in response to requests from Path Computation Clients (PCCs).

[RFC5440] outlines the message exchange procedures that PCEP Speakers must follow upon initial connection to establish a PCEP Peer relationship. This procedure includes sending an Open message containing an OPEN Object, which conveys various session characteristics such as protocol timers. The OPEN Object can be extended with TLVs to convey additional session characteristics, such as PCE capabilities (e.g., [RFC8408]) or specific values and ranges (e.g., [RFC8664] and [draft-ietf-pce-controlled-id-space]). This information is exchanged only once per session and cannot be dynamically modified without tearing down and re-establishing the PCEP session, which can be operationally disruptive.

Additionally, [RFC5440] describes a Notification Message (PCNtf) containing a NOTIFICATION Object, which a PCEP Speaker may use to notify the other speaker of an event.

This document proposes a generic mechanism that allows a PCEP Speaker to update previously exchanged Open Message information using a PCNtf Message, known as an Open Refresh. This approach mitigates the need to tear down the session to modify any exchanged information.

Note that [draft-ietf-pce-state-sync] also proposes using PCNtf Message to relay PcOpen messages between PCEs about each PCE's connected peers. It is anticipated that [draft-ietf-pce-state-sync] will be defined in parallel, with unique object definitions, as the semantics of a PCEP Speaker exchanging its own information differ from exchanging information related to a connected peer.

This documented describes a generic extension and mechanism to update PC Open content but future documents MAY describe further semantics on a per TLV basis.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

Open Refresh : The act of modifying content previously exchanged during PCEP Open message in an ad-hoc manner without tearing down the PCEP session.

# Operational considerations

This section discusses some high level considerations that should be considered when supporting Open Refresh. While scenarios described here exist in present day PCEP, they require explicitly tearing down the PCEP session which gives a clear indication of potential system impact. With ad-hoc manipulation of open information, the impact of a possible change may not be evident so this section attempts to describe some of these considerations.

## Capability change

One use case of PcOpen is to exchange device software capabilities and feature enablement to determine whether a PCEP Speaker may support a given operation defined in PCEP extensions. If a PCEP speaker supports removal of a capability using Open Refresh, then all state related to the capability MUST be reset and removed and MUST follow the guidelines set out by the capability should the other PCEP no longer support the capability. This may impact device wide state and network traffic. For example, [RFC8281] defines the STATEFUL-PCE-CAPABILITY-TLV to indicate support for PCE-Initiated LSPs. The removal of this capability would result in PCE-Initiated LSPs being deleted from each PCEP Speaker.

## Node-wide property change

One use case of PcOpen is to exchange device-level configurations or settings. In the case of statefully delegated LSPs ([RFC8231], the modification of these values may trigger path calculations for established LSP Objects and/or the possibility of LSP tear down.

# Open Refresh Procedures

## Capability Advertisement

A PCEP Speaker indicates support of Open Refresh during the PCEP Initialization phase ([RFC5440]). As per RFC5440, a PCEP Speaker MUST send a PCEP Open message with exactly one OPEN object. The PCEP speaker indicates support for Open Refresh by setting the OPEN-REFRESH (R-Bit) to 1 in the PCEP Open Message Flags field.

IANA is requested to allocate the R-Bit from the Open Object Flag Field registry.

* R (OPEN-REFRESH-CAPABILITY - 1 bit - TBD1): If set to 1 by a PCEP speaker, the PCEP speaker indicates that the session supports receiving an Open Refresh message.

If a PCEP speaker receives an Open message which does not contain the OPEN-REFRESH-CAPABILITY, the PCEP Speaker MUST NOT send Open Refresh messages to the remote speaker.

## PcNotify message

An Open Refresh is transmitted by sending a PCNtf Message ([RFC5440]) containing a NOTIFICATION Object with Notification-type=TBD2 (Open-Refresh).

// TODO below, on the fence about it. Could use the value to indicate add/remove/modify of a given TLV rather than sending a full snapshot.
// TODO But i'm on the fence if it would be better or worse to encode a snapshot vs individual diff changes. Open to supporting signalling add/remove/modify.

The Open-Refresh NOTIFICATION Object Notification-value MUST be set to zero.

The Open-Refresh NOTIFICATION Object encodes any TLV which may be encoded in an OPEN Object.

The Open-Refresh NOTIFICATION Object contains a snapshot of all unmodified and modified TLVs.

Upon receiving an Open-Refresh NOTIFICATION Object, the PCEP Speaker compares the newly received TLVs with the previously received TLVs to determine what has changed. An omission of a TLV MUST be treated as a removal of the TLV and perform necessary side effect operations as if the TLV was never exchanged during PcOpen.

If the PCEP Speaker determines it cannot support the Open-Refresh differential change(s), the PCEP Speaker generates a PCEP Error (PCErr) with Error-type=TBD3 (Unsupported-Open-Refresh) and error-value TBD4 and it SHOULD terminate the PCEP session.


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
