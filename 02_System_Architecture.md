# 02: wot.id - System Architecture

*As of 2026-10-03. The public version of the foundational document of the same name: the parts of the system, what each holds and sees, how one change reaches the ledger, the security architecture, and the current status. General mechanism, no implementation detail.*

---

## 1. Introduction

wot.id has three parts — the app in your browser, the contracts on the IOTA mainnet ledger, and wot.id's server — plus one store beside them, the wot.id cloud, which holds encrypted bytes. This page describes how they fit together and what each can and cannot do.

### 1.1. Foundational Concept: wot.id as Interface, Not Owner

wot.id is an interface to data stored on the public ledger; it is not the owner or gatekeeper of that data. You need only your identity's name and your key to reach everything that belongs to you; the app is one way of doing so. What follows from this is best read as two tables.

**Who sees what**

| Item | Your device | wot.id's server | The public ledger | Other people |
|---|:---:|:---:|:---:|:---:|
| Your private key (24 words) and every key derived from it | ✓ | — | — | — |
| The value of each detail | ✓ | — | ciphertext | people you admitted (name, account kind); the one verifier you showed a detail to |
| Field labels ("first name") and when each changed | ✓ | ✓ | ✓ | ✓ |
| Message text | ✓ | — | ciphertext (offline delivery only) | the recipients |
| Who messages whom, when, how large | ✓ | ✓ | ✓ for offline delivery | ✓ for offline delivery |
| File content, name and type | ✓ | — | ciphertext / sealed | people you shared with |
| File size, category and hash | ✓ | ✓ | ✓ | ✓ |
| Vouches: who vouched for whom, for which detail, when | ✓ | ✓ | ✓ | ✓ |
| Whom you admitted to your circles (not which circle) | ✓ | ✓ | ✓ — derivable from public data | ✓ |
| Your wallet address, balance, every transaction and its time | ✓ | ✓ | ✓ | ✓ |
| That wot.id paid the fee — which marks your address as a wot.id user | ✓ | ✓ | ✓ | ✓ |
| Your guardians | ✓ | — | salted hashes only, until a guardian approves a recovery | — |

The pattern: **content is encrypted everywhere; structure is public on the ledger.** Nothing on the ledger names you — the link between your address and you exists only where you make it — but the graph of who vouched for and admitted whom, and when, is readable by anyone patient enough to read it. Hiding the structure as well needs commitments in place of readable links and zero-knowledge proofs over them; that is planned, not built.

**Who controls what**

| Action | Who can do it |
|---|---|
| Create your identity | You, by signing the creating transaction on your device |
| Add or change a detail about yourself | You, by signing |
| Vouch for someone's detail | Whoever vouches signs — the voucher is taken from the signature |
| Erase a vouch about you | You, by signing |
| Read a message to you, open a file shared with you | You — your key opens it |
| Get back into your identity without your device | You: your private key, your passkey backup, or two guardians you chose plus your recovery code |
| Stop you from publishing what you signed | Nobody on the ledger. wot.id could stop preparing or paying for your transactions; you could still submit them yourself, paying your own fee. |
| Change the contracts | **wot.id**, by an upgrade signed with a key held in an offline wallet ([05](05_Move_Smart_Contracts.md) §6) |

### 1.2. Atomic Data Point Model

Every piece of your data has the same shape: one value, encrypted on your device, stored on the ledger under its label (first name, date of birth, nationality, …) with the time it last changed, a privacy band saying who may read it, a trust level that is reserved today, and the vouches other identities have signed for it. Values are independent of one another: you can reveal one without the others, and a vouch is about one value at one version.

### 1.3. Claims, Attestations, and Trust Scores

