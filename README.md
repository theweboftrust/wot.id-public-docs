# wot.id: Human Identity on the Web of Trust

wot.id is an open peer-to-peer environment where any digitally connected actor — human, machine, service, or otherwise — can communicate, manage and exchange assets, and handle trust with instantaneous speed, maximum security, and minimal cost. 

Built upon IOTA's advanced distributed ledger technology, every datapoint is permanently stored on the blockchain (the real cloud), but can only be accessed and controlled by its owner. Every datapoint also has an inbuilt trust level ranging from **-100 to +100** that enables participants to establish and manage complex trust relationships.

## Take Back Control

For the human actors in the loop, wot.id offers the following advantages:

1. **Clear Human Identification**: Unequivocally and verifiably identify yourself as human within the digital realm — a distinction that grows more critical as AI-generated content becomes indistinguishable from human output.
2. **True Data Ownership**: Own and control all digitalized aspects of your existence without any intermediaries whatsoever. No platform can revoke, suspend, or monetize your identity.
3. **Value Attribution**: Directly receive any value derived from or created with data connected to your identity. When your data generates profit, that profit flows to you — not to a platform.
4. **Selective Disclosure**: Reveal any aspect of your digital existence to anyone, with any desired degree of granularity and anonymity. You decide who sees what, and when.
5. **Provenance & Attribution**: Establish a reliable, auditable source of truth for your work, content, and contributions — essential in an era of deepfakes, misinformation, and uncredited AI training data.

These are not aspirational goals, the MVP is operational (with all its limitations) at

https://www.wot.id


## Documentation

Reach out to get access to 55,000 lines of code for examination and discussion at https://github.com/theweboftrust/wot.id, but make sure to study these foundational docs first.

### The Human Promise

- **[01 — Project Overview and Principles](docs/01_Project_Overview_And_Principles.md)**: The mission, the 10 core principles that guide every design decision, and why human sovereignty is the foundation — not an afterthought. This is the document to read first.

### Architecture and Infrastructure

- **[02 — System Architecture](docs/02_System_Architecture.md)**: The three-layer stack — Next.js frontend, Rust/Axum backend, IOTA mainnet — and how they interact. Hybrid CLI + SDK approach for distributed-ledger transactions.
- **[03 — IOTA Node and Network](docs/03_IOTA_Node_And_Network.md)**: IOTA Protocol 24 mainnet integration (Starfish consensus, May 2026), CLI-based transaction submission, and the build-time optimization that reduced compilation from 70 minutes to under 10.
- **[04 — Backend](docs/04_Backend.md)**: The Rust/Axum backend that orchestrates all business logic — DID generation (Ed25519 + BLAKE3) inlined since the standalone Identity Service was retired in March 2026, gas station sponsorship, OAuth auto-provisioning, and the cache layer.
- **[05 — Move Smart Contracts](docs/05_Move_Smart_Contracts.md)**: Five on-chain modules (identity registry, profile storage, trust attestations, FileVault, mailbox) plus the unified EncryptedAtom architecture. All deployed on mainnet (Package v8).

### Core Features

