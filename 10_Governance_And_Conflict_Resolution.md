# 10: Governance, Security, and Standards

*As of 2026-10-03. The public version of the foundational document of the same name: governance as designed and as built, the threat model and what the architecture does and does not protect, the standards followed, and the roadmap without dates. General mechanism, no implementation detail.*

---

## 1. Introduction

Three subjects share this page because the internal document joins them: how decisions about wot.id are meant to be made, how the system is protected and where that protection ends, and which standards it follows. On the first subject most of what follows is design; on the second it is the running system; on the third it is a mix, stated as such.

## 2. Core Governance Principles

The aims: decentralised governance with no central authority over the protocol's evolution; transparency of proposals, votes and proceedings; fairness; effectiveness; community integrity; accountability; and adaptability. These are design aims consistent with the first principles of [01](01_Project_Overview_And_Principles.md) §1.0; the implementation state is building blocks in the contracts and nothing in the app (§4).

## 3. Governance Model

Community-driven, with on-chain votes on the ledger and off-chain discussion, possibly with delegation of voting power — described as the intended model. Governance would concern the protocol's rules and the ecosystem's health, not individual disputes between users.

## 4. On-Chain Governance Mechanisms (Move Contracts)

The trust module of the contracts holds typed proposals about a trust profile — create, vote, execute — bound to their proposer and their target, so that a vote cannot be cast in another's name and a proposal cannot be executed against a profile it does not name ([05](05_Move_Smart_Contracts.md) §5.3). The wider governance objects the internal document sketches — a general proposal, a conflict case — do not exist in the contracts.

### 4.1. Phase 2: Move Primitives Deployed; Production Usage Deferred

The building blocks are deployed and callable; no server route and no app surface uses them. An earlier mock governance interface was deleted in March 2026 because it showed fabricated data. Governance is deferred until it becomes a strategic priority; no date is given.

## 5. Decision-Making Process

Envisioned: a proposal on the ledger, discussion in the community, a formal voting period, tallying against a threshold, implementation. Voter eligibility, voting weight and thresholds are to be defined as the model matures. Nothing of this runs today.

### 5.1. Governance Proposal Lifecycle Visualization

Design.

### 5.2. Reputation-Weighted Voting Mechanism

Design. No voting surface exists, and trust is not measured today ([07](07_Trust_Architecture_And_Management.md)).

## 6. Conflict Resolution Process

Design only: a dispute recorded on the ledger with its parties and evidence, arbiters selected by a mechanism to be defined, a resolution, an optional appeal. No contract object, no server route and no app surface exist for it.

## 7. Community Participation and Integrity

Lowering the barriers to participation, educating participants, fostering constructive dialogue, and safeguarding against manipulation — including verified human identity for roles where it matters — are the stated aims for the community around a governance that does not yet run.

## 8. Security Threats and Mitigations

### 8.1. Threat Model

Malicious actors on the network, on the ledger and off it; a compromised server or a rogue insider; compromised user devices; and future quantum adversaries. [02](02_System_Architecture.md) §10.9 says, case by case, what holds and what does not.

### 8.2. Security Mitigations and Best Practices

**What the architecture guarantees** — a guarantee holds whatever wot.id wants, because wot.id lacks the key, the data or the power to break it:

- **Your private key is created on your device and never sent.** No server route accepts it. The passkey backup on the ledger is ciphertext under a secret only your passkey can produce.
- **wot.id cannot sign as you.** Every change to your identity, details, vouches, files or recovery settings needs your signature; wot.id's signature pays the fee and authorises nothing else.
- **wot.id cannot read what you encrypt.** Details, messages, files and your Mailbox are encrypted on your device before they leave it. Under legal compulsion, what wot.id can hand over is ciphertext and the metadata of [02](02_System_Architecture.md) §1.1.
- **Captured ciphertext resists a quantum computer.** Every key delivery combines X25519 with ML-KEM-768.
- **wot.id cannot restore your identity, nor take it over through recovery.** It holds no copy of your key, no guardian share and no recovery code.
- **What you signed stays signed.** A transaction on the ledger is permanent and attributable to its signer.
- **A vouch about you is yours to erase**, and the erasure is final.

**Where the guarantees end:**

