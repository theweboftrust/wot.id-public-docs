# 01: wot.id - Project Overview and Principles

*As of 2026-10-03. The public version of the foundational document of the same name: what wot.id is, the aim it works towards, the principles it is built on and how far each holds in the running system today, why the ledger is IOTA, and the status. It explains the mechanism in general; the implementation detail stays in the internal document.*

---

## 1. Introduction: Human Identity on the Web of Trust

wot.id is an open peer-to-peer environment where any digitally connected actor — human, machine, organization, or otherwise — can communicate, manage and exchange assets, and handle trust — in complete privacy and with quantum-safe encryption.

For most people, identity on the internet is an account in someone else's database. Your name, your contacts and your history sit with the platforms you use; when something goes wrong, the only recourse is to ask the party that holds all of it. Three developments put that arrangement under pressure: platforms fail the people who depend on them; software now acts on people's behalf and needs an identity of its own; and encrypted data is being collected today for a quantum computer that may open it later.

Concretely, wot.id is three things working together:

- **An app in your browser.** It creates and holds your keys, encrypts everything you store before it leaves the device, and signs every change you make.
- **A set of smart contracts on the IOTA mainnet ledger.** Your identity is an object there, controlled by your key. Your encrypted details, the vouches others give you, your file catalogue, your offline messages and your recovery settings live there too.
- **wot.id's server.** It prepares transactions for you to sign and pays their network fee, relays live messages, and keeps encrypted copies in the wot.id cloud — your files, and by default your message history. It holds no key of yours.

Two edges of the sentence above are stated where they apply: *in complete privacy* describes content — every value, message and file is encrypted on your device — not the structure around it, which the public ledger shows ([02](02_System_Architecture.md) §1.1); *quantum-safe encryption* is exact for encryption, while the signatures the ledger verifies remain classical for now ([02](02_System_Architecture.md) §10.2).

### 1.0. Core Mental Model: Data Sovereignty and Trust

Four ideas carry the whole design.

1. **Data is the claim.** Every value you store — a first name, a date of birth, a nationality — is itself a statement about reality. Trust is not a separate system; it attaches to each value through the vouches other identities sign for it. There is no second "claims system" beside the data.
2. **The owner needs only the key and the name.** Your data lives on the public IOTA ledger under your identity's name, encrypted under keys only you hold. wot.id's app is one way to read and change it, not the gatekeeper. If wot.id's servers stopped, the data and your key would still be enough.
3. **Trust is a measure of reliability, not a verdict.** Who vouched for which value, when, and whether the vouch still stands is recorded on the ledger. Today the app counts these records and opens every count to them; measuring trust on a scale is the long-term aim (§2).
4. **The app groups data by domain, not by function.** Identity, People & Groups, Agents, Digital Assets, Encrypted Files, Linked Accounts and Recovery are sections of one page; the grouping is presentation, not architecture.

Three first principles follow and are enforced everywhere:

- **Nothing on the ledger is plaintext content.** The ledger is world-readable, so every stored value is ciphertext, encrypted on the device. "Public" on the ledger means more peers can decrypt it, never that the bytes are readable. The honest boundary: the *structure* — object ids, labels, timestamps, who vouched for whom, file sizes and categories — is readable, and closing the remaining metadata gaps is owed work, not a finished state.
- **There is no public viewer.** wot.id has no anonymous application surface beyond the sign-in page and its information pages. Everyone who reads anything is an authenticated peer with their own identity and keys, reached through an interaction — a verification, a conversation, an admission to your circles.
- **wot.id's servers are conveniences, never necessities.** The ledger is the system. The server and the hosted app accelerate — they prepare transactions, pay fees, relay messages, keep cloud copies — but nothing depends on them for meaning or access. Each must be removable without touching the data, so that the end state can be no wot.id-operated server at all. That end state is a design constraint, not a date.

### 1.1. wot.id Ecosystem Overview

The actors are people, organisations, and software — AI agents, bots, devices — each with an identity of the same kind. The parts they use: the app in the browser (keys, encryption, signing), the contracts on the IOTA mainnet ledger (identities, details, vouches, file catalogues, offline messages, recovery settings), wot.id's server (transaction preparation, fee payment, message relay, cloud copies), and the wot.id cloud (encrypted file bytes and, by default, the encrypted message history). Anyone can read the ledger; only the holder of a key can change what belongs to it.