Three words recur across these pages. A **claim** is a value you recorded about yourself — these pages call it a *detail*. An **attestation** is a signed statement by another identity that one of your details is true, or that you are a human being — these pages call it a *vouch*. A **trust score** would be a number computed from the vouches; wot.id computes none for any person today. What the app shows is counts — "3 people verified 5 of my details" — each opening to the individual records on the ledger. Measuring trust on the protocol's −100…+100 scale is the long-term aim ([07](07_Trust_Architecture_And_Management.md)).

## 2. Architectural Drivers

One server program handles everything wot.id's side does: it confirms who is signing in and issues sessions, prepares transactions and pays their fee, relays live messages between connected devices, hands out the short-lived links through which encrypted files and the Mailbox are stored and fetched, and reads the ledger to show you the current state. It holds no key of yours and keeps no database of identity data. The app in the browser does the rest: keys, encryption, signing, and sending signed transactions to the ledger itself.

## 3. System Components

- **The app in your browser** — creates and holds your keys from your 24 words, encrypts every value, message and file before it leaves the device, signs every transaction and broadcasts it to an IOTA node, and reads objects from the ledger directly where it can.
- **wot.id's server** — prepares transactions, pays their fee, relays live messages, keeps encrypted copies in the wot.id cloud, confirms transactions by their digest.
- **The IOTA mainnet ledger** — holds your identity object, your encrypted details, the vouches, your file catalogue, your offline messages and your recovery settings; public, permanent, readable by anyone.
- **The wot.id cloud** — storage run by wot.id in the EU for encrypted file bytes and, by default, your encrypted Mailbox. It holds ciphertext it cannot read.

### 3.1. W3C DID Implementation

Your identity's name is a decentralised identifier in the W3C sense, `did:wot:0x…`, formed from the object id of your profile on the ledger and minted by your own signed creation transaction. No DID document is served and no verifiable credential format is issued; the identity and its vouches live as ledger objects ([01](01_Project_Overview_And_Principles.md) §1.2).

### 3.2. Identity Identifier Architecture

The DID is the primary identifier and never changes. Secondary identifiers are doors to it: an e-mail address confirmed by a provider is recorded as a keyed, unreadable hash that maps to the DID, so a later sign-in with the same provider finds the same identity. Sign-in with a passkey or with the private key needs no secondary identifier at all: the key's address leads to the identity ([01](01_Project_Overview_And_Principles.md) §1.3).

### 3.3. Data Storage Architecture

The values you record are stored on the ledger, encrypted. The catalogue of files you chose to store under Encrypted Files is stored on the ledger too — hash, encrypted file key, size, category, sealed name and type, and a pointer to the bytes — while the encrypted bytes of every file stored today sit in the wot.id cloud, with an optional encrypted copy on your device. wot.id runs no database for identity data; the cloud tier is its one persistent store, and it holds ciphertext only ([09](09_Data_Storage_And_Asset_Management.md)).

## 4. On-Chain Architecture: Identity Registry

A shared registry object on the ledger maps names to identities: the DID to its profile object, and the keyed hashes of secondary identifiers to the DID. Anyone can look up a profile by DID without a central index. Profiles themselves are owned objects: each is controlled by the address of the key that created it, and only that key can change it.

### 4.1. Transaction Execution — Mixed JSON-RPC + CLI

Every change takes four steps ([01](01_Project_Overview_And_Principles.md) §4.2): the server prepares a transaction with you as sender and signs it as fee payer; your device signs it; your browser broadcasts it, with both signatures, straight to an IOTA node; the server confirms it by reading the ledger. A prepared transaction is valid only with the coin the server named for the fee, and an abandoned one expires within minutes. Because wot.id pays the fee, your wallet needs no balance to create or use an identity; a daily allowance of sponsored operations per identity — more than any person needs, less than a script wants — keeps the arrangement fair. Sending coins is the one operation you pay yourself.

## 5. Architectural Diagrams

### 5.1. Component Overview

