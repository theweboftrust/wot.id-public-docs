# wot.id: Human Identity on the Web of Trust

wot.id is an open peer-to-peer environment where any digitally connected actor — human, machine, service, or otherwise — can communicate, manage and exchange assets, and handle trust with instantaneous speed, maximum security, and minimal cost. 

Built upon IOTA's advanced distributed ledger technology, every datapoint is permanently available on a directed acyclic graph (the real cloud), but can only be accessed and controlled by its owner. Every datapoint also has an inbuilt trust level ranging from **-100 to +100** that enables participants to establish and manage complex trust relationships.

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

- **[01 — Project Overview and Principles](01_Project_Overview_And_Principles.md)**: The mission, the 10 core principles that guide every design decision, and why human sovereignty is the foundation — not an afterthought. This is the document to read first.

### Architecture and Infrastructure

- **[02 — System Architecture](02_System_Architecture.md)**: The three-layer stack — Next.js frontend, Rust/Axum backend, IOTA mainnet — and how they interact. Hybrid CLI + SDK approach for blockchain transactions.
- **[03 — IOTA Node and Network](03_IOTA_Node_And_Network.md)**: IOTA Protocol 17 mainnet integration, CLI-based transaction submission, and the build-time optimization that reduced compilation from 70 minutes to 5.
- **[04 — Backend and Identity Service](04_Backend_And_Identity_Service.md)**: The Rust backend that orchestrates business logic, the isolated Identity Service for DID generation, gas station sponsorship, and OAuth auto-provisioning.
- **[05 — Move Smart Contracts](05_Move_Smart_Contracts.md)**: Three core on-chain modules (identity registry, profile storage, trust attestations) plus the FileVault for encrypted document sharing. All deployed on mainnet.

### Core Features

- **[06 — P2P Communication](06_P2P_Communication.md)**: WebSocket-based peer-to-peer messaging between DIDs, with a roadmap to Signal Protocol and post-quantum end-to-end encryption.
- **[07 — Trust Architecture](07_Trust_Architecture_And_Management.md)**: The universal -100 to +100 trust scale, dual trust model (entity-level and claim-level), QR code attestation flows, and on-chain reputation. First production attestation: November 19, 2025.
- **[08 — Frontend and User Experience](08_Frontend_And_User_Experience.md)**: The Next.js interface at [wot.id](https://wot.id) — profile management, asset transfers, trust attestations, P2P messaging, health data with CSV import, and client-side PQC encryption throughout.
- **[09 — Data Storage and Asset Management](09_Data_Storage_And_Asset_Management.md)**: 100% on-chain data values with 16+ atomic data types, per-value trust scores, and the IOTA Kiosk pattern for digital asset trading.

### Governance and Security

- **[10 — Governance and Conflict Resolution](10_Governance_And_Conflict_Resolution.md)**: On-chain proposal voting with reputation-weighted influence (0.5x to 2.0x multiplier), arbiter-based conflict resolution, and a security threat model covering smart contract, P2P, and quantum attack vectors.

---

## Project Status (January 2026)

wot.id is actively under development with a fully operational backend and frontend deployed on IOTA mainnet.

### Working Components

- **IOTA Mainnet Integration**: Backend connected to IOTA mainnet with direct API and CLI integration
- **Deployed Move Contracts**: Identity Registry, Profile, Trust, and FileVault contracts on mainnet (Package v7)
- **Post-Quantum Encryption**: Hybrid X25519 + ML-KEM-768 encryption operational for identity, health, and file data
- **Gas Station Pattern**: Backend-sponsored transactions for zero-friction onboarding
- **Modern Rust Backend**: Axum-based REST API with JWT authentication
- **Next.js Frontend**: TypeScript frontend with complete user flows
- **Local Encrypted File Storage**: Client-side ChaCha20-Poly1305 encryption with IndexedDB, 9 file categories, hybrid PQC key wrapping
- **Comprehensive Security Hardening**: JWT signature verification, nonce replay prevention, 4-phase rate limiting, DID-based throttling, Sybil defenses
- **Production Deployment**: Live on [wot.id](https://wot.id)

### Recent Milestones

- **January 2026**: FileVault Move contract deployed on mainnet (Package v7) — encrypted file metadata and DEK on-chain
- **January 2026**: Full security hardening — JWT attestation verification, nonce replay prevention, 4-phase rate limiting, DID-based throttling
- **January 2026**: Local encrypted file storage with PQC key wrapping (ChaCha20-Poly1305 + X25519 + ML-KEM-768)
- **January 2026**: Account linking for multi-provider Sybil prevention
- **January 2026**: W3C DID Core 1.0 compliance verified for production
- **December 2025**: First PQC-encrypted transaction on IOTA mainnet
- **November 2025**: QR code attestations and cross-device trust flows
- **November 2025**: OAuth auto-provisioning (Google, GitHub, Apple)

---

## Source Code

the substantial codebase is currently not (yet) open source.

- **Repository**: [github.com/theweboftrust/wot.id](https://github.com/theweboftrust/wot.id)
- **Issues**: [Report bugs or request features](https://github.com/theweboftrust/wot.id/issues)
- **Support**: [support@wot.id](mailto:support@wot.id)
