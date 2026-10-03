# 07: wot.id - Comprehensive Trust Architecture

*As of 2026-10-03. The public version of the foundational document of the same name: what a vouch is, what the ledger records about it, how it is made, what the app counts today, what it does not compute, and the trust scale the system is built towards. General mechanism, no implementation detail.*

---

## Wire semantics — the R-D declaration (2026-08-06, binding)

What the trust fields mean on the ledger today, stated first so that nothing later on this page reads as current behaviour when it is design.

1. **The trust level on every vouch is a reserved field.** The protocol gives each vouch a level on a scale from −100 to +100. Today the app writes one fixed standard value into it and shows it nowhere as a judgement. A vouch is binary: its information is who vouched for which detail of whom, when, and its status — not the number. Graded and negative vouching return only through a design of their own, with a ceremony for giving them and protection against abuse.
2. **Any display of the scale is a single conversion, done once.** The ledger stores the level in its own integer form; whatever shows it converts it exactly once to the −100…+100 scale.
3. **Summaries speak in counts.** What the app shows about trust is how many people verified how many details, and the records behind each count. Older summary figures that pretended to be scores were removed.
4. **Nothing aggregates trust on the ledger.** Aggregates defined in earlier contract versions are neither fed nor read.
5. **Surfaces count people and details, not events.** One ceremony that verifies several details is one act; a count always names its unit — "3 people verified 5 of your details". A person is never shown with a minted score; what you see of a person is where you placed them in your circles and the signed record.

## 1. Introduction and Core Philosophy

Trust in wot.id is not a system layered on top of data: it is the measure of how reliable each stored value is, built from who vouched for it. Trust is over values — a first name, a nationality — never over files: a vouch says "this value is true", not "this file is authentic".

### 1.0. Foundational Understanding: Trust = Data Reliability

Every value an identity records is a claim about reality. The vouches other identities sign for it are the evidence. Today the evidence is counted and shown; in the long term it is to be measured on the scale of §1.2. The design the internal document describes beyond that — weighting by the voucher's own standing, context-dependent scores — is the designed range, not current behaviour.

### 1.1. Core Principles

Trust applies both to identities and to their individual details; one scale for everything; every assertion is signed and on the ledger, so anyone can check it; the same model for every kind of actor; and a post-quantum strategy for what is encrypted, with classical signatures until the ledger supports post-quantum ones.

### 1.2. The Universal Trust Scale

From **−100** (complete distrust) through **0** (neutral) to **+100** (complete trust), stored on the ledger in an integer form and shown on the −100…+100 scale. It is carried on every vouch and in the trust profile the contracts define per identity. Reserved today (see the declaration above); measuring trust on it is a central long-term aim ([01](01_Project_Overview_And_Principles.md) §2.6).

### 1.3. Identity Registry Integration

Vouches point at identities and their details through the registry and profile objects of [05](05_Move_Smart_Contracts.md). The identity being vouched for is found by its DID; the voucher is the signer of the transaction.

### 1.4. Current Implementation Status

| Capability | State | Since |
|---|---|---|
| Vouches per detail, the voucher taken from the signature | live | May 2026 |
| Vouches bound to the detail's exact version ("Value changed" when it moves) | live | August 2026 |
| Human verification — a vouch about the identity as a whole | live | November 2025 |
| Counts with their units; no score for any person | live | August 2026 |
| Erasure by the person a vouch is about; final | live | July 2026 |
| Asking someone to vouch back, remotely, after a first vouch | live | August 2026 |
| Withdrawing a vouch you gave | in the contract, no app control | — |
| Trust scale on every vouch and in the trust profile | in the contracts, reserved; measuring trust on it is the long-term aim | — |
| Graded or negative vouches | not built; the field is reserved | — |
| Automated vouches from services; agent-specific vouch contexts | not built | — |
| Proposals and votes about trust profiles | contract building blocks, no app surface | — |

### 1.5. Trust Scores on Atomic Data VALUES (wot.id Extension)

What wot.id adds around the W3C DID idea: per-value trust rather than per-identity reputation; vouches that name the value and its version; one public ledger for identity and vouches instead of issuers' registries; and erasure by the person a vouch is about. The weighting of a vouch by its voucher's standing and context-dependent trust are described in the internal document as the designed range; nothing computes them today.

## 2. On-Chain Trust Data Model (IOTA Move Contracts)

### 2.1. Entity-Relationship Diagram (ERD)

What the ledger records for each vouch, in words:

| Part | What is recorded |
|---|---|
| Who vouched | The voucher's address, taken by the contract from the transaction's signature — not from anything the voucher typed. It cannot be forged without the voucher's key. |
| About whom | The identity the vouch is about. |
| Which detail | The detail's label — "first name" — or "human verification". |
| Which version of it | The ledger object that holds the encrypted detail, and that object's version at the moment of vouching. |
| When | The ledger epoch; the transaction carries the exact time. A vouch has no expiry. |
| Status | Active, or one of the states in §5. |
| Level | On the −100…+100 scale; one fixed value today. |

What it does **not** record is the value. The detail stays encrypted where it is; the vouch points at it. Someone reading the ledger learns that *this identity vouched for that identity's first name on that day* — not what the name is.

### 2.2. Core Move Struct Definitions

The internal document also describes a wider set of trust objects — credentials, trust relationships, context definitions. Those are design; the deployed trust module holds vouches, trust profiles and the proposal building blocks of [05](05_Move_Smart_Contracts.md) §5.3.

## 3. Trust Interaction Flows and PTB Usage

### 3.1. Attestation Creation and Submission Flow