```
┌──────────────────────────────────────────────────────────────┐
│  Your device — the wot.id app in your browser                │
│  · creates and holds your keys (from your 24-word key)       │
│  · encrypts every value, message and file before it leaves   │
│  · signs every transaction; sends it to the ledger itself    │
└───────────────┬───────────────────────────┬──────────────────┘
                │ unsigned transactions,     │ signed transactions
                │ encrypted messages/files   │ (broadcast directly)
┌───────────────▼──────────────┐            │
│  wot.id's server             │            │
│  · prepares transactions     │            │
│  · pays their network fee    │            │
│  · relays live messages      │            │
│  · keeps encrypted copies    │            │
│  · holds no key of yours     │            │
└───────────────┬──────────────┘            │
                │ confirms by digest         │
┌───────────────▼────────────────────────────▼─────────────────┐
│  IOTA mainnet ledger                                          │
│  · your identity object, encrypted details, vouches,          │
│    file catalogue, offline messages, guardian settings        │
│  · public, permanent, readable by anyone                      │
└──────────────────────────────────────────────────────────────┘
```

### 5.2. Interaction Flows

- **Signing in.** The server issues a one-time challenge; your device signs it with your identity's key; the server checks the signature against the public key your identity published on the ledger and opens a session. With a provider sign-in, the provider confirms your e-mail and the server opens a session for the identity linked to it; on a device without keys the app then asks for your passkey backup or your 24 words.
- **Creating an identity.** Your browser generates the 24 words, derives your keys, and signs the transaction that creates your profile object on the ledger; the server prepares it and pays its fee. Your DID is the new object's id.
- **Saving a detail.** The value is encrypted on your device under a key derived from your private key; the ciphertext goes to the ledger with its label.
- **Giving a vouch.** The verifier's device signs a transaction that records who vouched, for whose detail, at which version, and when; the voucher's address is taken from the signature.
- **Sending a message.** The text is encrypted on your device to each recipient's published keys and signed; the relay forwards the sealed envelope, or, when the recipient is offline, your app leaves it in their drop box on the ledger.
- **Sharing a file.** Your device re-wraps the file's key for the recipient; the recipient accepts with their own signed transaction and can then fetch and decrypt the bytes.

## 6. On-Chain Interaction Model: Mixed JSON-RPC + CLI

State changes are programmable transaction blocks: the server builds each one, co-signs it as the payer of the fee, your device signs it as the sender, your browser submits it to the network, and the server confirms it by digest. Reads — your profile, a vouch, a catalogue entry — come from the public node API, either through the server or directly from the browser. No part of this needs a wallet extension or a balance of your own.

## 7. Network and Environment

### 7.1. Default Ports

The app and the server listen on ordinary web ports behind their hosts. The numbers are not part of the public description.

### 7.2. Environment Variables

The server and the app are configured through environment settings — the ledger endpoint, the published contract ids, keys and switches. Their names and values are not public; the contract ids they point at are ([05](05_Move_Smart_Contracts.md) §6.1).

## 8. Deployment and Startup

The app is served from wot.id's hosting; the server runs as one service at a cloud host and is deployed automatically from the main line of the code. Both talk to IOTA mainnet through a public node endpoint; wot.id runs no node of its own ([03](03_IOTA_Node_And_Network.md)).

## 9. References

- IOTA architecture: https://docs.iota.org/about-iota/iota-architecture/
- Programmable transaction blocks: https://docs.iota.org/developer/iota-101/transactions/ptb/programmable-transaction-blocks-overview
- The IOTA mainnet explorer: https://explorer.rebased.iota.org

## 10. Security Architecture

### 10.1. Security Overview

Several independent controls hold at once: keys that exist only on your devices; encryption of all content before it leaves them; signatures checked by the contracts, so the sender is always you; sessions proven by signature; rate limits on what the server will prepare and sponsor; validation of every input; a browser security policy for the served app; and crypto-agility, so that a primitive can be replaced. A guarantee here is a property of how the system is built — it holds whatever wot.id wants, because wot.id lacks the key, the data or the power to break it. §10.9 names where each guarantee ends.

