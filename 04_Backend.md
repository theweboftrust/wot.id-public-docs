# 04: wot.id - Backend API

*As of 2026-10-03. The public version of the foundational document of the same name: what wot.id's server does, what it holds and sees, what it never holds, how a sign-in is proven, and how it is run. General mechanism, no implementation detail.*

---

## Current Implementation Status

wot.id's server is one service. It prepares transactions and pays their fee, proves sign-ins and issues sessions, relays live messages, issues the short-lived links through which encrypted files and the Mailbox are stored and fetched, and reads the ledger to show you the current state. It holds no key of yours, reads nothing you encrypted, and keeps no database of identity data.

| Capability | State | Since |
|---|---|---|
| Sign-in by provider, by private key, by passkey | live | November 2025 · June 2026 · 25 September 2026 |
| Identity creation with a passkey alone, sponsored within limits | live | 30 September 2026 |
| Every write prepared with the user as sender and sponsored by the gas wallet | live | 15 September 2026 |
| Live message relay | live | December 2025 |
| Encrypted cloud copies — Mailbox and file bytes | live | July 2026 |
| Guardian recovery routes | live | 19 September 2026 |
| Administrative account reset | off unless switched on; routes absent while off | — |

## 1. High-Level Architecture

The server is the orchestration layer for encrypted values: it receives ciphertext from your browser, builds the transactions that write it to the ledger, and reads it back for display. It never sees a plaintext value and never holds a file's key. For Encrypted Files it maintains the catalogue on the ledger through transactions you sign, and keeps the encrypted bytes in the wot.id cloud behind a header that names the owner.

| It holds | It sees | It never holds or sees |
|---|---|---|
| The key of its own gas wallet, which pays fees | Which identity signs in and when; the e-mail a provider sign-in gives it, for that session | Your private key, any key derived from it, your passkey |
| Your session and the one-time sign-in challenges | Which transactions it prepares, for whom, and when they land | The value of any detail |
| Encrypted copies in the wot.id cloud: file bytes and, by default, your Mailbox (stored in the EU) | The size and timing of those copies; the owner's name on each | Their content, their file names |
| Live connections for message relay | Who sends an encrypted message to whom, when, how large | The message text |
| A secret key that turns e-mail addresses into the unreadable identifiers the ledger stores | Whether a given e-mail address is linked to an identity | — |

If the server were entirely compromised, the attacker would hold the gas wallet and the encrypted copies, and could stop relaying or preparing transactions. They could not sign as you, open your data, or change what you signed. The app that runs in your browser is served by wot.id, so a compromised deployment of the app itself is a different matter ([02](02_System_Architecture.md) §10.9).

### 1.1. W3C DID Implementation

Your identity's name, `did:wot:0x…`, is minted on the ledger by your own signed creation transaction; the server reads it back and registers it. It does not construct the name and does not generate keys. No DID document is served ([01](01_Project_Overview_And_Principles.md) §1.2).

### 1.2. Identity Architecture: DID as Primary Identifier

The DID is the identity. E-mail addresses confirmed by Google, Apple or GitHub are doors to it, recorded on the ledger as keyed, unreadable hashes that map to the DID. On a provider sign-in the server looks the hash up; if it finds an identity it opens a session for it, and if not, the app offers to create one.

### Backend API Responsibilities

| Responsibility | What the server does |
|---|---|
| Sessions | proves sign-ins (§2.3) and issues sessions tied to an identity |
| Transactions | builds every write with you as sender, co-signs as fee payer, confirms by digest |
| Reads | reads profiles, vouches, catalogues and events from the ledger for display |
| Relay | forwards sealed message envelopes between connected devices |
| Cloud | issues short-lived upload and download links for encrypted file bytes and the Mailbox |
| Limits | enforces the rate limits of [02](02_System_Architecture.md) §10.4 |
| Database | none — the ledger is the record; the cloud tier holds ciphertext only |

## 2. Backend API

### 2.1. Current Status

In production at wot.id, deployed as one service at a cloud host, reaching IOTA mainnet through a public node endpoint ([03](03_IOTA_Node_And_Network.md)).

