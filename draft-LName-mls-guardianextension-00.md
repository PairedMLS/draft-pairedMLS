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
This document describes the Paired Messaging Layer Security (MLS) extension that improves Post Compromise Security for devices that are unable to self-update using a trusted paired device. 

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
4. Extension Overview (what gets shared with who and how)
    [Diagrams of what end users see]
    [Diagram of step-by-step initiation of protocol]
5. Security Considerations 
    Restrictions for users, storage, and random tape sharing
    Anonymous Mode
6. Extension Requirements to MLS 
7. IANA Considerations
8. Acknowledgements 
9. Normative References 


# Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

The terms MLS client, MLS member, MLS group, Leaf Node, GroupContext, KeyPackage, Signature Key, Handshake Message, Private Message, Public Message, and RequiredCapabilities have the same meanings as in the [MLS protocol] <https://www.rfc-editor.org/rfc/rfc9420.html>.

Generally, ___Paired MLS___ involves two paired devices, where one device can perform PCS updates in an MLS group on behalf of the other device. Without loss of generality, we define the one performing updates as the active device and the device not performing the update as the passive device:

_Passive Device_: A passive device is an original user equipment device that is not issuing updates. Such a device may operate in receive-only mode or in another limited fashion such that sending regular keying updates is impractical or even impossible. A passive device may be offline or online, i.e., if the passive device is online it can receive application and group management messages, but is restricted from issuing updates.

_Active Device_: An active device is another user equipment device designated to update on behalf of its paired passive device. The active device's updates enable a passive device to PCS heal after a compromise for improved post-compromise security. 

Paired devices may switch roles between active and passive. Also a device may be paired with multiple others, such that it can issue updates on behalf of several paired passive devices.

_Anchor_:  The MLS node that acts as a shared access node between the paired and passive device is called an anchor. The anchor can be a leaf node of a group member, where the paired devices both have access to the leaf node information but our nominally outside of the MLS tree. The anchor can also be a shared intermediate node on the path to the root of an MLS tree, and as such the the anchor may be shared with multiple only MLS members in addition to the paired devices. 

<!-- 
_passive Device Operational Modes_
An passive device has two modes of operation. Before an passive device enters a new state the MLS delivery service (DS), which facilitates group state consensus by ensuring in-order delivery of MLS messages, must be informed. The operational states are:
* _Online mode_: The passive device is available and running the group protocol as per specification. In this state it does not have need of a active device.
* _Limited mode_: The passive device has the ability to receive application messages and key update messages, but it may not preform its own key updates. Depending on environ- mental situation, an passive device may still be allowed to send application messages while in this mode. In this state, the active device may send updates on behalf of the passive device.

_Active Operational Modes_
The active device may be in one of two modes as well, contingent on the mode of the passive.
* _Offline mode_: When an passive device is online the active device may be set to be inactive.
* _Online mode_: When the passive device enters limited mode, it becomes reliant on a active device. Therefore the active device status must be set to online in order to send key updates.
-->

_Shared Randomness_: In order for one device to perform cryptographic updates on behalf of another, they must share a source of randomness that is kept in secure storage. This is in addition to each of the devices' own randomness source. In cryptographic analysis terms, such shared randomness must be stored with similar protections as signature keys in order to not be assumed as compromised in the event of other state compromise. Practically, this is accomplished by sharing a seed for pseudorandom number generation between the two. This random seed IS RECOMMENDED be shared via secure hardware or sharing MAY be bootstrapped over a 1-to-1 channel, where the added MLS PCS guarantees from this draft are contingent on the security of the 1-to-1 channel. 
Readers are encouraged to see [FHX23] for security tradeoff analysis. 

<!--{::boilerplate bcp14-tagged}-->

<!--
# BH Notes

Title: PCS in Limited Modes (or something not Guardian)
  - Might be room to collab with User Trees  

Recommend standards track 
No use of guardian/edge terminology (anchor is ok)

Choose 4b: Shared Rand, Distinct Signature Keys 
 OR 
- Puncturable SOMETHING (PRF) approach: Alice and Bob are going to share randomness. Alice shares update to Group but not to Bob. Alice shares puncturable information to Bob so he can roll key forward. How do we send a message to Bob to have him update the key without an adversary being able to do the same?

