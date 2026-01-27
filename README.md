# wot.id: Human Identity on the Web of Trust

wot.id is an open peer-to-peer environment where any digitally connected actor — human, machine, service, or otherwise — can communicate, manage and exchange assets, and handle trust with instantaneous speed, maximum security, and minimal cost. 

Built upon IOTA's advanced distributed ledger technology (the real cloud), every datapoint is permanently available on a directed acyclic graph, but can only be accessed and controlled by its owner. Every datapoint also has an inbuilt trust level ranging from **-100 to +100** that enables participants to establish and manage complex trust relationships.

## Take Back Control

For the human actors in the loop, wot.id offers the following advantages:

1. **Clear Human Identification**: Unequivocally and verifiably identify yourself as human within the digital realm — a distinction that grows more critical as AI-generated content becomes indistinguishable from human output.
2. **True Data Ownership**: Own and control all digitalized aspects of your existence without any intermediaries whatsoever. No platform can revoke, suspend, or monetize your identity.
3. **Value Attribution**: Directly receive any value derived from or created with data connected to your identity. When your data generates profit, that profit flows to you — not to a platform.
4. **Selective Disclosure**: Reveal any aspect of your digital existence to anyone, with any desired degree of granularity and anonymity. You decide who sees what, and when.
5. **Provenance & Attribution**: Establish a reliable, auditable source of truth for your work, content, and contributions — essential in an era of deepfakes, misinformation, and uncredited AI training data.

These are not aspirational goals. They are operational on the IOTA mainnet today.


**The world's first quantum-safe self-sovereign identity platform.** First mainnet transaction with PQC encryption: December 23, 2025.

| Technology | Purpose |
|------------|---------|
| **IOTA Tangle** | Feeless DAG-based distributed ledger (Protocol 17 mainnet) |
| **Move** | Secure smart contract language eliminating 5 of OWASP Top 10 |
| **W3C DID Core 1.0** | Standard decentralized identifier with Ed25519 + BLAKE3 |
| **NIST PQC** | Hybrid X25519 + ML-KEM-768 post-quantum encryption (FIPS 203) |

---

## Documentation

Reach out to get access to 55,000 lines of code for examination and discussion at https://github.com/theweboftrust/wot.id, but make sure to study these foundational docs first.

### The Human Promise (Start Here)

- **[01 — Project Overview and Principles](docs/01_Project_Overview_And_Principles.md)**: The mission, the 10 core principles that guide every design decision, and why human sovereignty is the foundation — not an afterthought. This is the document to read first.

### Architecture and Infrastructure

- **[02 — System Architecture](docs/02_System_Architecture.md)**: The three-layer stack — Next.js frontend, Rust/Axum backend, IOTA mainnet — and how they interact. Hybrid CLI + SDK approach for blockchain transactions.
- **[03 — IOTA Node and Network](docs/03_IOTA_Node_And_Network.md)**: IOTA Protocol 17 mainnet integration, CLI-based transaction submission, and the build-time optimization that reduced compilation from 70 minutes to 5.
- **[04 — Backend and Identity Service](docs/04_Backend_And_Identity_Service.md)**: The Rust backend that orchestrates business logic, the isolated Identity Service for DID generation, gas station sponsorship, and OAuth auto-provisioning.
- **[05 — Move Smart Contracts](docs/05_Move_Smart_Contracts.md)**: Three core on-chain modules (identity registry, profile storage, trust attestations) plus the FileVault for encrypted document sharing. All deployed on mainnet.

### Core Features

- **[06 — P2P Communication](docs/06_P2P_Communication.md)**: WebSocket-based peer-to-peer messaging between DIDs, with a roadmap to Signal Protocol and post-quantum end-to-end encryption.
- **[07 — Trust Architecture](docs/07_Trust_Architecture_And_Management.md)**: The universal -100 to +100 trust scale, dual trust model (entity-level and claim-level), QR code attestation flows, and on-chain reputation. First production attestation: November 19, 2025.
- **[08 — Frontend and User Experience](docs/08_Frontend_And_User_Experience.md)**: The Next.js interface at [wot.id](https://wot.id) — profile management, asset transfers, trust attestations, P2P messaging, health data with CSV import, and client-side PQC encryption throughout.
- **[09 — Data Storage and Asset Management](docs/09_Data_Storage_And_Asset_Management.md)**: 100% on-chain data values with 16+ atomic data types, per-value trust scores, and the IOTA Kiosk pattern for digital asset trading.

### Governance and Security

- **[10 — Governance and Conflict Resolution](docs/10_Governance_And_Conflict_Resolution.md)**: On-chain proposal voting with reputation-weighted influence (0.5x to 2.0x multiplier), arbiter-based conflict resolution, and a security threat model covering smart contract, P2P, and quantum attack vectors.

---

## Project Status (December 2025)

wot.id is actively under development with a fully operational backend and frontend deployed on IOTA mainnet.

### Working Components

- **IOTA Mainnet Integration**: Backend connected to IOTA mainnet with direct API and CLI integration
- **Deployed Move Contracts**: Identity Registry and Profile contracts on mainnet
- **Post-Quantum Encryption**: Hybrid X25519 + ML-KEM-768 encryption operational
- **Gas Station Pattern**: Backend-sponsored transactions for zero-friction onboarding
- **Modern Rust Backend**: Axum-based REST API with JWT authentication
- **Next.js Frontend**: TypeScript frontend with complete user flows
- **Production Deployment**: Live on [wot.id](https://wot.id)

### Recent Milestones

- **December 2025**: First PQC-encrypted transaction on IOTA mainnet
- **November 2025**: QR code attestations and cross-device trust flows
- **November 2025**: OAuth auto-provisioning (Google, GitHub, Apple)

---

## Open Source

wot.id is fully open source. Audit the code, contribute, or run your own instance.

- **Repository**: [github.com/theweboftrust/wot.id](https://github.com/theweboftrust/wot.id)
- **Issues**: [Report bugs or request features](https://github.com/theweboftrust/wot.id/issues)
- **Support**: [support@wot.id](mailto:support@wot.id)
