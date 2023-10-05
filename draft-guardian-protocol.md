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

docname: draft-guardian-protocol
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: AREA
workgroup: MIMI
keyword:
 - MLS
venue:
  group: MIMI
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: USER/REPO
  latest: https://example.com/LATEST

author:
 -
    fullname: Your Name Here
    organization: Your Organization Here
    email: your.email@example.com

normative:

informative:


--- abstract

TODO Abstract


--- middle

# Introduction


Guardianship offers a way for end-users to achieve improved security in constrained settings by allowing alternative trusted devices to update security detail on their behalf. The trusted device will be denoted guardian. 

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