- **The app itself is served by wot.id.** Every guarantee assumes the app you run is the app wot.id means to ship. The source is not published, so you cannot compare the served code with a published build. This is the largest trust you place in wot.id; nothing is built yet that removes it.
- **wot.id can upgrade the contracts**, under the ledger's compatible policy, with a key kept offline; each upgrade is a public transaction ([05](05_Move_Smart_Contracts.md) §6.1). "Nobody can move your identity" is true of the contracts as deployed; an upgrade could change that, in public.
- **The keys on your device** are as safe as the device: wrapped under your passkey when you have one, protected only by the device when you do not. Malware on a device you are using can act as you while it is there.
- **The passkey backup is as strong as the passkey**: whoever can use it, on any device where your keychain has synced it, can open the backup. If you want no third party involved, back up the 24 words instead.
- **The ledger shows structure**: who vouched for whom and when, whom you admitted, your wallet's history including that wot.id paid its fees, file sizes and categories, the shape of your recovery settings, when offline messages were left for you.
- **Some things cannot be taken back**: admission to your circles; past messages if your key ever leaks, since Talk has no forward secrecy; a leaked private key, since there is no way today to move an identity to a new key.
- **wot.id's servers are a dependency today**: the hosted app, the fee payment, the live-message relay and the cloud copies. If they stopped, what is on the ledger would remain and your key would still open it; what would end is those four things and sign-in sessions.

**How the system is built to these ends:** contracts in a language whose ownership model prevents whole classes of bugs, with authorisation by the transaction's signer; end-to-end encryption and signed envelopes in Talk; sessions required on every protected route, an origin allow-list, rate limits, validated inputs, and logs without plaintext e-mail or tokens; keys derived and held in the browser, never at the server; content encrypted before it leaves the device; a crypto-agile design with hybrid post-quantum key delivery, classical signatures until the ledger supports post-quantum ones; weekly dependency audits. Formal security audits of the contracts are a target, not a completed step.

## 9. Adopted Standards and Interoperability

### 9.1. Adopted Standards

- **W3C Decentralized Identifiers** — the idea: `did:wot:0x…` names an identity object, controlled by a key. The DID document format is not served.
- **W3C Verifiable Credentials** — the concept informs the design; a vouch is a ledger record, not a verifiable credential, and no credential format is issued.
- **IOTA Rebased standards and practices** — Move on the base layer, programmable transaction blocks, sponsored transactions.
- **NIST post-quantum standards** — ML-KEM-768 (FIPS 203) in every key delivery today; post-quantum signatures when the ledger supports them.

### 9.2. Interoperability Strategy

Alignment with the Trust over IP model ([01](01_Project_Overview_And_Principles.md) §5); a formal trust-spanning protocol, a context registry and standard data formats are design intentions. No integration with another identity system, social network or credential system exists today.

## 10. Development Principles and Best Practices

Modularity, readability, testability, scalability, reusability; design decisions documented before code; every change gated by the project's build and test checks; a weekly dependency audit. The source code is not published; the contracts are readable on the ledger as deployed.

## 11. Technical Design Principles Enforcement

Modularity through distinct contract modules, one server and a component-based app; security and privacy by design through hybrid post-quantum encryption, client-side key custody and the three privacy bands; decentralisation and user sovereignty at the core; interoperability pursued through the standards of §9; resilience, rigorous testing, crypto-agility, and simplicity as working rules.

## 12. Project Roadmap and Future Considerations

### 12.1. Project Roadmap and Milestones

Completed: the foundation — principles, contracts, one server, the app, the documentation. Partly done: on-chain vouches are real and live; governance exists as building blocks without an interface; the official IOTA identity framework was evaluated and not adopted. Ahead, without dates: community tools and development kits, pilot programmes, formal security audits, broader adoption, integration with other trust ecosystems, a formal body for long-term stewardship, and expanded use of zero-knowledge proofs.

### 12.2. Future Governance Considerations

Delegation of votes, reputation in governance, treasury management, scalability of governance, cross-ecosystem decisions — all open.

| Item | State | Since |
|---|---|---|
| Private key generated on the device, never sent | live | from the start |
| Hybrid X25519 + ML-KEM-768 key delivery | live | December 2025 |
| Passkey-wrapped keys on the device | live | September 2026 |
| Guardian recovery (two of two, 48-hour arming) | live | 19 September 2026 |
| Proposal and voting building blocks | in the contracts, no app surface | — |
| Conflict resolution | design | — |
| Changing a guardian set in the app; moving an identity to a new key | not built | — |
| Checking the served app against a published build | not built | — |
| Hiding the public structure (zero-knowledge proofs) | planned, no date | — |