### 1.2. Standards Foundation: W3C DID Compliance

wot.id follows the idea of the W3C Decentralized Identifiers standard: an identity has a permanent name, `did:wot:0x…`, which is the object id of your profile on the ledger, and that name is controlled by a key rather than by an account at a provider. It does not follow the formats: no DID document is served and no verifiable credential is issued — a vouch is a record on the ledger. Interoperability with other DID systems is not built.

### 1.3. Identity Architecture: Primary vs Secondary Identifiers

Your **primary identifier** is the DID. It is minted when your identity is created, it does not change when you sign in on another device or recover through guardians, and everything — details, vouches, files, guardians — accumulates on it.

**Secondary identifiers** are doors, not the identity. An e-mail address confirmed by Google, Apple or GitHub is recorded on the ledger only as an unreadable, keyed hash that maps to your DID, so that a later sign-in with the same provider finds the same identity. A passkey and your private key are the other two doors ([02](02_System_Architecture.md) §10.3). If a provider closed your account, the identity would stay and the other doors would still open it.

### 1.4. Data Architecture: 100% On-Chain VALUES

The values you record about yourself are stored on the ledger, each encrypted on your device under a key derived from your private key. Beside them, the ledger holds the catalogue of files you chose to store under Encrypted Files: for each file its hash, its encrypted file key, its size and category, its sealed name and type, and a pointer to where the encrypted bytes are. The bytes themselves are not on the ledger; for every file stored today they are in the wot.id cloud, as ciphertext wot.id cannot read, with an optional encrypted copy on your device.

wot.id runs no database of its own for identity data. Its one persistent store is the cloud tier that holds encrypted file bytes and, by default, your encrypted Mailbox — ciphertext in both cases ([09](09_Data_Storage_And_Asset_Management.md)).

## 2. The long-term aim

wot.id records identity and trust between any actors, human or machine, under one privacy rule. The aim beyond today's system is that the trust an actor has recorded becomes usable for that actor's own decisions — which counterparties, messages or offers to accept — with the data staying with its owner. That is stated here as an aim, in the future tense. What exists today is binary vouching at one fixed trust level with counts shown to the user, the recovery, sharing and messaging surfaces of the later pages, and no recommendation, filtering, learning or zero-knowledge component.

### 2.6. Universal Trust Scale Visualization

The protocol carries one trust scale, from **−100** (complete distrust) through **0** (neutral) to **+100** (complete trust). It is built into the contracts twice: every vouch carries a trust level on it, and the contracts define a trust profile per identity holding a trust score on the same scale, starting at neutral. The scale is reserved today: the app writes one fixed standard level into every vouch and shows it nowhere as a judgement, and it creates no trust profiles. Graded and negative vouches will return only through a design of their own — a ceremony for giving them and protection against abuse. Measuring trust on this scale is a central long-term aim.

### 2.7. Core Functionalities

- **Self-sovereign identity management** — an identity you create, control and recover with keys only you hold, and details you record encrypted and reveal selectively.
- **Secure peer-to-peer communication** — Talk: end-to-end encrypted conversations and groups, delivered live or left on the ledger for an offline recipient ([06](06_P2P_Communication.md)).
- **Digital asset management** — your identity has an IOTA address; today you can view your balance and send and receive IOTA; the wider asset vision is described as a vision in [09](09_Data_Storage_And_Asset_Management.md) §5.
- **Decentralised trust management** — vouches given face to face, recorded on the ledger, counted today, measured in the long term ([07](07_Trust_Architecture_And_Management.md)).

## 3. Guiding Principles

### 3.1. Core Principles

The ten founding principles, by name, with where the running system stands against each.