### 10.2. Post-Quantum Cryptography (PQC)

| Job | Primitive |
|---|---|
| Your private key | 24 words (BIP-39, 256 bits) — the words decode directly to the key |
| Signing transactions and messages | Ed25519; your address is a hash of its public half |
| Delivering a key to someone — you on a new device, a peer, a guardian, a file recipient | Hybrid key encapsulation: X25519 and ML-KEM-768 (NIST FIPS 203); both secrets feed one derived key, so breaking one of the two is not enough |
| Encrypting values, messages and files | ChaCha20-Poly1305, with a separate derived key per field and a fresh random key per message and per file |
| File integrity | SHA-256 of the original, recorded on the ledger |
| Passkey wraps — the backup on the ledger, the keys at rest on the device | a secret produced by the passkey, AES-256-GCM |

All keys derive from the one private key: the same 24 words give the same keys on any device, and nothing else needs backing up.

**The quantum note.** A future quantum computer threatens two things differently. Captured ciphertext is protected: every key delivery includes ML-KEM, so data recorded today cannot be opened later by breaking the classical half. Signatures are not yet: the ledger verifies only classical schemes, so its signatures are Ed25519 until it supports a post-quantum one. The design is crypto-agile: every encrypted record carries a version and a scheme number.

### 10.3. Authentication System

Three doors open a session, and each proves the same thing — that the device holds your identity's key.

| Door | What happens | What the server learns |
|---|---|---|
| **Passkey** | One biometric opens the keys stored on this device, and they sign a fresh challenge. | A signature from your identity's key, checked against the public key your identity published on the ledger. Nothing about the passkey itself. |
| **Google, Apple or GitHub** | The provider confirms your e-mail; wot.id opens a session for the identity linked to it. On a device that has no keys yet, the app then asks for your passkey backup or your 24 words. | That you signed in with this provider. Never your key. |
| **Private key** | You type the 24 words; the app derives the keys, finds your identity from the address they produce, and signs the challenge. | The same signature as with a passkey. The words themselves never leave the device. |

A first visit can also **create** an identity with a passkey alone, without any provider account: the app generates the 24 words in memory and shows them once, proves possession of the new key to the server, and the server sponsors the creation; the passkey is set up last. The provider door remains the other way to start ([08](08_Frontend_And_User_Experience.md) §4.1). A session is tied to the identity, expires, and renews while the device can still prove itself. Signing in never moves your identity; it only proves that the device holds its key.

### 10.4. Rate Limiting

The server limits what any one caller can ask of it: sign-in attempts per address and per account, sponsored transactions per identity per day and per minute and for the whole service per day, and identities created with a passkey alone per connection per day and in total. When a limit is reached the request is refused and the app says so; nothing is created. The numbers are not part of the public description; each is set well above what a person needs and well below what a script wants.

### 10.5. Input Validation

Every value the server receives is checked for shape before it is used: identifiers, addresses, encodings, lengths. Content it cannot read — your ciphertext — is checked for size and encoding only.

### 10.6. W3C DID Implementation

As §3.1: the DID idea is followed, the DID document and verifiable credential formats are not served.

### 10.7. Middleware Architecture

Every server route except the sign-in and creation routes and a few public reads requires a valid session. Browsers may call the server only from wot.id's own origins and its preview deployments. The app is served with a content security policy and the standard security headers — strict transport security, no framing by other sites, a restrictive referrer policy, and a permissions policy that allows only the camera, for scanning codes.

### 10.8. Security Configuration

The server's security switches and keys are configuration, not public. One fact about them worth stating: the administrative reset of an account is off unless it is switched on explicitly, and while it is off its routes do not exist at all.

### 10.9. Security Threat Model

