# 03: wot.id - IOTA Node and Network Setup

## 1. Introduction

**IOTA Protocol 26 Mainnet**

This document provides comprehensive guidance for IOTA mainnet connectivity for `wot.id` development and production. The current architecture is **CLI-only** (the Rust `iota-sdk` Cargo dep that previously supplied a few type aliases was removed on 2026-05-26, Open-Issues #12 closed — see `docs/2026_Code_Work/26-05-26_Backend_Deploy.md`); the backend shells out to the `iota` CLI v1.23.2 and parses its `--json` output into local structs.

**Current Architecture (May 2026)**:
- **IOTA Mainnet**: Protocol 26 via public endpoint `https://api.mainnet.iota.cafe`
- **CLI-Based Transactions**: IOTA CLI v1.23.2 for PTB construction and submission
- **No Rust SDK Cargo Dep**: `iota-sdk` removed 2026-05-26; handlers wrap CLI output in their own structs
- **Move Framework**: IOTA framework v1.23.2 (bumped from v1.21.1 in v11; contracts backward-compatible with Protocol 26)
- **Simplified Stack**: Direct mainnet access, no L2 Wasp complexity
- **Production Ready**: Operational with OAuth auto-provisioning, QR code attestations, on-chain attestation submission, two-tx default-privacy orchestration (TrustProfile lazy provisioning since 2026-05-26 — Open-Issues #8 closed)

**Local Node (Optional)**:
- Development can use local IOTA node for testing
- Production uses public mainnet endpoint
- This guide covers both approaches

**Architecture Context**:
For understanding how IOTA integration fits within the wot.id architecture:
- **Standards Foundation**: See `docs/01_Project_Overview_And_Principles.md` sections 1.2-1.4
- **Storage Model**: wot.id stores encrypted **VALUES** (identity claims, health atoms, trust scores) on the IOTA mainnet object ledger — not source documents. The user's PDFs / scans / CSVs / photos stay on their own device or cloud. See `docs/01_Project_Overview_And_Principles.md` Principle #4 + `docs/Claude_Primer.md` §17.
- **System Architecture**: See `docs/02_System_Architecture.md` sections 3.1-3.3
- **Backend Integration**: See `docs/04_Backend.md` section 1.1
- **W3C DID Implementation**: wot.id uses W3C DID Core 1.0 compliant DIDs (Ed25519 + BLAKE3). See `docs/2026_Code_Work/26-01-01_W3C_Compliance.md`

---

## 2. IOTA Mainnet Node Setup (Optional - Local Development)

**Note:** Production wot.id uses the public mainnet endpoint `https://api.mainnet.iota.cafe`. Local node setup is optional for development testing.

The IOTA node can run as a Docker container. Current mainnet is **Protocol 26**.

## 2. IOTA Mainnet Node Setup

The IOTA node runs as a Docker container using the official `iotaledger/iota-node:mainnet` image with Protocol 26 support.

### 2.1. Setup and Configuration

**Setup Directory**: `iota-fullnode-docker-setup/` (in project root)

**Docker Compose Configuration** (`docker-compose.yaml`):

```yaml
services:
  fullnode:
    image: iotaledger/iota-node:mainnet  # Latest mainnet version
    ports:
      - "8084:8084/udp" # P2P port
      - "9000:9000/tcp" # JSON-RPC port
      - "9184:9184/tcp" # Metrics port
    volumes:
      - ./data:/opt/iota/:rw
    command: [
      "/usr/local/bin/iota-node",
      "--config-path",
      "/opt/iota/config/fullnode.yaml",
    ]
```

**Execution Steps:**
1.  **Navigate to Setup Directory**: `cd iota-fullnode-docker-setup/`
2.  **Start Node**: `docker compose up -d`
3.  **Verify Version**: `docker exec <container> /usr/local/bin/iota-node --version`

### 2.2. Accessing IOTA Node Services

Once running, the IOTA node exposes the following services on `localhost`:

| Service            | URL (`localhost`)         | Description                               |
| ------------------ | ------------------------- | ----------------------------------------- |
| **JSON-RPC API**   | `http://localhost:9000`   | Primary endpoint for IOTA CLI and backend integration |
| **Metrics**        | `http://localhost:9184`   | Prometheus metrics for monitoring         |
| **P2P Port**       | `udp://<your_ip>:8084`    | Used for peering with other IOTA nodes   |

**Protocol Version**: 26 (current mainnet, Starfish consensus, since 2026-05-21)
**Network**: IOTA Mainnet
**CLI Integration**: Backend uses `iota` CLI v1.23.2 for all transaction submission. No Rust `iota-sdk` Cargo dep (vestigial type-aliases dep removed 2026-05-26, Open-Issues #12 closed).
**Framework Version**: Move contracts v1.23.2 (bumped from v1.21.1 in v11; backward-compatible with Protocol 26)

---

## 3. IOTA CLI Integration

**Current Approach**: The wot.id system uses the IOTA CLI for all Move contract interactions, eliminating the need for complex L2 Wasp node setup while maintaining full mainnet compatibility.

### 3.1. IOTA CLI Installation

The IOTA CLI is installed via Cargo and provides direct access to IOTA mainnet functionality.

**Installation**:

```bash
# Install via Cargo
cargo install --git https://github.com/iotaledger/iota.git iota

# Verify installation
iota --version
```

**Installation**: IOTA CLI is installed via Cargo to `~/.cargo/bin/iota`

### 3.2. CLI Configuration

**Environment Variables**:

```bash
export IOTA_CLI_PATH="$HOME/.cargo/bin/iota"
export IOTA_RPC_URL="http://localhost:9000"   # Local node (preferred name; new on 2026-05-26)
export IOTA_NODE_URL="http://localhost:9000"  # Legacy alias — still honoured for back-compat
# Production: export IOTA_RPC_URL="https://api.mainnet.iota.cafe"
```

> **Note (2026-05-26):** `backend/src/config.rs` reads `IOTA_RPC_URL` first, then falls back to `IOTA_NODE_URL`, then to the hard-coded mainnet default — existing deploys keep working unchanged. The 2026-05-26 deploy also changed `deploy.sh`'s default `IOTA_NODE_URL` from the long-dead `api.testnet.shimmer.network` to `api.mainnet.iota.cafe` (Open-Issues #14 closed). See `docs/2026_Code_Work/26-05-26_Backend_Deploy.md`.

**Keystore Setup**:

```bash
# Import private key to keystore
iota keytool import $IOTA_PRIVATE_KEY ed25519

# Verify keystore
iota client active-address
```

### 3.3. Move Contract Interaction

**Programmable Transaction Blocks (PTB)**:

```bash
# Example: Register email → DID mapping
iota client ptb \
  --move-call PACKAGE::wot_identity_registry::register_identifier \
    @0x334a70ee16409b749bf221a9d0aafdd8c829db22474e2363a0bdd43e9b45ad92 \
    "did:iota:mainnet:abc123" \
    "email" \
    "user@example.com" \
  --gas-budget 10000000 \
  --json
```

**Current Package IDs (Protocol 26 Mainnet, v11 deployment May 23, 2026 — Move-layer cleanup release (V11-1…V11-14))**:
- **Identity Registry Package**: `0x40e24bdddd34bdac9ebcfe2d60da0585dbd3b2fa261b716264b5a43597bfe299` (v11; supersedes v10 `0xdfea0e92…`)
- **Registry Shared Object**: `0x334a70ee16409b749bf221a9d0aafdd8c829db22474e2363a0bdd43e9b45ad92` (unchanged across upgrades)

**Contract Name**: `wot_identity_registry` (not `identity_registry`)

---

## 4. System Architecture Diagram

This diagram illustrates the CLI-only IOTA integration (the Rust `iota-sdk` Cargo dep was removed on 2026-05-26 — Open-Issues #12 closed).

```mermaid
graph TD
    subgraph "Production Architecture"
        Frontend[Frontend<br/>Next.js on Vercel]

        subgraph "Backend Services"
            Backend[Backend API<br/>Port 10000<br/>Integrated DID Generation]
        end

        subgraph "IOTA Integration"
            IOTA_CLI[IOTA CLI<br/>PTB Construction]
            Mainnet[IOTA Mainnet<br/>Protocol 26]
        end
    end

    subgraph "Optional Local Development"
        IOTA_Local[Local IOTA Node<br/>Docker Container]
    end

    Frontend --> Backend
    Backend -- "CLI Commands" --> IOTA_CLI
    IOTA_CLI -- "JSON-RPC" --> Mainnet
    IOTA_CLI -. "Optional Dev" .-> IOTA_Local

    style Mainnet fill:#cde4ff
    style Backend fill:#ffe4cd
    style IOTA_CLI fill:#fcf5c7
    style IOTA_Local fill:#e8e8e8
```

---

## 5. Key Operational Concepts

### 5.1. Data Persistence

All IOTA blockchain data is stored in the `data/` subdirectory within the `iota-fullnode-docker-setup/` folder. This folder is mounted directly into the container, ensuring the ledger state persists across container restarts.

### 5.2. Monitoring Node Sync Status

The IOTA node can take time to fully sync with mainnet. Monitor progress by querying the JSON-RPC API.

**Command to Check Progress:**

```bash
curl -s -X POST http://localhost:9000 \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc": "2.0", "method": "iota_getLatestCheckpointSequenceNumber", "id": 1}'
```

**Expected Response Format:**

```json
{"jsonrpc":"2.0","id":1,"result":"13597323"}
```

Compare the `result` to the latest checkpoint on the [IOTA Rebased Explorer](https://explorer.rebased.iota.org/). (Note: `explorer.iota.org` redirects to the legacy Stardust archive — do not use for the post-Rebased mainnet.)

### 5.3. CLI-Only Transaction Execution

The wot.id backend submits all mainnet transactions through the `iota` CLI. The Rust `iota-sdk` Cargo dep was removed on 2026-05-26 (Open-Issues #12 closed — see `docs/2026_Code_Work/26-05-26_Backend_Deploy.md`).

**Architecture**:
- **CLI for Transactions**: IOTA CLI v1.23.2 constructs and submits PTBs to mainnet
- **No Rust SDK Types**: Handlers parse the CLI's `--json` output into local structs (e.g. `extract_trust_profile_id_from_object_changes` in `trust_profile.rs`)
- **No SDK Transaction Builder**: All PTBs assembled as CLI arg strings

**Benefits**:
- ✅ Direct Mainnet Access: No intermediate layers
- ✅ CLI Stability: Proven reliable interface tracking Protocol 26 exactly
- ✅ No version-skew risk against a moving Protocol
- ✅ Full PTB Support: Complete Programmable Transaction Block functionality
- ✅ Gas Efficiency: Optimized transaction costs

**Build Performance**: ~5 minutes for app-code-only deploys (Docker caches the compiled dependency layer); ~10–15 minutes when `Cargo.toml`/`Cargo.lock` change. The prior 30–40-minute Cargo.lock window applied before 2026-05-26 when the `iota-sdk` dep dragged the upstream IOTA workspace through Cargo. See `Claude_Primer.md` §11.

### 5.4. IOTA CLI Wallet Management

The `iota` CLI manages wallets and keypairs for transaction signing.

**Current Wallet Configuration**:
- **Active Address**: `0x45745c3d1ef637cb8c920e2bbc8b05ae2f8dbeb28fd6fb601aea92a31f35408f`
- **Balance**: Sufficient IOTA for gas fees
- **Private Key**: Stored in keystore (environment variable `IOTA_PRIVATE_KEY`)

**Wallet Commands**:

```bash
# Check active address
iota client active-address

# Check balance
iota client balance

# Import new key
iota keytool import <private_key> ed25519
```

### 5.5. Viewing Logs

**IOTA Node Logs**:

```bash
# From setup directory
cd iota-fullnode-docker-setup/
docker compose logs -f

# Or by container name
docker logs iota-fullnode-docker-setup-fullnode-1 -f
```

**Backend Service Logs**:
- **Backend API**: Console output from `cargo run` in `/backend/`

### 5.6. Resetting the Node

**To Reset IOTA Node**:

```bash
cd iota-fullnode-docker-setup/

# Stop container
docker compose down

# Remove blockchain data (optional - forces full resync)
rm -rf ./data

# Restart node
docker compose up -d
```

**Note**: Removing `./data` forces a complete resync from genesis, which can take several hours.

---

## 6. Current Deployment Status

### 6.1. ✅ Production Environment (March 2026)

**Current Status**: IOTA mainnet Protocol 26 **fully operational** via public endpoint.

**IOTA Mainnet**:
- ✅ **Protocol**: Version 26 (current mainnet, Starfish consensus, since 2026-05-21; the earlier Protocol 20 → 24 production verification on 2026-05-05T11:07Z is in `docs/2026_Code_Work/26-05-05_SDK_Upgrade_Verified.md`)
- ✅ **Network**: IOTA mainnet via `https://api.mainnet.iota.cafe`
- ✅ **Production URLs**:
  - Frontend: https://wot.id (Vercel)
  - Backend: https://wot-id-backend.onrender.com

**CLI-Only Integration**:
- ✅ **IOTA CLI v1.23.2**: Used for PTB construction and submission
- ✅ **No Rust SDK Cargo dep**: `iota-sdk` removed 2026-05-26 (Open-Issues #12 closed)
- ✅ **PTB Support**: Full Programmable Transaction Block functionality
- ✅ **Move Contracts**: Deployed to mainnet at package v11 `0x40e24bdddd…` (May 23, 2026)

**Backend Integration Status**:
- ✅ **Backend API**: CLI-only approach (no Rust SDK Cargo dep since 2026-05-26)
- ✅ **Identity Registry**: `wot_identity_registry` deployed and operational
- ✅ **Gas Station**: Backend sponsors transactions with 24h rate limiting
- ✅ **OAuth Auto-Provisioning**: Google, GitHub operational; Apple 95% complete
- ✅ **W3C DID Core 1.0 Compliant**: Ed25519 + BLAKE3 cryptographic derivation
- ✅ **QR Code Attestations**: Generation and scanning operational
- ✅ **On-Chain Attestations**: wot_trust.move operational
- ✅ **Post-Quantum Encryption**: X25519 + ML-KEM-768

### 6.2. Network Performance

**Sync Performance**:
- Initial sync: ~2-4 hours (depending on network conditions)
- Startup time: ~30 seconds for full node readiness
- Current uptime: 100% operational on Protocol 26

**Resource Usage**:
- IOTA Node container: ~2GB RAM, ~50GB storage
- CLI operations: Minimal overhead, sub-second execution
- Backend services: ~500MB RAM combined

**Transaction Performance**:
- PTB construction: <100ms
- Transaction submission: 1-3 seconds
- Confirmation time: 2-5 seconds (mainnet finality)