A vouch is one signed transaction by the verifier: wot.id's server prepares it and pays its fee; the verifier's device signs it; the verifier's browser broadcasts it; the server confirms it. On the ledger the verifier is the sender. The server refuses to prepare a vouch for yourself; the contract itself does not check this, so a reader of the raw ledger should.

### 3.2. Example PTB for Creating an Attestation

A vouch for several details is one transaction creating one vouch per detail. The layout is not part of the public description.

### 3.2a. Mobile-to-mobile QR attestation flow

Face to face, with two phones. The person being verified chooses what to include — human verification only, or selected details — and shows a code that is valid for a short time. The verifier scans it, sees the chosen values, confirms each one they have checked with their own eyes, and their device signs one vouch per detail. Both sides then see the record, each row with a link to the ledger.

### 3.3. Web-Based Attestations

After a face-to-face vouch, the verifier can ask the other person to vouch back. The request travels as an encrypted message and stays open for a limited time; answering needs no camera. This path exists only between two people who have already met through a first vouch.

### 3.4. Automated Attestations from Services

Vouches issued by services or automated systems on their own logic are design, not built. Every vouch today is made by an identity's holder in the app.

### 3.5. Phase 2: Governance Proposals

Typed proposals about a trust profile — create, vote, execute — exist as building blocks in the trust module, bound to their proposer and target. No app surface uses them ([10](10_Governance_And_Conflict_Resolution.md) §4).

## 4. Trust Aggregation and Computation Models

wot.id computes no score, rank or percentage for any person today, applies no weighting — a vouch from a close friend and a vouch from a stranger count the same, one person, one detail — and has no global view: there is no directory and no leaderboard. One rule does hold everywhere: **only an active vouch counts.** A vouch that was superseded, withdrawn by its voucher or erased by its subject is not current verified information and no surface counts it.

The consequence worth stating plainly: because the counts do not ask *who* the vouchers are, a group of identities that vouch for one another can produce any count. What exposes such a group is the record — each count opens to named identities, and you can see whether you know any of them. The protection today is in reading the evidence; there is no formula behind the counts.

### 4.1. Core Principles of Trust Aggregation

For the measuring that is the long-term aim, the internal document sets principles: aggregation within a defined context; diverse inputs; configurable and transparent models; account for age, changing reputations and revocation. None is implemented.

### 4.2. Input Data for Aggregation

Active vouches only (the admission rule above), each with its voucher, detail, version and time.

### 4.3. Basic Aggregation Models (Illustrative Examples)

Design, not shipped. What ships is the count.

### 4.4. Contextual Adjustments

Design, not shipped.

### 4.5. Output of Aggregation

Would be a value on the −100…+100 scale with the evidence behind it. Two conditions are already set for it: graded and negative vouches need a design of their own before any level other than the fixed one is written, and any number shown about a person must open to the vouches behind it, as every count does today.

### 4.6. Future Considerations

Advanced models, personal trust policies, explainability — design directions, none built.

## 5. Key Trust Dynamics and System Rules

| Status | Set by | What it means | Counted |
|---|---|---|---|
| Active | — | The vouch stands. | yes |
| Superseded | The voucher, by vouching again for the same detail | Replaced by the newer vouch. | no |
| Suspended, revoked | The voucher | Withdrawn — for a while, or for good. The contract supports it; the app has no control for it yet. | no |
| Erased | The person the vouch is about, in the app | Counts nowhere, is shown nowhere as evidence, and is final — the voucher cannot bring it back. | no |

Nothing is deleted. The ledger keeps every vouch and every change of status; erasure means the app and anyone honouring the status treat the record as void. A vouch is bound to the exact version of the detail it was made for: if you change the detail afterwards, the existing vouches stay active but are marked *Value changed* wherever they are listed — they vouched for an earlier value — and the voucher can vouch again for the new one. Each row in the app also says whether it could be checked against the ledger.

## 6. On-Chain Implementation with IOTA Mainnet

The trust module of [05](05_Move_Smart_Contracts.md) §5.3 is the live surface. The verifier signs; wot.id sponsors; the browser broadcasts.

### 6.1. Core Move Smart Contracts for Trust Objects

The wider object set the internal document sketches here — credentials, trust relationships, context definitions — is not deployed.

### 6.2. Programmable Transaction Blocks (PTBs) for Trust Operations

As for every write ([01](01_Project_Overview_And_Principles.md) §4.2).

## 7. Security, Privacy, and Ethical Considerations

### 7.1. Security Considerations

The voucher cannot be forged — it is the signer. Signatures are classical Ed25519 because the ledger verifies only classical schemes; post-quantum signatures wait on the ledger. Users and services should keep their keys as they would any key that can act for them.

### 7.2. Privacy Considerations

A vouch reveals that one identity vouched for one labelled detail of another identity at one time — never the value. Who vouched for whom is public structure on the ledger ([02](02_System_Architecture.md) §1.1); the values stay encrypted, and the people who can see your details are the ones you admitted or showed them to.

### 7.3. Ethical Considerations

Any future measuring of trust can inherit bias from who gets vouched for and by whom. The two conditions of §4.5 — a designed ceremony with abuse protection, and every number open to its evidence — are the guards set so far. wot.id does not judge intent: the ledger records who signed what and who vouched for what; whether an action was right is for the people involved.

## 8. Future Considerations

Qualitative assurance levels, contextual modifiers, standard aggregation formulas — named as possible directions, none built, none dated.

## Addendum: Trust Seeding Algorithms for Bootstrap Problem Resolution

How a newcomer with no vouches should be given a starting trust is an open problem the internal document explores with several unbuilt algorithmic approaches. Nothing in that addendum is implemented; today a newcomer simply has no vouches until someone who met them signs one.