The idea that the puncturable *something* can't be replayed.. 

- The shared randomness can be preinstalled or in a secure channel, but we assume the adversary does not compromise this initial establishment. 

- Chnage nomenclature to not have edge or guardian mentions. Use user-tree POV instead (e.g. user device A user device B).

- If Alice and Bob not paired you do a standard KEM update to share the randomness with Bob. If they are paired, you'll have to find a different way (via the puncturable PRF msg) to signal to Bob to update their state. It needs be formatted properly for the DS to handle (check proposal, commit, and update message structure looks for a peer). It needs to look like how a KEM would (i.e. appear random). 

TODO: 
- 
-->


# Introduction

___Paired MLS___ allows a trusted device to update the security parameters of an another group member. The trusted paired device can be added to the group or can be another existing group member. The ___Paired MLS___ extension builds upon MLS (see [1]. This document makes describes for two operational modes of the ___Paired MLS___ extension; interested readers can learn about other cases that were evaluated at [FHX23]. 

The ___Paired MLS___ extension describes a standard case where each device possesses its own signature key. To enable the standard ___Paired MLS___ extension, the MLS leaf node must accommodate being shared by at least two devices. This means that the leaf node would store at least two sets of signature keys. 
An additional operational modes is described, *Anonymous* mode, where the paired devices share a signing key and the paired device is able to issue digital signatures on behalf of the partner device in addition to PCS updates. Caveats to the Anonymous mode are discussed further under ___Security Considerations___. 


# Extension Execution 

Paired MLS is an extension of MLS, as found in [1], per user, i.e. per MLS leaf node. Meaning that each MLS leaf node itself MAY decide whether it wishes to run the extension. This extension comes in two variantions, one where all group members are aware that an MLS node uses the extension and one where the usage is opaque to the remaining MLS group members.   

Independent of version selected the execution of the extension assumes the existence of a MLS protocol where the device that desires to execute the extension is already a member, and thus has access to an MLS leaf node. The group member initiating this extension must first negociate the shared randomness with another device: this SHOULD be done via secure hardware and MAY be done through a secure one-to-one channel. [**TODO specify details of this negociation - use Handshake Messages with DS or with an out-of-band chanel?]**

After the shared randomness is established, the devices are "paired" and either device may update on the other's behalf. When the active device issues an update to the group on behalf of the passive device it will also issue a notification to the passive device to ratchet forward its group key. This ensures the passive device can still process updates and commits made by other group members. The notification message MUST be formatted as a MLS Public Message to the paired devices group. **[TODO: Private Message vs Public Message for notification]**

Important; once a device has initiated the use of Paired MLS mode, the original MLS commands become obsolete for the specified MLS leaf node, instead the following commands take precedence. 

* enterPairing
* exitPairing
* removePair
* updatePairing

Messages generated by running one of the specified commands are either transmitted between the paired devices or onto the MLS group. The transmitted messages either take the form inherited from MLS or one of the following.

* A _InitiatePCS_ message is sent form the original MLS leaf node to its paired device to initiate execution of Paired MLS.
* A _CeasePCS_ message is sent form the original MLS leaf node to its paired device to terminate execution of Paired MLS.
<!-- * A _Toggle_ message is sent between paired devices to determine who is resposible for MLS updates. -->
* _ACK_ messages are returned by the paired device to signify command has been recieved and accepted. __[ESM Notes]__ dont think this is needed.

<!----------
__[XT Notes]__
* It's unclear to me what the difference between the commands and the messages:  _InitiatePCS_ and _enterPairing_; _CeasePCS_ and _exitPairing_; and _toggleUpdateCntrl_ and _Toggle_. 
* Limited Mode is a use case of our extension, and should not be the name. Maybe we can call it "Paired MLS" or "MLS Paired PCS": we need a name that captures the essence of Guardianship and that stands on it's own to let a reader intuitively understand what it does... "user branches"? haha
----------->

## Example Run of Paired MLS

### Negotiate Randomness 
The following is an example of using a one-time secure channel to establish shared randomness. Device A initiates the beginning of the Paired MLS extension by informing future pairing device B as well as the DS of a coming pairing. The paired device B will also inform the DS that it is ready to run the protocol. After the DS has been informed by both initiator and its paired device an accept reply is returned to both devices (see Figure 1). 

                                                 Group
A                 B            Directory         Channel
| NegociateRand(B)|                 |              |
+----------------->                 |              |
|                 |                 |              |
|        Negociate|Rand(B)          |              |
+-----------------+----------------->              |
|                 |                 |              |
|                 | NegociateRand(B)|              |
|                 +----------------->              |
|                 |                 |              |
|                 | NegociateRand(B)|              |
|                 <-----------------+              |
|                 |                 |              |
|        NegociateRand(B)           |              |
<-----------------+-----------------+              |
|                 |                 |              |
**Figure 1b** A is an MLS group member that wishes to pair with device B using the existing MLS infrastructure.

                                                 Group
A                 B            Directory         Channel
| NegociateRand(B)|                 |              |
+----------------->                 |              |
|                 |                 |              |
| NegociateRand(B)|                 |              |
<-----------------+                 |              |
|                 |                 |              |
**Figure 1b** A is an MLS group member that wishes to pair with device B using a  one-to-one channel. 

Once A and B have been paired A can issue an update on behalf of B. Into the MLS group it issues the update as normal. The nofitication message is directly transmitted directley to B. From the regular MLS group members this update will not be destinguishable from any other MLS update preformed by A. The DS however has the additional task of informing B of the requested update, and if accepted, the acctual update. 

                                                                Group
A            B              G1  ...    Gn         Directory     Channel
|Update(B)   |              |          |              |           |
+------------+--------------+----------+--------------+----------->
|Notify(B)   |              |          |              |           |
+------------+--------------+----------+--------------+----------->
|            |              |          |              |Notify(B)  |
|            <--------------+----------+--------------+-----------+
|            |              |          |              |Update(B)  |
<------------+--------------+----------+--------------+-----------+
|            |              <----------+--------------+-----------+
|            |              |          |              |           |
|            |              |          <--------------+-----------+
|            |              |          |              |           |
|            |              |          |              |           |
|            |              |Commit(up)|              |           |
|            |              +----------+--------------+----------->
|            |              |          |              |           |
|            |              |          |              | Commit(up)|
<------------+--------------+----------+--------------+-----------+
|            |              <----------+--------------+-----------+
|            |              |          |              |           |
|            |              |          <--------------+-----------+
|            |              |          |              |           |
|            |              |          | Commit(up)   |           |
|            <--------------+----------+--------------+           |
|            |              |          |              |           |
|            |              |          |              |           |

**Figure 2** Paired devices A and B when A prefoms an update on behalf of B. Remaining MLS group members are labeled G1, ..., Gn.

Similarilty if A is the current main device and any other MLS group member updates the process will follow the flow as defined in RFC9420 with the additional step of DS informing B as well. If the network used is a multicast or broadcast setting, the addional step is not needed. 
<!--
When at some point A has to enter limited mode, i.e. will no longer be able to issue updates, B will need to be informed that it needs to take charge and become the new main device. This command does not affect the MLS group and only occurs as direct communication between A and B as well as informing the DS of a change in main device.

Now that B is the new main device it is in charge of updating the MLS leaf node. The updates issued by B is dones similarily as the update preformed by A as seen in, **Figure 2**. -->

To end MLS Paired mode extenision either A or B may issue an _CeasePCS_ command. This will remove the both devices form MLS group and will in priniple function as a self remove, this is a change required from MLS according to RFC9420. In the case of the example A is the one issuing the removal.

                                                                Group
A            B              G1  ...    Gn         Directory     Channel
| CeasePCS   |              |          |              |           | 
| Commit(Rem)|              |          |              |           |
+------------+--------------+----------+--------------+----------->
|            |              |          |              | Remove(A) | 
|            |              |          |              |Commit(Rem)|
<------------+--------------+----------+--------------+-----------+
|            <--------------+----------+--------------+-----------+
|            |              <----------+--------------+-----------+
|            |              |          <--------------+-----------+
|            |              |          |              |           |


MLS commands such as Remove, GroupInfo KeyPackage and Welcome take the form and are processed as according to RFC9420. 

<!--**[TODO]** Add diagrams of how the protocol is initiated and what messages are sent - use https://asciiflow.com/-->

## Shared Random Tape

Two (or more) devices pair with one another by sharing randomness via a secure out-of-band channel. Once the devices share a random tape, their group key updates are synchronized through the paired device making an update to the MLS group via the anchor node and notifying the passive device to ratchet it's own key forward. **[TODO]** specify how to use a puncturable PRF to ensure the notification to ratchet the key can't be replayed or forged by an adversary. Since the devices share a random tape, their key derivation function will yield the same pseudorandom keys. 

# Security Considerations
**[TODO]** Restrictions on how things are stored and shared; assumptions made (sharing the random tape is successfully done with no compromises) - how the randomness is shared is up to the user. 

Generally, the security of this extension is based upon the security of the out-of-band channel for sharing randomness and notification messages. 
<!--If the shared randomness is obtained by the adversary, then the only way to regain PCS is to remove the leaf node used to anchor the devices from the group. -->

Without the ability to interrogate the delivery service for anonymous pairings, compromised or malicious paired devices may eavesdrop undetected. If a group key is leaked somehow, PCS can be achieved through an update by either of the paired devices. However, if the shared randomness is compromised on one device, then both devices are irrevocably compromised as the attacker could generate new secrets used to generate group keys. 

the traceability of the paired device and the passive device by the rest of the group is possible. Public Mode is distinguished by the use of distinct signature keys by paired devices to issue signed updates to the group. This implies that the anchor leaf node holds a signature key for each of the devices sharing it. 

### Applicable use cases
This mode of application is desirable when group members do not want to explicitly inform all other group members that they are unable to update. 


## Hidden Mode 
**[TODO]** How to address abuse of anonymity
In hidden mode, pairing is undetectable by the other group members. Through sharing a signature key pair, signed messages to the group would appear to be coming from a single group member instead of unique entities. Traceability of the pair is limited to the MLS Authentication Service (AS) and Delivery Service (DS). Depending on the DS design, the pairing could be detected by the DS to properly deliver messages. In a broadcast/multicast DS design scheme even the DS would be oblivious to the presence of the guardian. 

The ramifications of this Hidden mode include ghost devices that could bypass the AS and DS in joining and participating in a group masquerading as its paired device. Moreover, if one paired device is compromised, then all devices will need to be revoked from the MLS group to regain group security. This is easily done by simply pruning the anchor node shared by the paired members from the ratchet tree. 


### Applicable use cases
This operational mode is applicable when a user wants to explicitly announce that their passive device is in a limited receive-only mode. 


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



#### Shared Randomness Establishment
The security of this extension is based upon the security of the out-of-band channel to used to establish shared randomness. In other words, if an adversary is granted access to the shared randomness, then the MLS group security is broken irrevocably as PCS could never be recovered except via removal. Therefore, we RECOMMEND the shared-randomness be installed via protected hardware in the same way that long-term signing keys stored such that it is infeasible to be accessed by an adversary. The shared-randomness MAY be shared via a secure 1-to-1 channel such as a key encapsulation mechanism between the devices. 

#### Notifications Between Paired Devices
The notification message sent to the passive device to forward ratchet its group key must be secured from forgery and replay attacks. If an attacker were able to to prompt either devices to update, then they would fall out-of-sync and be unable to decrypt future group messages. **[TODO]** Recommend sending notifications using the MLS DS or some other out-of-band DS? 

# IANA Considerations 
**[TODO]** Determine an extension code to use

# References

[I-D.ietf-mls-protocol]
Barnes, R., Beurdouche, B., Robert, R., Millican, J., Omara, E., and K. Cohn-Gordon. "The Messaging Layer Security (MLS) Protocol". Work in Progress, Internet-Draft, draft-ietf-mls-protocol-20, 27 March 2023. <https://datatracker.ietf.org/doc/html/draft-ietf-mls-protocol-20>


## Normative References (i.e. RFCs)
[1] <https://www.rfc-editor.org/info/rfc9420> "MLS RFC"
[2] <https://www.rfc-editor.org/info/rfc5246> "TLS RFC"


## Informational References 
[FHX23] E. M. Fondevik, B. Hale, and X. Tian. "Guardianship in Group Key Exchange for Limited Environments". Cryptology ePrint Archive, Paper 2023/1761. 11 November 2023. <https://eprint.iacr.org/2023/1761>

# Appendices 


# Acknowledgments
{:numbered="false"}
## Contributors 
## Authors 
