---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<ngit@github.com:emmestl/draft-guardian-protocol.gitame>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Guardianship extension for MLS"
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
 -
    fullname: Elsie Fondevik 
    organization: Naval Postgraduate School
    email: elsie.fondevik@kongsberg.com
- 
    fullname: Britta Hale
    organization: Naval Postgraduate School
    email: britta.hale@nps.edu
- 
    fullname: Xisen Tian 
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

#Copyright Notice 

Copyright (c) 2023 IETF Trust and the persons identified as the document authors.  All rights reserved.

This document is subject to BCP 78 and the IETF Trust's Legal Provisions Relating to IETF Documents (https://trustee.ietf.org/license-info) in effect on the date of publication of this document. Please review these documents carefully, as they describe your rights and restrictions with respect to this document.  Code Components extracted from this document must include Revised BSD License text as described in Section 4.e of the Trust Legal Provisions and are provided without warranty as described in the Revised BSD License.

# Introduction


Guardianship offers a way for end-users to achieve improved security in constrained settings by allowing a paired trusted device to update security parameters on their behalf. The trusted device is denoted as the guardian. 

The guardianship protocol builds upon MLS **ref MLS-rfc** and is for improved security in group communication between multiple distinct entities. Readers that are not familiar with MLS are referred to MLS **ref** to gain a general understanding. 

The protocol consists of two operational modes, distinguished by the groups general knowledge of guardians. For mode concerning anonymous guardians ref section **Anonymous Garudian** for section concerning known guardianship and update ref section **Public Guardian** respectively.

TODO should reference to paper be here? Intereseted readers can learn more etc.git@github.com:emmestl/draft-guardian-protocol.git


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Anonymous Guardian

Ref 5

## Applicable use cases
This mode of application is desirable when group members do not want to explicitly inform all other group members that they are unable to update. 


## Protocol Descrition
TODO Ref to separete document that conains a thurough design description

The protocol uses default MLS together with a seperate secure 1-1 communication channel. 


## Protocol Restrictions




# Public Guardian

Ref fig 4, 6
Requires altercations to MLS

## Applicable use cases

## Protocol Description
TODO Ref to separate document that contains a thorough description

## Protocol Restrictions


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.