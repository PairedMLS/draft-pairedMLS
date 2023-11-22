---

title: "PCS in Limited Modes"
#abbrev: "TODO - Abbreviation"
category: info

docname: draft-LName-mls-guardianextension-00
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:00
date:
consensus: true
v: 3
area: AREA
workgroup: MLS
keyword:
 - MLS
venue:
  group: MLS
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -  fullname: Elsie Fondevik 
    organization: Kongsberg Defense & Aerospace
    email: elsie.fondevik@kongsberg.com
-   fullname: Britta Hale
    organization: Naval Postgraduate School
    email: britta.hale@nps.edu
-   fullname: Xisen Tian 
    organization: Naval Postgraduate School
    email: xisen.tian1@nps.edu


normative:

informative:


--- abstract

# Abstract 
This document describes the Messaging Layer Security (MLS) Guardian extension that improves security for offline/limited mode user device(s) through using a paired guardian device. Constrained devices that are unable to self-update can be updated by their guardian devices to preserve forward secrecy and post-compromise security through this Guardian Extension. 

# About This Document

This note is to be removed before publishing as an RFC.

Status information for this document may be found at https://datatracker.ietf.org/doc/draft-LName-mls-guardianexension/.

Discussion of this document takes place on the MLS Working Group mailing list (mailto:mls@ietf.org), which is archived at https://mailarchive.ietf.org/arch/browse/mls/.  Subscribe at https://www.ietf.org/mailman/listinfo/mls/.

Source for this draft and an issue tracker can be found at https://github.com/emmestl/draft-guardian-protocol.

# Status of this Memo 
This Internet-Draft is submitted in full conformance with the provisions of BCP 78 and BCP 79.

Internet-Drafts are working documents of the Internet Engineering Task Force (IETF).  Note that other groups may also distribute  working documents as Internet-Drafts.  The list of current Internet-Drafts is at https://datatracker.ietf.org/drafts/current/.

Internet-Drafts are draft documents valid for a maximum of six months and may be updated, replaced, or obsoleted by other documents at any time.  It is inappropriate to use Internet-Drafts as reference material or to cite them other than as "work in progress."

This Internet-Draft will expire on XX May 2024.

# Copyright Notice 

