---
title: "PCE Communication Protocol (PCEP) extensions for updating Open Message content"
abbrev: "PCEP-UPDATE-OPEN"
category: std

docname: draft-stone-pce-update-open-latest
submissiontype: IETF
number:
date: {DATE}
consensus: true
v: 3
area: "Routing"
workgroup: "PCE Working Group"

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
  RFC8281:
  I-D.ietf-pce-controlled-id-space:
  I-D.ietf-pce-state-sync:


--- abstract

This document describes extensions to the Path Computation Element (PCE) Communication Protocol (PCEP) to permit a PCEP Speaker to update information previously exchanged during PCEP session establishment via the Open message.


--- middle

# Introduction

The Path Computation Element (PCE) Communication Protocol (PCEP) {{RFC5440}} provides mechanisms for PCEs to perform path computations in response to requests from Path Computation Clients (PCCs).

{{RFC5440}} outlines the message exchange procedures that PCEP Speakers must follow upon initial connection to establish a PCEP Session. This procedure includes sending an Open Message containing an OPEN Object, which conveys various session characteristics such as protocol timers. The OPEN Object can be extended with TLVs to convey additional session characteristics, such as PCE capabilities (e.g., {{RFC8408}}) or specific values and ranges (e.g., {{RFC8664}} and {{I-D.ietf-pce-controlled-id-space}}). This information is exchanged only once per session and cannot be dynamically modified without tearing down and re-establishing the PCEP session, which can be operationally disruptive.

Additionally, {{RFC5440}} specify a Notification Message (PCNtf) containing a NOTIFICATION Object, which a PCEP Speaker may use to notify the other speaker of an event.

This document proposes a generic mechanism allowing a PCEP Speaker (PCC or PCE) to update previously exchanged Open Message information using a PCNtf Message with a new notification called "Open Refresh". This approach mitigates the need to terminate the session to modify any exchanged information.

Note that {{I-D.ietf-pce-controlled-id-space}} also proposes using PCNtf Message to relay Open Messages between PCEs about each PCE's connected peers. It is anticipated that {{I-D.ietf-pce-state-sync}} will use a similar notification mechanism with a unique notification type, as the semantics of a PCEP Speaker exchanging its information differ from exchanging information related to a connected state-sync PCEs.

This document describes a generic extension and mechanism to update Open Object content but future documents MAY describe further semantics on a per TLV basis.

## Requirements Language

{::boilerplate bcp14-tagged}

# Terminology

Open Refresh: The act of modifying content previously exchanged during PCEP Open Message in an ad-hoc manner without terminating the PCEP session.

Open Refresh Notification message: An Open Refresh notification message is a new Notification Type to be used in the PCNtf Message ({{RFC5440}}) that will also contain the open information to be refreshed.

# Operational Considerations

This section discusses some high-level considerations that should be considered when supporting Open Refresh. While scenarios described here exist in present-day PCEP, they require explicitly tearing down the PCEP session which gives a clear indication of potential system impact. With ad-hoc manipulation of open information, the impact of a possible change may not be evident so this section attempts to describe some of these considerations.

## Capability Change

One use case of the Open message is to exchange device software capabilities and feature enablement to determine whether a PCEP Speaker may support a given operation defined in PCEP extensions. If a PCEP speaker supports removal of a capability using Open Refresh, then all states related to the capability MUST be reset and removed and MUST follow the guidelines set out by the capability should the other PCEP speaker no longer support the capability. This may impact device-wide state and network traffic. For example, {{RFC8281}} defines the STATEFUL-PCE-CAPABILITY-TLV to indicate support for PCE-Initiated LSPs. The removal of this capability would result in PCE-Initiated LSPs being deleted from each PCEP Speaker.

## Node-wide Property Change