- **[06 — P2P Communication](docs/06_P2P_Communication.md)**: WebSocket-based peer-to-peer messaging between DIDs, with a roadmap to Signal Protocol and post-quantum end-to-end encryption.
- **[07 — Trust Architecture](docs/07_Trust_Architecture_And_Management.md)**: The universal -100 to +100 trust scale, dual trust model (entity-level and claim-level), QR code attestation flows, and on-chain reputation. First production attestation: November 19, 2025.
- **[08 — Frontend and User Experience](docs/08_Frontend_And_User_Experience.md)**: The Next.js interface at [wot.id](https://wot.id) — profile management, asset transfers, trust attestations, P2P messaging, health data with CSV import, and client-side PQC encryption throughout.
- **[09 — Data Storage and Asset Management](docs/09_Data_Storage_And_Asset_Management.md)**: 100% on-chain data values with 16+ atomic data types, per-value trust scores, and the IOTA Kiosk pattern for digital asset trading.

### Governance and Security

- **[10 — Governance and Conflict Resolution](docs/10_Governance_And_Conflict_Resolution.md)**: On-chain proposal voting with reputation-weighted influence (0.5x to 2.0x multiplier), arbiter-based conflict resolution, and a security threat model covering smart contract, P2P, and quantum attack vectors.

---

## Project Status (May 2026)

wot.id is actively under development with a fully operational backend and frontend deployed on IOTA mainnet (Protocol 24, Starfish consensus). The core identity → attestation → trust score loop has been verified end-to-end on mainnet, including PTB writes (claim updates, attestations, claim deletes) under the v1.21.1 SDK.

### Working Components

- **IOTA Mainnet Integration**: Backend connected to IOTA mainnet (Protocol 24, SDK v1.21.1) with direct JSON-RPC reads and CLI-based writes
- **Deployed Move Contracts**: Five on-chain modules on mainnet — Identity Registry, Profile, Trust, FileVault, and Mailbox (Package v8, `0x14b1e852...`)
- **Unified EncryptedAtom Architecture**: Single universal `store_atom()` entry point replacing 15 separate atom structs — all user data PQC-encrypted by design
- **Post-Quantum Encryption**: Hybrid X25519 + ML-KEM-768 encryption operational for all on-chain data (identity, health, documents, files)
- **Gas Station Pattern**: Backend-sponsored transactions for zero-friction onboarding (~0.008 IOTA per profile creation)
- **Modern Rust Backend**: Axum-based REST API with JWT authentication, DID generation inlined (Identity Service retired)
- **Next.js Frontend**: TypeScript frontend with complete user flows — profile, attestation, messaging, health data
- **Per-Field Trust Scores**: Real attestation-derived trust scores queried from on-chain events, with per-claim granular attestations
- **Local Encrypted File Storage**: Client-side ChaCha20-Poly1305 encryption with IndexedDB, 9 file categories, hybrid PQC key wrapping
- **Comprehensive Security Hardening**: JWT signature verification, nonce replay prevention, 4-phase rate limiting, DID-based throttling, email normalization
- **Interactive Demo**: `./scripts/demo.sh` — walks through the full system with live mainnet data
- **Production Deployment**: Live on [wot.id](https://wot.id)

### Recent Milestones

- **May 2026**: SDK upgraded v1.17.2 → v1.21.1 (Protocol 20 → 24, Starfish consensus) — closed a 4-version mainnet gap; PTB read + write paths verified end-to-end with explorer-confirmed transactions
- **May 2026**: Auth-flow repair — frontend now mints backend JWTs via `/api/auth/exchange` (the dead `/auth/token` chain to the retired Identity Service was finally removed); encryption-state-machine hardened to prevent silent key regeneration when on-chain ciphertext exists (Safari ITP scenario)
- **May 2026**: Evidence-tier discipline added to consistency tests + primer — `[RUNTIME]` checks of user-facing flows must include a user-visible artifact, not just backend log lines
- **March 2026**: Smart Contract v8 deployed — unified `EncryptedAtom` architecture with `store_atom()`, `delete_atom()`, and `has_atom_access()`
- **March 2026**: SDK upgraded v1.13.1 → v1.17.2 (Protocol 17 → 20) — error parsing updated, Dockerfile upgraded to rust:1.88
- **March 2026**: Identity Service retired — DID generation (Ed25519 + BLAKE3) inlined into backend, eliminating a separate microservice
- **March 2026**: Dead endpoint audit — 852 lines of mock/debug/test code removed, including critical security fix (unauthenticated key export endpoint)
- **March 2026**: Trust score pipeline fix — granular attestation type mapping, per-claim trust score calculation from real on-chain events
- **March 2026**: Privacy level architecture — unified 0-3 scale (Public, Contacts, Selective, Private) across all layers
- **March 2026**: Governance voting research — analysis of 7 voting algorithms (Minimax, Quadratic, RSV, Approval, Conviction, Schulze, Ranked Pairs) with trust-weighted voting design
- **March 2026**: End-to-end dry run on mainnet — QR → scan → attestation → on-chain verification confirmed working
- **February 2026**: Decentralization strategy v2.0 — 7-phase roadmap from current architecture to fully serverless (custom PTB construction convergence)
- **February 2026**: Custom binary protocol analysis (Option C) — feasibility study for eliminating CLI/SDK dependencies with ~2,000 lines of custom BCS transaction construction
- **January 2026**: FileVault Move contract deployed on mainnet (Package v7) — encrypted file metadata and DEK on-chain
- **January 2026**: Full security hardening — JWT attestation verification, nonce replay prevention, 4-phase rate limiting, DID-based throttling
- **January 2026**: Local encrypted file storage with PQC key wrapping (ChaCha20-Poly1305 + X25519 + ML-KEM-768)
- **January 2026**: Account linking for multi-provider Sybil prevention
- **January 2026**: W3C DID Core 1.0 compliance verified for production
- **December 2025**: First PQC-encrypted transaction on IOTA mainnet
- **November 2025**: QR code attestations and cross-device trust flows
- **November 2025**: OAuth auto-provisioning (Google, GitHub, Apple)

### Roadmap

- **Q1 2026**: ✅ Demo-ready prototype with end-to-end trust flow
- **Q2 2026**: Test-user-ready platform *(current)* — account linking ✅, onboarding flow, Approval Voting, CLI elimination (Option C, blocked on `iota-rust-sdk` stable release), FileVault frontend; SDK / Protocol kept current with mainnet ✅
- **Q3 2026**: Decentralization Phase 2-3, Condorcet voting, client-side transactions
- **Q4 2026**: Scale to 100+ users, governance in production, advanced anti-gaming defenses

---

## Source Code

the substantial codebase is currently not (yet) open source.

- **Repository**: [github.com/theweboftrust/wot.id](https://github.com/theweboftrust/wot.id)
- **Issues**: [Report bugs or request features](https://github.com/theweboftrust/wot.id/issues)
- **Support**: [support@wot.id](mailto:admin@wot.id)
