# 06: wot.id - Secure Peer-to-Peer (P2P) Communication

*As of 2026-10-03. The public version of the foundational document of the same name: Talk — how messages travel live and offline, how they are encrypted and signed, how history follows you to another device, and where the limits are. General mechanism, no implementation detail.*

---

## Current Implementation Status

Talk — wot.id's messaging — works in production for online and offline peers.

| Capability | State | Since |
|---|---|---|
| Live delivery through wot.id's relay | live | December 2025 |
| End-to-end encryption, mandatory for every message | live | April 2026 |
| Every message signed by the sender and checked against the sender's published key | live | 25 July 2026 |
| Offline delivery through a drop box on the ledger | live | July 2026 |
| History across devices — the Mailbox, cloud by default, opt-out | live | July 2026 |
| Group conversations | live | 8 August 2026 |
| Sharing an Encrypted File from a conversation | live | 10 September 2026 |
| Direct browser-to-browser transport | implemented, switched off | — |
| Forward secrecy | not built, by design | — |

## 1. Overview and Architectural Rationale

Talk is built for verified peer-to-peer interaction between any digital actors — people, organisations, devices, services — with mandatory end-to-end encryption and the keys in the actors' hands. The honest boundary: encryption gives content confidentiality against a passive or fully compromised relay, and every message is signed by its sender. It is not Signal-grade: there is no forward secrecy (messages are encrypted to long-term keys), and the app trusts wot.id's server for the lookup of a peer's public key without a second check.

### 1.1. Why a Separate P2P Stack?

Messages are not identity data: they are high-volume, two-party, and mostly ephemeral. They travel outside the ledger whenever both sides are online, and touch the ledger only for offline delivery.

### 1.2. Why WebSocket relay + WebRTC (and not libp2p)

The production transport is a relay run by wot.id: each connected device opens one authenticated connection, and the relay forwards sealed envelopes between connected identities. A direct browser-to-browser transport is implemented but switched off by default. An earlier general peer-to-peer networking stack was removed in May 2026 as more than the system needed.

### 1.3. The `wot.id` P2P Communication Stack

Transport (the relay, with the ledger drop box as the offline fallback), encryption (the hybrid scheme of [02](02_System_Architecture.md) §10.2, a fresh key per message), sender authentication (a signature on every envelope), and storage (the conversation history on the device, and the encrypted Mailbox in the cloud by default).

## 2. Unified Identity for All Actors: Humans, Devices, and Services

Every participant — a person, a device, an autonomous service — has an identity of the same kind and talks the same way. A device can hold its own identity; a person can talk to a person or to a device; a service can be identified and addressed like anyone else ([11](11_Onboarding_And_Adoption.md) §1.1).

### 2.1. A Note on Legacy IOTA Streams

The messaging framework of the earlier IOTA network is not part of IOTA Rebased; Talk fills that role at the application layer.

## 3. Communication Modes

| Mode | Use | Mechanism | Persistence |
|---|---|---|---|
| Live | real-time conversation | the relay forwards sealed envelopes between connected devices | on the devices only |
| Offline | the recipient is not connected | your app leaves the sealed message in the recipient's drop box on the ledger; their app collects it later | on the ledger until claimed |

## 4. Layer 1: Transport & Peer Discovery

### 4.1. Peer Discovery via DID

Peers are found by identity, never by network address. You start a conversation by scanning someone's code, showing yours, or entering their DID. Your device connects to the relay under your own identity, proven by your session; the relay routes an envelope to the recipient's live connection, or tells your app that the recipient is not connected, in which case your app deposits the message in their drop box. The relay never deposits anything itself.

### 4.2. NAT Traversal

The relay makes network traversal unnecessary. The optional direct transport, when switched on, uses standard browser mechanisms.

## 5. Layer 3: The `wot.id` Trust & Security Protocol (TSP)

### 5.1. Message Envelope

An envelope carries the sender's and recipient's identities, a timestamp, the kind of message, the sealed content, and the sender's signature over the whole. The content is encrypted on the sender's device to each recipient's published keys; the signature is checked on the recipient's device against the sender's public key on the ledger, and a message that fails the check is rejected.

### 5.2. Message Types and Payloads

Text, and a few system kinds the app uses among itself. A file shared from a conversation travels as a catalogue share plus one ordinary encrypted text line announcing it; no file bytes travel through Talk.

## 6. Core Interaction Flows

### 6.1. Flow 1: Context-Aware Handshake (Off-Chain)

Today a conversation begins when two identities know each other's DID — by code or by typing. A handshake that exchanges verifiable credentials before talking is design, not built.

### 6.2. Flow 2: Asynchronous Messaging (On-Chain Offline Messages)

When the relay reports the recipient absent, the sender's app deposits the sealed message in the recipient's drop box on the ledger, with wot.id paying the fee. When the recipient's app comes online it lists the drop box, decrypts the waiting messages, stores them, and claims them with a signed transaction, which removes them from the ledger. The full path was verified on mainnet in July 2026.

## 7. On-Chain Offline Messages: Technical Deep Dive

### 7.1. The `wot_offline_messages` Package (and the Legacy Module)

Offline messages live in their own package, published on 25 July 2026, so that it can evolve without an upgrade of the main package; its id is in [05](05_Move_Smart_Contracts.md) §6.1. The first-generation mailbox module inside the main package was retired the same day and is called by nothing.

### 7.2. Core Contract Definition

Each identity has one drop box, created on first use and owned by that identity. Anyone can deposit a message into it; only the owner can claim or delete. The drop box is bounded in how many messages it holds and how large each may be; it is for delivery, not for archiving.

### 7.3. Backend Integration

wot.id's server prepares the deposit and claim transactions, sponsors their fee, and confirms them; your device signs them and your browser broadcasts them, as for every write.

### 7.4. Gas and Signing Model

The depositor signs as sender and wot.id's gas wallet pays the fee; the claim is signed by the drop box's owner. The server signs nothing on anyone's behalf.

### 7.5. Production Verification

The complete offline path — relay miss, deposit, listing, decryption, durable storage, signed claim, display, survival of reload and re-login — was verified end to end on mainnet on 8 July 2026, and again when the separate package was published on 25 July 2026.

### 7.6. IOTA Move Patterns Used

The drop box is a shared object so that any sender can deposit; each message is attached to it as its own object; owner-only functions check that the signer is the owner; a claimed message is transferred to the owner and then destroyed.

### 7.7. Privacy and Cost Trade-Offs

The content of an offline message is ciphertext, but its envelope is not: the sender's identity, the timestamp, the kind of message and the signature are readable on the ledger, as are the transaction's sender, the recipient's drop box, the time and the size. Anyone reading the ledger can see that one identity left a message of some size for another at some time; not what it said. The fee of a deposit grows with the message's size and is paid by wot.id.

## 8. Post-Quantum Cryptography (PQC) Strategy

Message content is encrypted with the hybrid X25519 + ML-KEM-768 key delivery and ChaCha20-Poly1305, with a fresh key per message, so captured ciphertext resists a future quantum computer. Signatures on envelopes and on the ledger are classical Ed25519, because the ledger verifies only classical schemes; post-quantum signatures wait on the ledger supporting them. There is no forward secrecy: whoever obtains your private key can open every message encrypted to it that they have captured, including offline messages that are still on the ledger ([02](02_System_Architecture.md) §10.9).
