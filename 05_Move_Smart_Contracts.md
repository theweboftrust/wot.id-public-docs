# 05: wot.id - Move Smart Contracts

*As of 2026-10-03. The public version of the foundational document of the same name: the contracts on the IOTA ledger, what each holds, how they are called and upgraded, and their published ids. General mechanism, no implementation detail.*

---

## Current Implementation Status

wot.id's contracts run on IOTA mainnet as one package, written in Move, deployed in January 2026 and upgraded in place since. The current version went live on **2 October 2026**, the fifth upgrade signed from an offline wallet (§6). Beside it runs a separate, smaller package for offline messages, published on 25 July 2026.

| Module | What it holds | State |
|---|---|---|
| Identity registry | a shared object mapping names to identities: the DID to its profile, keyed hashes of e-mail addresses to the DID | live |
| Identity profiles | one owned object per identity: encrypted details with their labels, privacy bands and timestamps, the published public keys | live |
| Trust | the vouches, each bound to one detail at one version, with its status; a trust profile type; proposal and voting building blocks | live; proposals and votes without an app surface |
| File vault | one owned catalogue per identity: per file its hash, encrypted file key, size, category, sealed name and type, storage pointer; share offers, accepted shares, revocations | live |
| Recovery | guardian settings and recovery requests | live |
| Mailbox (first generation) | retired from service in July 2026; frozen on the ledger, called by nothing | retired |
| Offline messages (separate package) | one drop box per identity where encrypted messages wait for an offline recipient | live |

Every upgrade has also retired older entry points by making them refuse to run, so that superseded or unsafe ways of writing — plaintext values among them — are closed on the ledger itself and not only in the app.

## 1. IOTA Move Smart Contracts on Mainnet

### 1.1. IOTA Mainnet Architecture

The contracts are published directly to IOTA mainnet and execute in the Move virtual machine on the base layer. Objects on the ledger have owners; a shared object — the registry — can be read and, within the contract's rules, written by anyone, while owned objects — profiles, catalogues, trust objects — change only by their owner's signature. Contracts emit events that let the app and the server find what changed without a central index.

### 1.2. The Anatomy of a Smart Contract

A Move package is a set of modules; a module defines object types and the functions that may create and change them. The ledger enforces the ownership model and the compatibility rules of upgrades; the module's own checks enforce everything else — who may call a function, what a value must look like, which version of the package may write.

## 2. wot.id Uses the Move VM Exclusively

wot.id uses only the Move virtual machine on IOTA's base layer; it does not use IOTA's EVM.

### 2.1. The Move VM (wot.id's Choice)

Move treats digital assets as resources with special properties enforced by the language: they must be explicitly moved and cannot be accidentally copied or deleted. Whole classes of common contract bugs are prevented at the compiler level, and the language is designed to be amenable to formal verification.

### 2.2. Why wot.id Chose Move VM Over EVM

For an identity system the integrity, uniqueness and security of identities and vouches are the critical concerns. Move's ownership model and resource safety fit that requirement; wot.id's core on-chain components are built on it exclusively.

### 2.3. Technical Implementation in wot.id

The contracts implement a registry-and-profile pattern (§2.5). The server builds each call as a programmable transaction block; your browser signs and broadcasts it; the server confirms it by digest ([01](01_Project_Overview_And_Principles.md) §4.2).

### 2.4. Data Architecture: 100% On-Chain VALUES with Trust Scores

A "claim" in the contracts is a detail: one encrypted value with its label, its privacy band, a reserved trust level and the vouches that point at it. There is no separate claims system — every stored value is itself the claim. Values are ciphertext on the ledger; the labels and timestamps around them are readable ([09](09_Data_Storage_And_Asset_Management.md) §3).

### 2.5. wot.id Custom Move Contracts: Identity Registry Pattern

A shared registry object maps the DID to its profile object and keyed hashes of secondary identifiers to the DID. Anyone can look an identity up on the ledger; no database stands in between. Since the summer of 2026 the registry refuses writes from superseded versions of the package, so that every live write goes through the current contract rules.

## 3. Smart Contract Interaction Flow (Step-by-Step)

### 3.1. Architecture Overview

The registry (shared) points at profiles (owned). A profile holds the identity's details and may point at a trust profile (owned). Vouches are their own objects, each pointing at the detail it vouches for. A file vault (owned) holds the catalogue. Recovery settings (owned) hold the guardian set.

### 3.2. Contract Responsibilities

| Contract | Responsibility | Objects |
|---|---|---|
| Identity registry | discovery — name to identity | shared |
| Identity profiles | storage of encrypted details | owned by the identity's key |
| Trust | vouches and their status; trust profiles; proposal building blocks | owned by the voucher / the identity |
| File vault | the file catalogue, share offers, shares, revocations | owned by the identity's key |
| Recovery | guardian settings and recovery requests | owned by the identity's key |

### 3.3. Complete Interaction Flow

Creating an identity is one signed transaction: it creates the profile object, names the DID after that object's id, and writes the registry entries that map the name and your address to it. Saving a detail writes ciphertext to your profile. Giving a vouch creates a vouch object naming the detail and its version, with the voucher taken from the signature. Registering a file writes a catalogue entry to your vault. Each is one transaction you sign and wot.id sponsors.

### 3.4. Lookup Flows

By DID: registry to profile. By a provider's e-mail: keyed hash to DID to profile. By address: the address the key produces to the DID — this is how a passkey or private-key sign-in finds the identity without anything to type.

### 3.5. Critical Design Points

