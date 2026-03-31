---
title: Permission Transaction ID
description: >
  What the Permission Transaction ID is, how it uniquely identifies a consent interaction,
  and how it serves as proof-of-consent for GDPR accountability.
status: stub
---

# Permission Transaction ID

Every consent interaction in Waulter generates a unique **Permission Transaction ID** — a tamper-evident identifier that serves as proof-of-consent for GDPR accountability purposes.

<!-- TODO:
  ## What is the Permission Transaction ID?
  - A unique identifier generated for each consent interaction (accept, reject, mixed)
  - Links a specific visitor's consent decision to a point in time
  - Serves as the consent receipt / proof-of-consent under GDPR Article 7

  ## What data is included in a consent receipt
  - Transaction ID
  - Timestamp of the consent decision
  - Which purposes were granted and denied
  - Configuration ID and version
  - How the consent was collected (banner, preference centre)

  ## Where to find the Transaction ID
  - **Dashboard**: in the consent records / transaction log section
  - **API**: accessible via the Waulter API for programmatic retrieval
  - **dataLayer**: included in the `Waulter:Decision` event payload

  ## How DPOs use it
  - Demonstrating compliance during audits: "here is the consent receipt for user X"
  - Responding to data subject access requests (DSARs)
  - Proving that consent was collected before data processing began
  - Verifying the scope of consent (which purposes were granted)

  ## GDPR accountability
  - GDPR Article 7(1): "the controller shall be able to demonstrate that the data subject has consented"
  - The Permission Transaction ID provides this demonstration
  - Each transaction is immutable — the record cannot be altered after creation
  - Retention period for consent receipts (configurable, regulatory minimums)
-->
