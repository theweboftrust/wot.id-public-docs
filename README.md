# wot.id: Human Identity on the Web of Trust

**The world's first quantum-safe self-sovereign identity platform.**

wot.id is an open peer-to-peer environment where any digitally connected actor—human, machine, service, or otherwise—can communicate, manage and exchange assets, and handle trust with instantaneous speed, maximum security, and minimal cost. Built upon IOTA's advanced distributed ledger technology, any datapoint is permanently available on a directed acyclic graph (the Tangle), but can only be accessed and controlled by its owner. Every datapoint also has an inbuilt trust level ranging from **-100 to +100** that enables participants to establish and manage complex trust relationships.

---

## Quantum-Safe Identity

wot.id is the **first digital identity platform** to combine post-quantum cryptography with a fully decentralized web of trust.

### Hybrid Encryption Architecture

| Component | Description |
|-----------|-------------|
| **X25519** | Classical elliptic curve encryption, battle-tested and proven |
| **ML-KEM-768** | Post-quantum lattice-based encryption (NIST FIPS 203) |
| **BIP-39 Mnemonic** | User-owned 24-word recovery phrase for key derivation |

Attackers must break **both** X25519 AND ML-KEM to decrypt your data. Even quantum computers cannot break both simultaneously.

**First mainnet transaction with PQC encryption: December 23, 2025.**

### Why Quantum-Safe Now?

- **"Harvest Now, Decrypt Later"**: Adversaries are already collecting encrypted data today, waiting for quantum computers to break it tomorrow. Your identity data must be protected NOW.
- **Regulatory Alignment**: NIST finalized post-quantum standards in 2024 (FIPS 203, 204, 205). wot.id implements ML-KEM-768, the recommended key encapsulation mechanism.
- **Defense in Depth**: Hybrid encryption means even if one algorithm is broken, the other still protects your data. No migration needed—your 24-word recovery phrase already protects you.

### Zero-Knowledge Server

All encryption happens **client-side**. wot.id servers **never** see your plaintext data. Only you hold the keys.

```
Your Data → Client-Side Encryption → Encrypted Blob
                     ↓
          Encrypted Blob → IOTA Tangle (permanent storage)
                     ↓
          wot.id Server sees: ████████████████
```

---

## How wot.id Compares

| Capability | Traditional SSI | wot.id |
|------------|-----------------|--------|
| **Encryption** | Classical (breakable by quantum) | Hybrid X25519 + ML-KEM-768 (quantum-safe) |
| **Key Storage** | Platform-controlled or HSM | User-owned via BIP-39 mnemonic |
| **Trust Model** | Centralized issuers | Decentralized peer attestations |
| **Data Storage** | Off-chain or siloed | 100% on-chain (IOTA Tangle) |
| **Server Knowledge** | Sees plaintext data | Zero-knowledge (client-side encryption) |

---

## Take Back Control

For the human actors in the loop, wot.id offers the following advantages:

1. **Clear Human Identification**: Unequivocally and verifiably identify yourself as human within the digital realm.
2. **True Data Ownership**: Own and control all digitalized aspects of your existence without any intermediaries whatsoever.
3. **Value Attribution**: Directly receive any value derived from or created with data connected to your identity.
4. **Selective Disclosure**: Reveal any aspect of your digital existence to anyone, with any desired degree of granularity and anonymity.
5. **Provenance & Attribution**: Establish a reliable, auditable source of truth for your work, content, and contributions.

---

## Decentralized Trust Network

Every piece of data carries a trust score from -100 to +100, verified by peer attestations.

```
-100        -50         0          +50        +100
 │           │          │           │           │
 ▼           ▼          ▼           ▼           ▼
Distrust   Skeptical  Neutral    Trusted    Verified
```

- Trust scores evolve based on interactions and attestations
- Multiple actors' assessments contribute to overall trust
- Context-aware: professional, personal, financial, technical domains

---

## True Data Sovereignty

- **You own your DID**: Your Decentralized Identifier (W3C DID) is cryptographically yours. No company can revoke it.
- **100% On-Chain**: All identity data lives on the IOTA Tangle. To access your data, you need only your DID—not wot.id.
- **Selective Disclosure**: Reveal only what you choose. Share specific attributes without exposing your full identity.

---

## Built On

| Technology | Purpose |
|------------|---------|
| **IOTA** | Feeless DAG-based distributed ledger |
| **Move** | Secure smart contract language |
| **W3C DID** | Standard decentralized identifier format |
| **NIST PQC** | FIPS 203 post-quantum cryptography |

---

## Documentation

For detailed information, please refer to the **comprehensive documentation with visual diagrams** in the [`/docs`](docs/) directory. All major documentation files include mermaid diagrams for better understanding of architecture, processes, and data flows.

### Vision, Principles, and Governance

- **[Project Overview and Principles](docs/01_Project_Overview_And_Principles.md)**: Our mission, 10 core principles, and visual ecosystem diagrams
- **[Governance and Conflict Resolution](docs/10_Governance_And_Conflict_Resolution.md)**: Community-led decision-making with proposal lifecycle diagrams

### System Architecture and Technology

- **[System Architecture](docs/02_System_Architecture.md)**: Four-layer architecture (IOTA L1/L2, Move VM, Rust backend, Next.js frontend)
- **[IOTA Node and Network](docs/03_IOTA_Node_And_Network.md)**: IOTA Tangle integration and L2 smart contract execution
- **[Move Smart Contracts](docs/05_Move_Smart_Contracts.md)**: On-chain identity, credentials, and trust logic
- **[Backend and Identity Service](docs/04_Backend_And_Identity_Service.md)**: Rust/Axum backend with isolated cryptographic operations
- **[Frontend and User Experience](docs/08_Frontend_And_User_Experience.md)**: Next.js/TypeScript UI with IOTA DApp Kit integration

### Core Features

- **[Trust Architecture](docs/07_Trust_Architecture_And_Management.md)**: Dual-model trust system with transitive trust and aggregation
- **[P2P Communication](docs/06_P2P_Communication.md)**: End-to-end encrypted messaging between DIDs
- **[Data Storage and Asset Management](docs/09_Data_Storage_And_Asset_Management.md)**: Decentralized storage with IOTA Kiosk integration

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