### 2.2. API Endpoints

The server's routes fall into families: sign-in and sessions; identity creation and the identity's details; vouches (the face-to-face ceremony, the records, erasure); circles and contacts; Encrypted Files (the catalogue, cloud links, sharing and revocation); Talk (the relay, offline messages, the Mailbox); guardian recovery; the wallet; and a few public reads such as an identity's published public key. Every write route comes as a pair — one call prepares the transaction, one confirms it after your browser has broadcast it. Route names and shapes are not part of the public description.

### 2.3. DID Ownership Verification (Option C)

A sign-in with a passkey or with the private key is a challenge-response proof. The server issues a random one-time challenge; your device signs it with your identity's key; the server checks the signature and, decisively, that the public key is the one your identity published on the ledger. Only then does it issue a session, and it issues it to the caller who made the proof — nothing is stored that another caller could reuse. A provider sign-in opens a session on the provider's confirmation of your e-mail instead; it never involves your key.

### 2.4. Performance Optimizations

The server keeps short-lived caches of what it reads from the ledger and drops them when it confirms a change, so that the app shows a new state without re-reading everything.

### 2.5. PQC Encrypted Identity Handling

The server handles your details only as ciphertext produced on your device. It holds none of the encryption primitives needed to open them and none of the keys; it checks sizes and encodings, builds the transaction, and passes the ciphertext through ([02](02_System_Architecture.md) §10.2).

## 3. Identity Service (RETIRED — March 2026)

Identity generation once lived in a separate service. It was retired in March 2026, and since the summer of 2026 identities are minted on the ledger by the user's own transaction, so no server component generates identities or keys at all.

## 4. Current Integration Status

### 4.1. Service Overview

One server, one app, one ledger, one cloud tier for ciphertext. The app is at https://wot.id.

### 4.2. JWT Authentication Flow

A session is a signed token tied to your identity. It is issued after a proven sign-in (§2.3) or a provider confirmation, expires, and is renewed while the device can still prove itself. Every protected route requires it. A short-lived creation token exists for one purpose only: a first visit creating an identity with a passkey alone proves possession of its new key and receives a token that is valid for a few minutes and for the creation routes alone.

### 4.3. Real Data Flow

Everything the app shows you comes from the ledger: your profile object, your encrypted details (decrypted on your device), the vouches, your file catalogue, your offline messages, your recovery settings. The server adds nothing of its own except the cloud copies you asked for.

## 5. Interaction Flows

Creating an identity, saving a detail, giving a vouch, sending a message and sharing a file all follow the four steps of [01](01_Project_Overview_And_Principles.md) §4.2 and the flows of [02](02_System_Architecture.md) §5.2.

## 6. Configuration (Environment Variables)

The server is configured through environment settings: the ledger endpoint, the published contract ids, the gas wallet's key, session secrets, switches and limits. The names and values are not public. The contract ids are ([05](05_Move_Smart_Contracts.md) §6.1).

## 7. Deployment and Operational Notes

The server runs as one container at a cloud host and is deployed automatically from the main line of the code. Its logs carry no plaintext e-mail addresses and no session tokens. Operations are reviewed every day from a fixed window of those logs; the records are internal.

## 8. Architectural Benefits & Current Status

One service with no database: nothing to synchronise, one place for secrets, one health check. The ledger is the single record; the server is replaceable without touching your data ([02](02_System_Architecture.md) §12).

## 9. Integration Achievements

Sign-in by three doors, identity creation by two, user-signed and sponsored writes for every change, live relay and offline delivery, encrypted cloud copies, guardian recovery — all running in production on IOTA mainnet.

## 10. IOTA Identity Framework Reference

wot.id does not use the IOTA Identity framework. Identities are wot.id's own contracts; the W3C DID idea is followed without its document format ([01](01_Project_Overview_And_Principles.md) §1.2).

## 11. External Resources and Contribution

The source code is not published. The contracts are readable on the ledger as deployed bytecode at the ids in [05](05_Move_Smart_Contracts.md) §6.1. For an integration, a research collaboration or an audit, these pages are the starting point for the conversation.