| If … | What holds | What does not |
|---|---|---|
| wot.id's server were entirely compromised | The attacker could not sign as you, open your data, or change what you signed. | They would hold the gas wallet and the encrypted copies, see the metadata of §1.1, and could stop relaying or preparing transactions. |
| the app you are served were malicious | — | A compromised deployment could read your keys while you use it. The source is not published, so you cannot compare the code you are served with a published build. This is the largest trust you place in wot.id; nothing is built yet that removes it. |
| your device were compromised | Your other devices and the ledger are untouched. | Malware on a device you are using can act as you while it is there. Without a passkey, the keys at rest in the browser's storage are protected from nobody who can read that storage. |
| someone obtained your 24 words | — | They can act as you, and there is no way today to move an identity to a new key. Guardian recovery restores the same key; it does not replace a stolen one. |
| a quantum computer existed | Everything encrypted is protected by the ML-KEM half of every key delivery. | Ledger signatures are classical until the ledger supports post-quantum ones. |
| a group of identities vouched for one another | The counts open to named identities; you can see whether you know any of them. | The counts do not weigh who the vouchers are; a ring can produce any count. |

### 10.10. Integration with wot.id Architecture

Security is not a layer added at the edge: the contracts check the sender, the device holds the keys, the server sees ciphertext, and the ledger keeps the record. Each part enforces what it alone can enforce.

### 10.11. Frontend Security Architecture

Your keys are derived in the browser from the 24 words and kept in the browser's storage for this device. When you sign in with a passkey, the stored keys are wrapped under a secret only the passkey produces, so opening them takes a biometric and they lock again after a period without activity. Without a passkey they are protected only by the device itself. Talk conversations stored on the device have no encryption layer of their own. The served app carries a content security policy (§10.7). A device is as safe as its lock screen and its software.

### 10.12. Future Security Enhancements

Post-quantum signatures on the ledger wait on the ledger supporting them. Zero-knowledge proofs — hiding the public structure and proving a property without showing the detail — are planned, without a date. Checking the served app against a published build, and moving an identity to a new key, are not built.

## 11. Current System Status

### 11.1. End-to-End Identity System Operational

The whole chain runs in production: sign in by any of the three doors, create an identity your own key controls, record encrypted details, give and receive vouches, talk, store and share files, set up guardians. Every write is user-signed and sponsored by wot.id.

### 11.2. IOTA Mainnet Deployment

The contracts run on IOTA mainnet as one package, upgraded for the fifth time on 2 October 2026, plus a separate package for offline messages ([05](05_Move_Smart_Contracts.md) §6.1). The server talks to mainnet through a public node endpoint.

### 11.3. Operational Status

The app is at https://wot.id. Operations are reviewed daily from the server's own logs, which carry no plaintext e-mail addresses and no session tokens.

### 11.4. Architecture Benefits Realized

One server, no database, no wallet extension, no balance needed to start; keys and encryption in the browser; the ledger as the single record.

| Mechanism | State | Since |
|---|---|---|
| Browser signs and broadcasts every transaction; wot.id pays the fee | live | 15 September 2026 |
| Identity as a ledger object named by its own id | live | June 2026 |
| Hybrid X25519 + ML-KEM-768 key delivery | live | December 2025 |
| Both halves of the hybrid key pair derived from the 24 words | live | May 2026 |
| Passkey-wrapped keys on the device | live | September 2026 |
| Sealed file names and types on the ledger | live | September 2026 |
| Content security policy and security headers on the served app | live | September 2026 |
| Commitments instead of readable links; zero-knowledge proofs | planned, no date | — |
| Post-quantum signatures on the ledger | waits on the ledger | — |

## 12. Future Considerations

wot.id's servers are conveniences, never necessities for meaning or access. Every new piece is judged by one question: could a browser with the ledger and the user's keys do this without us? Today the browser already signs every transaction, sends it to the ledger itself and reads objects straight from the ledger; the server still prepares transactions, pays fees, relays live messages and keeps the cloud copies. Each of those is built to be removable without touching your data; the end state is no wot.id-operated server at all. That is a design constraint, not a date.