| # | Principle | What it means | Today |
|---|---|---|---|
| 1 | **Open Technological Environment** | Any actor can take part with minimal friction and cost and maximal security. | The contracts accept a signed transaction from anyone; no API key, no allow-list. There is one client today, wot.id's own. |
| 2 | **Strict Peer-to-Peer Environment** | No intermediary owns a transaction or the data; actors deal with each other directly at the protocol level. | Holds for the ledger path: your browser signs and sends every transaction itself. Live messages pass through wot.id's relay, which forwards envelopes it cannot open; the relay and the hosted app are conveniences the design can remove. |
| 3 | **Guaranteed Human Identity** | A human can reliably identify themself and be verified by others. | Human verification is a vouch given face to face by a named person; it is evidence from people you can see, not a uniqueness guarantee. No biometric proof-of-personhood exists or is planned for the current stage. |
| 4 | **Absolute User Control & SSI Ownership** | Each actor keeps absolute control over their identity. | Holds: keys exist only on your devices; every change needs your signature; wot.id cannot sign as you, read what you encrypted, or restore your identity for you. |
| 5 | **Fair Value Distribution** | Actors own the value derived from their data and are rewarded for it. | Not built. There is no reward, incentive or token mechanism. |
| 6 | **Decentralized Governance** | Decisions by proposal and vote, with equal participation. | Proposal and voting building blocks exist in the contracts; there is no app surface for them. |
| 7 | **Effective Conflict Resolution** | Clear, fair, decentralised mechanisms to resolve disputes. | Design only; nothing is built ([10](10_Governance_And_Conflict_Resolution.md) §6). |
| 8 | **Dynamic Liquidity** | The system adapts to behaviour and context over time. | A design aim; no mechanism of its own exists in the app today. |
| 9 | **Intelligent Assistance** | An assistant acting for the user within permissions the user sets. | Future; nothing built (§2). |
| 10 | **Feeless Core Interactions** | The founding wording, from an earlier generation of the IOTA network. | On the current IOTA network every operation carries a small network fee — a few hundredths of a cent. For basic use wot.id currently pays it from its own gas wallet, under fair use; nothing is charged to you and nothing is paid to wot.id. Sending coins is paid from your own wallet. |

### 3.2. Technical Design Principles

The ten technical principles, in the same order as the internal document, each with what it means for the system you use.

1. **DAG-Based Consensus Architecture** — the IOTA network orders transactions with a Byzantine-fault-tolerant protocol over a directed acyclic graph; a change is final within seconds (§4.1).
2. **Decentralized Validator Network** — a committee of validators chosen by delegated proof of stake secures the ledger; no single party controls it.
3. **Real-Time, Low-Cost Transactions** — near real-time interaction; each operation carries a small, predictable fee (§3.1, principle 10).
4. **wot.id Stores VALUES, Not Files** — wot.id stores the values you extract from your documents, encrypted, on the ledger. Encrypted Files is a separate catalogue of files you chose to link: the catalogue is on the ledger, the encrypted bytes in the wot.id cloud, with an encrypted copy on your device if you keep one. One private key opens both ([09](09_Data_Storage_And_Asset_Management.md) §1).
5. **Security and Privacy by Design** — proven signatures, a contract language whose ownership model prevents whole classes of bugs, and three privacy bands for every value: readable by the people you admit to your circles, by named people you grant access to, or by you alone.
6. **Atomic Data Structure & Modularity** — identity is not one profile but many independent values, each shared or withheld on its own.
7. **Crypto-Agility & Future-Proof Security** — every key delivery combines X25519 with ML-KEM-768; every encrypted record carries a version and scheme number so a primitive can be replaced without rebuilding the system ([02](02_System_Architecture.md) §10.2).
8. **Device-to-Device Trust & P2P Flows** — identity is verified between devices held by people who meet: one shows a code, the other scans it.
9. **IOTA-Native and W3C-Compliant** — the contracts are written in Move and run on IOTA's base layer; the DID is minted on the ledger by your own signed transaction (§1.2).
10. **Universal TrustLevel & Selective Disclosure** — every vouch carries a level on the −100…+100 scale, written at one fixed value today (§2.6); every flow lets you show one detail to one person rather than a profile to everybody.

## 4. The IOTA Architecture: A Foundation for wot.id

### 4.1. The Core Ledger and Consensus

IOTA orders transactions with a Byzantine-fault-tolerant protocol over a directed acyclic graph, which lets validators process blocks in parallel and reach finality in a few rounds of messages. The validators form a committee chosen by delegated proof of stake. The unit of state is an *object with an owner*: an identity, a file catalogue and a vouch are objects, and "only the holder of the key can change this" is enforced by the ledger rather than by wot.id's code.

### 4.2. The Transaction Lifecycle