Copyright (c) 2023 IETF Trust and the persons identified as the document authors.  All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal Provisions Relating to IETF Documents (https://trustee.ietf.org/license-info) in effect on the date of publication of this document. Please review these documents carefully, as they describe your rights and restrictions with respect to this document.  Code Components extracted from this document must include Revised BSD License text as described in Section 4.e of the Trust Legal Provisions and are provided without warranty as described in the Revised BSD License.


# Table of Contents

1. Terminology
2. Introduction
3. Convention and Definitions
4. Protocol Overview (what gets shared with who and how)
    [Diagrams of what end users see]
    [Diagram of step-by-step initiation of protocol]
5. Limited Mode 
    Anonymous 
        Security Considerations
    Public 
        Security Considerations
7. Security Considerations 
    Restrictions for users, storage, and random tape sharing
8. Extension Requirements to MLS 
9. IANA Considerations
10. Acknowledgements 
10. Normative References 


# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

The terms MLS client, MLS member, MLS group, Leaf Node, GroupContext, KeyPackage, Signature Key, and RequiredCapabilities have the same meanings as in the MLS protocol [I-D.ietf-mls-protocol].

# BH Notes

Title: PCS in Limited Modes (or something not Guardian)
  - Might be room to collab with User Trees  

Recommend standards track 
No use of guardian/edge terminology (anchor is ok)

Stick with Anonymous Guardians; we could do a separate draft for "public guardian". There seems to be less interest in public guardian. 

Choose 4b: Shared Rand, Distinct Signature Keys 
 OR 
- Puncturable SOMETHING (PRF) approach: Alice and Bob are going to share randomness. Alice shares update to Group but not to Bob. Alice shares puncturable information to Bob so he can roll key forward. How do we send a message to Bob to have him update the key without an adversary being able to do the same?

The idea that the puncturable *something* can't be replayed.. 

- The shared randomness can be preinstalled or in a secure channel, but we assume the adversary does not compromise this initial establishment. 

- Chnage nomenclature to not have edge or guardian mentions. Use user-tree POV instead (e.g. user device A user device B).

- If Alice and Bob not paired you do a standard KEM update to share the randomness with Bob. If they are paired, you'll have to find a different way (via the puncturable PRF msg) to signal to Bob to update their state. It needs be formatted properly for the DS to handle (check proposal, commit, and update message structure looks for a peer). It needs to look like how a KEM would (i.e. appear random). 

TODO: 
- MLS Security Impacts - this is a required header 
- 



# Introduction

Guardianship offers a way for end-users to achieve improved security in constrained settings by allowing a paired trusted device to update security parameters on their behalf. The end-user's trusted device is denoted as the guardian. The guardianship protocol builds upon MLS (see [RFC9420](https://www.rfc-editor.org/info/rfc9420)). While this document makes recommendations for two versions of guardian extensions, interested readers can learn about other cases that were evaluated at https://github.com/emmestl/draft-guardian-protocol.git. #should this be eprint?

The Guardian Protocol (GP) consists of two operational modes, distinguished by the group's general knowledge of guardians: *Anonymous Guardian* and *Public Guardian*. To enable the guardian extension, the MLS leaf node must accomodate being shared by at least two devices, the guardian and edge(s). This means that, depending on the operational mode, the leaf node would also support at least two sets of signature keys. 

# Conventions and Definitions

_Edge Device_: An edge device is an original user equipment device. Such a device may operate in receive-only mode or in another limited fashion such that sending regular keying updates is impractical or even impossible

_Guardian Device_: The guardian device operates from a secured space with reliable network access. The intent is for the guardian device to be paired with, and provide keying updates on behalf of the edge device. This supports forward secrecy in the event the edge device is compromised. When the edge device is in an active state that allows for it to perform keying updates of its own, the guardian device may be placed in offline mode.

**TODO: Since we are making a MLS extension, should we stick with just "Leaf Node" instead of "anchor"?**
_Anchor_:  The MLS leaf node which acts as a shared access point(s) into the underlying group by the guardian and edge device is called an anchor. The anchor is viewed as a group member by the rest of the group and has it's own unique credentials. 

_Edge Device Operational Modes_
An edge device has two modes of operation. Before an edge device enters a new state the MLS delivery service (DS), which facilitates group state consensus by ensuring in-order delivery of MLS messages, must be informed. The operational states are:
* _Online mode_: The edge device is available and running the group protocol as per specification. In this state it does not have need of a guardian.
* _Limited mode_: The edge device has the ability to receive application messages and key update messages, but it may not preform its own key updates. Depending on environ- mental situation, an edge device may still be allowed to send application messages while in this mode. In this state, a guardian may send updates on behalf of the edge device.

_Guardian Operational Modes_
A guardian may be in one of two modes as well, contingent on the mode of the edge.
* _Offline mode_: When an edge device is online the guardian may be set to be inactive (depending on the guardianship construction).
* _Online mode_: When the edge device enters limited mode, it becomes reliant on a guardian. Therefore the guardian status must be set to online in order to send key updates.

_Shared Randomness_: In order for a guardian to heal an edge that is unable to self-update, they must share a random tape. Practically, this is accomplished by sharing a seed for random number generation between edge and guardian. This random seed SHOULD/IS RECOMMENDED be shared via secure hardware or it MAY be shared over a secure channel. Readers are encouraged to see [www.link.to.our.paper.com] for security tradeoff analysis. 

<!--{::boilerplate bcp14-tagged}-->

# Protocol Overview 
[TODO] Add diagrams of how the protocol is initiated and what messages are sent 


# Limited Mode
[TODO] Discuss tradeoffs, general overview of modes, anything that fits both anonymous and public modes 


## Anonymous Mode 

An anonymous guardian is when the guardian presence is undetectable by the other group members. Through the edge device and guardian sharing an MLS leaf node, signature key pair, and randomness its communications to the group would appear to be coming from a sole group member instead of a guardian-edge pair to other group members.

Traceability of the Anonymous Guardian is limited to the MLS Authentication Service (AS) and Delivery Service (DS). Depending on the DS design, the edge-guardian pairing could be detected by the DS to properly deliver messages. In a broadcast/multicast DS design scheme even the DS would be oblivious to the presence of the guardian. 


### Security Considerations 
Restrictions on how things are stored and shared; assumptions made (sharing the random tape is successfully done with no compromises) - how the randomness is shared is up to the user. 

### Applicable use cases
This mode of application is desirable when group members do not want to explicitly inform all other group members that they are unable to update. 


### Protocol Overview (?)
TODO Ref to separete document that conains a thurough design description

The protocol uses default MLS together with a seperate secure 1-1 communication channel. 



## Public Mode

A public guardian is one that shares an anchor node with the edge but has its own unique signing key. Updates that come from the guardian and edge (when online) will be traceable by other group members. 

Ref fig 4, 6
Requires altercations to MLS

# Security Considerations 
## Applicable use cases
This operational mode is applicable when a user wants to explicitly announce that their edge device is in limited mode. 

## Protocol Overview (?)

## Protocol Restrictions

# Extension Changes to MLS

## Leaf Node Contents

The MLS leaf node will need to support multiple signature keys for the public guardian. The leaf node content is modified by changing `signature_key` to a vector of `SignaturePublicKey`. 

    struct {
        HPKEPublicKey encryption_key;
        SignaturePublicKey signature_key<V>;
        Credential credential;
        Capabilities capabilities;

        LeafNodeSource leaf_node_source;
        select (LeafNode.leaf_node_source) {
            case key_package:
                Lifetime lifetime;

            case update:
                struct{};

            case commit:
                opaque parent_hash<V>;
        };

        Extension extensions<V>;
        /* SignWithLabel(., "LeafNodeTBS", LeafNodeTBS) */
        opaque signature<V>;
    } LeafNode;


# Security Considerations

# IANA Considerations 




# References

[I-D.ietf-mls-protocol]
Barnes, R., Beurdouche, B., Robert, R., Millican, J., Omara, E., and K. Cohn-Gordon, "The Messaging Layer Security (MLS) Protocol", Work in Progress, Internet-Draft, draft-ietf-mls-protocol-20, 27 March 2023, <https://datatracker.ietf.org/doc/html/draft-ietf-mls-protocol-20>.


## Normative References (i.e. RFCs)
[1] <https://www.rfc-editor.org/info/rfc9420> "MLS RFC"
[2] <https://www.rfc-editor.org/info/rfc5246> "TLS RFC"


## Informational References 
Our paper 

# Appendices 


# Acknowledgments
{:numbered="false"}
## Contributors 
## Authors 