One use case of the Open message is to exchange device-level configurations or settings. In the case of statefully delegated LSPs ({{RFC8231}}, the modification of these values may trigger path calculations for established LSPs with a possibility of LSP tear down.

## Frequency of Open Refresh

A PCEP implementation should consider the impact on the entire network before sending frequent Open Refresh Notifications. It could consider applying some form of dampening or back-off mechanism.  

# Procedures

## Capability Advertisement

A PCEP Speaker indicates support of Open Refresh during the PCEP Initialization phase ({{RFC5440}}). As per {{RFC5440}}, a PCEP Speaker sends an Open Message with exactly one OPEN object. This document defines a new flag, OPEN-REFRESH-CAPABILITY (R-bit), in the Open Message Flags field to indicate the support of the Open Refresh feature.

* R (OPEN-REFRESH-CAPABILITY - 1 bit - TBD1): If set to 1 by a PCEP speaker, the PCEP speaker indicates that the PCEP speaker supports updating the information in the Open message without resetting the session.

If a PCEP speaker receives an Open Message that does not set the OPEN-REFRESH-CAPABILITY flag, the PCEP Speaker MUST NOT send Open Refresh messages to the remote PCEP speaker.

## Open Refresh

An Open Refresh is transmitted by sending a PCNtf Message ([RFC5440]) containing a NOTIFICATION Object with Notification-type=TBD2 (Open-Refresh):

* Notification-value=1: Refresh the Open information. The NOTIFICATION Object encodes any TLV that can be encoded in an OPEN Object. The NOTIFICATION Object contains a snapshot of all unmodified and modified TLVs. Upon receiving an Open-Refresh Notification message, the PCEP Speaker compares the newly received TLVs with the previously received TLVs to determine what has changed. An omission of a TLV MUST be treated as a removal of the TLV and perform a necessary side effect(s) to the system state as if the TLV was never exchanged during session establishment via Open message.

### Adding/removing values or Sub-TLVs

If the PCEP Speaker determines it cannot support the Open-Refresh differential change(s), the PCEP Speaker generates a PCEP Error (PCErr) with Error-type=TBD3 (Unsupported-Open-Refresh) and error-value TBD4 and it SHOULD terminate the PCEP session.


# Security Considerations

TODO Security

# Managability Considerations

TODO

# Implementation Status
[Note to the RFC Editor - remove this section before publication, as well as remove the reference to RFC 7942.]

This section records the status of known implementations of the protocol defined by this specification at the time of posting of this Internet-Draft, and is based on a proposal described in [RFC7942]. The description of implementations in this section is intended to assist the IETF in its decision processes in progressing drafts to RFCs. Please note that the listing of any individual implementation here does not imply endorsement by the IETF. Furthermore, no effort has been spent to verify the information presented here that was supplied by IETF contributors. This is not intended as, and must not be construed to be, a catalog of available implementations or their features. Readers are advised to note that other implementations may exist.

According to [RFC7942], "this will allow reviewers and working groups to assign due consideration to documents that have the benefit of running code, which may serve as evidence of valuable experimentation and feedback that have made the implemented protocols more mature. It is up to the individual working groups to use this information as they see fit".




# IANA Considerations

##  Open Object Flag Field

IANA is requested to allocate a new bit value in the "Open Object Flag Field" registry:

| Bit | Description              | Reference       |
| :--- |    :----:               |          ---: |
| TBD1 | OPEN-REFRESH-CAPABILITY | This document |


##  NOTIFICATION Object

IANA is requested to allocate a new Notification-type value in the "Notification Object" registry:

| Notification-type | Name              | Reference       |
| :--- |    :----:               |          ---: |
| TBD2 | Open-Refresh | This document |
|      | Notification-value | |
|      | 1: Refresh the Open information | |


##  PCEP Error

IANA is requested to allocate a new Error-type value in the "PCEP-ERROR Object Error Types and Values" registry:

| Error-type | Meaning              | Error-value |Reference       |
| :--- |    :----:               | :----:|         ---: |
| TBD3 | Open-Refresh Error | 1: Open-Refresh is not supported| This document |

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge Dhruv Dhody for the discussion and ideas related to this document.