Every change you make in wot.id — a detail saved, a vouch given, a file registered, a message left for someone offline — follows the same four steps. **Prepare:** the app asks wot.id's server for the transaction; the server builds it with you as the sender and signs it as the payer of the fee. **Sign:** your device signs the same transaction with your identity's key; the key never leaves the device. **Broadcast:** your browser sends it, with both signatures, straight to an IOTA node. **Confirm:** the app tells the server the transaction's digest; the server reads the result from the ledger and updates what it shows you. Every contract checks the sender, and the sender is always you; wot.id's signature authorises nothing but the payment.

### 4.3. Security by Design

Access to anything on the ledger is controlled by key pairs: a transaction exists only with a valid signature. The contracts are written in Move, whose object-centric ownership model prevents many classes of bugs at the language level. Upgrades to a package are possible only with its upgrade capability and only within the ledger's compatibility rules ([05](05_Move_Smart_Contracts.md) §6).

## 5. Alignment with Broader Standards: Trust over IP (ToIP)

wot.id is designed as one instance of a digital trust ecosystem in the sense of the Trust over IP Foundation: a technology stack and a governance stack side by side, a layered architecture, and the principles of decentralisation, interoperability, end-to-end security and human-centricity. This is an alignment of design, not a certification; the governance stack is design today ([10](10_Governance_And_Conflict_Resolution.md)).

## 6. Implementation Status & Roadmap

### 6.1. Current Status (October 2026)

| Capability | State | Since |
|---|---|---|
| Identity on the ledger, named by its own object id (`did:wot:0x…`) | live | June 2026 |
| Sign-in with Google, Apple or GitHub | live | November 2025 |
| Sign-in with your private key (the 24 words) | live | June 2026 |
| Sign-in with a passkey | live | 25 September 2026 |
| Creating an identity with a passkey alone, without a provider account | live | 30 September 2026 |
| Encrypted personal details, hybrid post-quantum key delivery | live | December 2025 |
| Circles — choosing whom you admit | live | July 2026 |
| In-person verification — vouches on the ledger | live | November 2025 |
| Talk — encrypted messages, offline delivery, groups | live | groups since August 2026 |
| Encrypted Files — wot.id cloud by default, device copy, sharing | live | July 2026 |
| Network fees paid by wot.id for basic use | live | 15 September 2026 |
| Guardian recovery | live | 19 September 2026 |
| Contract package, current version | live | 2 October 2026 |
| Zero-knowledge proofs | planned, no date | — |
| Collective decisions (proposals, votes) | contract building blocks only, no app | — |
| Bounded delegation for agents | not built | — |
| Files in a cloud you choose | designed, not built | — |

### 6.2. Q2 2026 Pilot

An open beta for the IOTA community was planned for the second quarter of 2026 and did not launch: the quarter closed with the decision to drive the core loop to a reliability bar first. The pilot design is kept as the intended shape of the open beta; no date is given for it ([11](11_Onboarding_And_Adoption.md) §3).

### 6.3. Future Enhancements

Named in the internal documents as possible future work, none with a date or a decision: resolution of wot.id identities by external DID resolvers; proving a property without showing the detail (zero-knowledge proofs); storing files in a cloud the user chooses; signed, bounded delegation from a person to an agent; app surfaces for proposals, votes and conflict resolution.

## 7. Conclusion

wot.id is live on IOTA mainnet with identities controlled by their holders' keys, encrypted details, vouches recorded on the ledger, encrypted messaging and files, and guardian recovery. The principles above decide its design; this page says for each how far it holds today, so that none reads as a promise the system does not keep. The current effort is reliability of the core loop before outreach.

## 8. References

- IOTA architecture and consensus: https://docs.iota.org/about-iota/iota-architecture/
- IOTA transaction lifecycle: https://docs.iota.org/about-iota/iota-architecture/transaction-lifecycle
- Move concepts: https://docs.iota.org/developer/iota-101/move-overview/
- W3C Decentralized Identifiers (DID Core 1.0): https://www.w3.org/TR/did-core/
- ML-KEM (NIST FIPS 203): https://csrc.nist.gov/pubs/fips/203/final
- The IOTA mainnet explorer, where every id in these pages can be opened: https://explorer.rebased.iota.org
