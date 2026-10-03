# wot.id — public docs

*As of 2026-10-03. wot.id is an open peer-to-peer environment where any digitally connected actor — human, machine, organization, or otherwise — can communicate, manage and exchange assets, and handle trust — in complete privacy and with quantum-safe encryption.*

These eleven pages are the public version of wot.id's internal foundational documents. They carry the same numbers and the same titles, section for section, and each explains in general terms what its counterpart specifies in detail: how the system works, what it holds, who can see and do what, and where its limits are. Code, configuration and operating procedures stay out; nothing here is needed to understand the system or to check it against the ledger.

Every page carries the date it was written against. Where a page describes a capability, a status table says what is live and since when, so that nothing reads as a promise the running system does not keep.

## How to read

**In order** (each page stands on its own):

1. [01: Project Overview and Principles](01_Project_Overview_And_Principles.md) — what wot.id is, the long-term aim, the ten principles and how far each holds today, why IOTA, the status.
2. [02: System Architecture](02_System_Architecture.md) — the three parts, who holds and sees what, how one change reaches the ledger, the security architecture.
3. [03: IOTA Node and Network Setup](03_IOTA_Node_And_Network.md) — how wot.id talks to the IOTA network, and what it does not run itself.
4. [04: Backend API](04_Backend.md) — what wot.id's server does, what it never holds, and how a sign-in is proven.
5. [05: Move Smart Contracts](05_Move_Smart_Contracts.md) — the contracts on the ledger, what each holds, how they are upgraded, and their published ids.
6. [06: Secure Peer-to-Peer (P2P) Communication](06_P2P_Communication.md) — Talk: live relay, offline delivery on the ledger, the Mailbox, the encryption.
7. [07: Comprehensive Trust Architecture](07_Trust_Architecture_And_Management.md) — what a vouch is, what is counted today, and the trust scale the system is built towards.
8. [08: Frontend, UX, and Client Applications](08_Frontend_And_User_Experience.md) — the app in your browser: keys, the first hour, the sections, getting back in.
9. [09: Data Storage and Asset Management](09_Data_Storage_And_Asset_Management.md) — encrypted values on the ledger, the Encrypted Files catalogue, the Mailbox, the wallet.
10. [10: Governance, Security, and Standards](10_Governance_And_Conflict_Resolution.md) — governance as designed and as built, the threat model, what is protected and what is not, the standards followed.
11. [11: Onboarding and Adoption](11_Onboarding_And_Adoption.md) — what wot.id is and is not yet, how a newcomer starts, the abuse defences, how it compares with adjacent systems.

## Headlines

- **An identity that is yours.** It is an object on the IOTA mainnet ledger, named `did:wot:0x…` after its own object id, and controlled by a key — 24 words — generated in your browser and never sent anywhere ([01](01_Project_Overview_And_Principles.md) §1.3, [08](08_Frontend_And_User_Experience.md) §4.0).
- **Every change is signed on your device and sent to the ledger by your browser.** wot.id's server prepares the transaction and pays the network fee — a few hundredths of a cent, currently paid by wot.id for basic use. Its signature authorises nothing but that payment ([02](02_System_Architecture.md) §4.1).
- **Encrypted before it leaves the device.** Details, messages and files are encrypted on your device; every key delivery combines X25519 with ML-KEM-768, so captured ciphertext resists a future quantum computer. Signatures on the ledger remain classical until the ledger supports post-quantum ones ([02](02_System_Architecture.md) §10.2).
- **Trust is counted today; measuring it is the aim.** A vouch is a signed "this detail is true" by a named identity, bound to the exact version of the detail. The app shows counts — "3 people verified 5 of my details" — each opening to the records. No score is computed for any person yet; the protocol carries a trust scale from −100 to +100 for that long-term aim ([07](07_Trust_Architecture_And_Management.md)).
- **Three ways back in.** Your 24 words, a passkey backup, or two guardians you chose plus a 12-word recovery code. wot.id holds no key, no share and no code ([08](08_Frontend_And_User_Experience.md) §4.0).
- **Named limits.** The app is served by wot.id; the contracts are upgradeable by wot.id with an offline key; the ledger shows the structure around encrypted content. [10](10_Governance_And_Conflict_Resolution.md) §8 states each one.
- **Checkable.** The contract package, the registry and the offline-messages package are published by id in [05](05_Move_Smart_Contracts.md) §6.1 and readable on the ledger as deployed bytecode. The source code is not published.

## What is deliberately not here

- Code-level detail: file paths, function names and signatures, transaction layouts, the server's internal structure.
- Exact key-derivation labels, rate limits and quotas as numbers, and the server's configuration.
- Operator runbooks, deploy procedures, internal issue numbers.
- Roadmap dates. What is planned or designed is marked as such, without a date.

The foundational documents hold all of this. These pages are their general, public reading.
