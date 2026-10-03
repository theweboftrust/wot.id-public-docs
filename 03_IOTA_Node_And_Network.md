# 03: wot.id - IOTA Node and Network Setup

*As of 2026-10-03. The public version of the foundational document of the same name: how wot.id reaches the IOTA network, what it runs and what it deliberately does not run, how a transaction is executed, how the gas wallet works, and the state of the deployment. General mechanism, no implementation detail.*

---

## 1. Introduction

wot.id runs on the IOTA mainnet, the public layer-1 ledger of the IOTA Rebased network. It runs no node of its own: both the app in your browser and wot.id's server talk to the network through a public node endpoint. The server uses IOTA's own command-line tooling to build transactions; the browser signs them and sends them to the network itself; the server confirms the result by reading the ledger. The contracts are written in Move and run on the base layer — there is no second layer or bridge in between.

## 2. Local node setup — RETIRED

An earlier effort to run a full IOTA node for wot.id was abandoned for focus. Nothing in the running system depends on a wot.id-operated node; the section is kept so that the numbering matches the internal document.

## 3. IOTA CLI Integration

### 3.1. IOTA CLI Installation

wot.id's server bundles a pinned release of IOTA's command-line tool and verifies its checksum when the server is built. The pin follows the network's protocol upgrades: when the network moves to a new protocol version, the tool is updated to one that supports it. An earlier lag of two releases once stopped the server from building transactions for a day; the pin has been kept current since.

### 3.2. CLI Configuration

The tool is configured with one key: the key of wot.id's gas wallet, which pays the network fee of every sponsored transaction (§5.4). It holds no key of any user, and no other key of wot.id's — the key that can upgrade the contracts is kept offline ([05](05_Move_Smart_Contracts.md) §6).

### 3.3. Move Contract Interaction

The server uses the tool to build each transaction as a programmable transaction block that calls the contracts: create an identity, save a detail, record a vouch, register a file, leave an offline message, set up guardians. It names you as the sender and its gas wallet as the payer, signs for the payment, and returns the unsigned transaction to your browser. Your signature as sender is what the contracts check.

## 4. System Architecture Diagram

```
  Your browser ──(signed transaction)──────────────▶ IOTA mainnet node ──▶ validators ──▶ ledger
       │  ▲                                                  ▲
       │  │ unsigned transaction,                            │ reads: objects, events,
       │  │ co-signed for the fee                            │ transaction results
       ▼  │                                                  │
  wot.id's server ───────────────────────────────────────────┘
       · builds the transaction with IOTA's command-line tool
       · signs as the payer of the fee from its gas wallet
       · confirms each transaction by its digest
```

Everything that matters to you — your identity, your details, your vouches, your files' catalogue, your offline messages, your recovery settings — is on the ledger, readable by anyone with the public endpoint or the explorer, changeable only by the key that owns it.

## 5. Key Operational Concepts

### 5.1. Data Persistence — RETIRED

There is no node data directory to persist: the ledger holds the state, and wot.id's server holds no ledger data.

### 5.2. Checking Mainnet Checkpoint Height

Anyone can check the network's current height and read any object or transaction through the public endpoint or the IOTA mainnet explorer, https://explorer.rebased.iota.org. wot.id's server does the same to confirm your transactions; nothing it reads is privileged.

### 5.3. Transaction Execution — Mixed JSON-RPC + CLI

Four steps, the same for every change you make in the app ([01](01_Project_Overview_And_Principles.md) §4.2): the server builds the transaction with you as the sender and signs as payer; your device signs as sender; your browser broadcasts it to a node; the server reads the result from the ledger by the transaction's digest. The server executes a few transactions itself — the housekeeping of its gas pool, and a small set of older server-signed write paths that wot.id records internally as a gap to close.

### 5.4. IOTA CLI Wallet Management

The gas wallet is a pool of coins owned by wot.id. When the server prepares a transaction for you, it names one coin from the pool to pay the fee; a prepared transaction is valid only with that coin, and an abandoned one expires within minutes so that the coin is free again. The server rotates the coins and tops the pool up from wot.id's treasury; the pool's balance and every fee it pays are visible on the ledger like any other wallet's. Because wot.id pays, your wallet needs no balance to create or use an identity; sending coins is the one operation you pay yourself.

### 5.5. Viewing Logs

The server writes operational logs: which routes were called, which transactions it prepared and confirmed, errors, the state of its gas pool. They carry no plaintext e-mail addresses and no session tokens. Operations are reviewed daily from these logs; the daily records are internal.

### 5.6. Resetting the Node — RETIRED

There is no wot.id node to reset.

## 6. Current Deployment Status

### 6.1. Production Environment

| Item | State |
|---|---|
| Network | IOTA mainnet, reached through a public node endpoint; no wot.id-operated node |
| Contracts | wot.id's package plus the separate offline-messages package, both on mainnet; ids in [05](05_Move_Smart_Contracts.md) §6.1 |
| Transaction path | server builds and co-signs for the fee, browser signs and broadcasts, server confirms by digest |
| Fees | paid by wot.id's gas wallet for basic use; coin transfers paid by the user |
| App | https://wot.id |

### 6.2. Network Performance

A transaction is final within seconds of being broadcast; the app reflects a confirmed change as soon as the server has read it. The network's protocol moves on its own schedule; the server's tooling is kept in step with it (§3.1).