1. The registry is shared — one global object, callable by anyone who can sign a transaction.
2. Profiles, vaults and recovery settings are owned — each user owns their own objects, and only their key changes them.
3. Many doors, one identity — e-mail, passkey and private key all resolve to the same DID.
4. The sender is always the user — wot.id's signature on a transaction pays the fee and authorises nothing else.

## 4. wot_identity_registry Module (Detailed)

The registry holds the mappings of §2.5 and emits an event when an identity is registered, which is how the app and the server find new identities. It carries a version floor: writes are accepted only through the current package version. The registry object's id has not changed since the first generation of the contracts (§6.1).

## 5. wot.id Move Contract Architecture

### 5.1. Core Identity Management (`wot_identity` module)

A profile object per identity, controlled by the owner's address, holding encrypted details and the published public keys the sign-in proof is checked against. Every value write is ciphertext; the entry points that once accepted plaintext have been retired on the ledger.

### 5.2. File Vault (`file_vault` module) - NEW January 2026

A catalogue per identity, in a vault object owned by the identity's address. Per file: the hash of the original, the file key encrypted to the owner, size, category, sealed name and type, a code and a pointer saying where the encrypted bytes are. The module holds no file bytes. Sharing is by consent: the owner creates an offer carrying the file key re-encrypted to the recipient; the recipient accepts or rejects with their own signature; the owner can revoke a share and restore it ([09](09_Data_Storage_And_Asset_Management.md) §4.7).

### 5.3. Trust Management (`wot_trust` module)

Vouches: who vouched, about whom, for which detail at which version, when, with a status — active, superseded, suspended, revoked, or erased by the person the vouch is about. The voucher is taken from the transaction's signature and cannot be forged. Each vouch carries a trust level on the protocol's −100…+100 scale, written at one fixed value today; the module also defines a trust profile per identity with a score on the same scale, which the app does not create today. Proposal and voting building blocks exist in this module; no app surface uses them ([07](07_Trust_Architecture_And_Management.md)).

### 5.4. Privacy Levels (3-Band Scale — v9 May 2026)

Every detail carries one of three privacy bands: readable by the people you admit to your circles; readable by named people you grant access to; or readable by you alone. The value is ciphertext in every band — a band says who holds a key to it, never that the bytes are readable. New details are private by default; your first and family name and your account type are circle-readable by default.

### 5.5. Trust Algorithm Integration

The identity module can reference an identity's trust profile. No computation of trust runs on the ledger today; the on-chain aggregates defined in earlier versions are neither fed nor read.

### 5.6. Gas Station Pattern Implementation — RETIRED (2026-08-31)

In the first year wot.id's gas station signed and paid transactions on users' behalf. That pattern ended on 31 August 2026. Since 15 September 2026 the gas station is back in a new shape: it pays the fee of every contract write from a pool of its own coins and co-signs the transaction as payer, while the user signs as sender ([01](01_Project_Overview_And_Principles.md) §4.2).

## 6. Contract Deployment and Interaction

### 6.1. Deployed Package and Registry IDs

| On-ledger object | Id |
|---|---|
| wot.id contract package — current version, live since 2 October 2026 | `0x7fac024e1da20f3a2e0aee6c57e659c7ffbf77d108cc83212b0eb7a84ec86af5` |
| Identity registry — shared object, unchanged across upgrades | `0x334a70ee16409b749bf221a9d0aafdd8c829db22474e2363a0bdd43e9b45ad92` |
| Offline-messages package — published 25 July 2026 | `0x7e0e6ec613c494f22718bfe774e5ddad19119ddda9edce34fc6940a6093f1bdf` |

Each can be opened on the IOTA mainnet explorer, https://explorer.rebased.iota.org, where the modules are readable as deployed bytecode. The source code is not published.

**Upgrades.** The package is upgradeable under the ledger's *compatible* policy: an upgrade can add functions and change what an existing function does — including making it refuse to run — but cannot change the shape of stored objects or remove a public function. The upgrade capability is held in a wallet kept offline and used in a signed ceremony: the key is imported for the ceremony and removed again afterwards. There have been five such ceremonies — 22 July, 11 August, 22 August, 23 September and 2 October 2026 — each a public transaction on the ledger. Moving this power away from wot.id is an open design question, not a built feature.

### 6.2. Executing Functions (`moveCall` in PTB)

Every call is a programmable transaction block that the server builds and co-signs as fee payer, you sign as sender, your browser submits, and the server confirms by digest.

### 6.3. Integration Patterns

The contracts are callable by anyone who can sign an IOTA transaction — no API key, no allow-list. They check who signed, not who paid: a second client could submit transactions with its users paying the network fee themselves, or make its own arrangement. There is one client today, wot.id's own.

## 7. Developer Workflow

Changes to the contracts are written and unit-tested in Move, built, and tested again before any upgrade; an upgrade is then published in the offline-wallet ceremony of §6.1 and verified on the ledger. Details of the workflow are internal.

## 8. Current Status and Next Steps

| Item | State | Since |
|---|---|---|
| Package on mainnet, upgraded in place | live, fifth upgrade | 2 October 2026 |
| Separate offline-messages package | live | 25 July 2026 |
| Vouches bound to the detail's version; erasure by the subject | live | August 2026 · July 2026 |
| File-share revocation and restoration on the ledger | live | July 2026 · August 2026 |
| Registry version floor | live | August 2026 |
| Proposal and voting building blocks | in the contracts, no app surface | — |
| Trust computation on the ledger | not performed; aggregates unused | — |
