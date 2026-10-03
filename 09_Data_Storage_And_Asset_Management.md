# 09: Data Storage and Asset Management

*As of 2026-10-03. The public version of the foundational document of the same name: where your data lives — encrypted values on the ledger, the Encrypted Files catalogue and the wot.id cloud, the Mailbox — who can read what, and what the wallet does today. General mechanism, no implementation detail.*

---

## 1. Introduction

wot.id stores **values**, not files: the facts you extract from your documents — a name, a date of birth, a passport number — each encrypted on your device and written to the ledger. The documents themselves stay on your own device or in your own cloud, unless you store them under Encrypted Files. Encrypted Files is a separate catalogue of files you chose to link to your identity: the catalogue is on the ledger; the encrypted bytes of every file stored today are in the wot.id cloud, with an encrypted copy on your device if you keep one. Linking a file never replaces extracting its values, and extracting values never requires linking the file.

One private key binds both: the keys for your values are derived from it, and a file's own random key is encrypted to the key pair built on it. Possession of the 24 words is what makes the two stores one whole rather than disconnected fragments.

### 1.1. Storage Architecture Overview

| Where | What | Readable by |
|---|---|---|
| The ledger | your identity, your encrypted values with their labels, the vouches, your file catalogue, your offline messages, your recovery settings | structure by anyone; content by the holders of its keys |
| The wot.id cloud (EU) | the encrypted bytes of your files; by default your encrypted Mailbox | wot.id holds ciphertext it cannot read; the bytes carry the owner's name |
| Your device | your keys; an optional encrypted copy of each file; your Talk history | you |

wot.id runs no database for identity data. The cloud tier is its one persistent store, a durability convenience rather than a source of truth.

## 2. Core Principles for Data and Asset Management

Values on the ledger, encrypted; atomic — each value independent, shared or withheld on its own; a trust record per value, counted today and measured in the long term; security and privacy by design — hybrid post-quantum key delivery and three privacy bands per value ([05](05_Move_Smart_Contracts.md) §5.4); and user control — you decide what is stored, how it is shared, and with whom.

## 3. On-Chain Storage (IOTA Mainnet with Move)

### 3.1. Identity Registry Architecture

The shared registry maps names to identities; profiles hold the values ([05](05_Move_Smart_Contracts.md) §2.5).

### 3.2. What Lives On-Chain (100% of Data VALUES)

Your DID and the keyed hashes of your secondary identifiers; your encrypted values with their labels, privacy bands and timestamps; your published public keys; the vouches with their status and the version of the detail they point at; your file catalogue; your drop box of offline messages; your recovery settings. What the ledger shows of a value is its label and when it changed — never the value.

### 3.3. Atomic Data Structure Architecture

Identity is composed of independent fragments. Each value is its own encrypted entry; a vouch names one value at one version; a privacy band applies per value. You can reveal one fragment to one person without the others.

## 4. Optional Off-Chain Storage: Supporting Document FILES Only

The internal document designed a path that would link a stored document to the values extracted from it and verify the document by its hash; that path is designed and not built — the app has no feature that connects a file in the catalogue to a detail. What exists off the ledger is the Mailbox (§4.6) and the Encrypted Files catalogue's bytes (§4.7).

### 4.1. Document FILES That May Live Off-Chain (Optional)

Any file: a scan, a photo, a contract, a recording. Whether a file corresponds to a document you also extracted values from is your decision; the catalogue does not know.

### 4.2. Deterministic Linking Mechanism

Designed, not built.

### 4.3. Storage Options

Each catalogue entry records where its encrypted bytes are. The app writes two places: the wot.id cloud, the destination of every file stored since July 2026, and the device, for entries from before that and for the optional extra copy in a folder you grant the browser. Two further codes — a public content network and a cloud of your own — exist in the contract and are written by nothing; storing files in a cloud you choose is designed, not built.

### 4.4. Document Verification Flow

Designed, not built.

### 4.5. Access Control and Encryption

Your keys are generated and held by you. A file is shared by handing one person a copy of its key, sealed to them; a share has no expiry of its own and is ended by revocation.

### 4.6. Mailbox — blind cloud-parked encrypted Talk history **(NEW 2026-07-16)**

Your Talk history is kept on your device and, by default, as one encrypted artifact in the wot.id cloud that only your private key opens, updated after every change, so that your conversations converge on every device you sign in on. wot.id stores and serves the sealed artifact and cannot read it; restoring from a file you downloaded works without wot.id's server. You can take the Mailbox out of the cloud and keep the file yourself. Deleting it removes every stored version. The Mailbox is distinct from offline messages, which are the drop box on the ledger ([06](06_P2P_Communication.md) §7).

### 4.7. Encrypted Files — the file catalogue, as built (2026-10-02)

- **Encryption.** A file is encrypted on your device with its own fresh random key; a hash of the original goes along so that any altered copy fails loudly. That file key is encrypted to your hybrid X25519 + ML-KEM-768 key pair, built on your private key. A folder is zipped first.
- **The catalogue entry on the ledger** records the hash, the encrypted file key, the file's size and category, its name and type as sealed tokens that are not readable there, and where the bytes are. It holds none of the file's bytes.
- **Where the bytes are.** Every file stored today goes to the wot.id cloud, in the EU, as ciphertext behind a header naming the owner; wot.id can read the header and the size, not the content or the name. Before it hands out a download link, wot.id's server checks that the stored object carries the expected owner's header and looks like ciphertext. You can also keep an encrypted copy in a folder on your device that you grant the browser, and download the encrypted file at any time. The cloud holds a bounded amount per identity; the limits are not part of the public description.
- **Sharing.** Only a cloud entry can be shared. Your device re-encrypts the file's key for the recipient — the bytes are not touched — and the offer goes to them on the ledger; they accept with their own signed transaction and can then fetch and decrypt the bytes with a short-lived link. You can share with a whole chat group in one go. **Revoking** a share records the revocation on the ledger, stops new download links for that person and does not re-encrypt the file; a link already issued works for its short remaining life; you can restore a revoked share.
- **Removal.** Deleting a cloud entry erases the stored bytes, every version of them, and then removes the catalogue entry. Deleting a shared file revokes nothing by itself: a recipient's share entry stays on their side until they remove it, and their next download fails because the bytes are gone.
- **What wot.id and the ledger can see.** wot.id's server and the cloud's operator see the owner's name on each stored object, its size, its header, and the names of the people a file was shared with. Anyone reading the ledger sees, per file, the hash, size, category, storage code and pointer, and per accepted or rejected share the two addresses involved — never the content, and the name and type only as sealed tokens. Entries written before the sealing keep their names in the clear.
- **On a new device**, your private key opens your whole catalogue again.

## 5. Digital Asset Management in wot.id

What exists today: your identity has an IOTA address; under **Transfer** you see your balance and history and can receive and send IOTA and on-ledger objects, paying the fee of a transfer from your own balance. The wider scope the internal document describes — tokens, non-fungible assets, representations of real-world assets, gaming assets, an on-chain shop or vault per user — is a vision, not built.

### 5.1. Core Technologies and Standards

Any asset wot.id would manage lives on IOTA mainnet as Move objects, moved by programmable transaction blocks you sign.

### 5.2. Fungible Assets (Tokens)

IOTA itself is the one fungible asset the app handles today.

### 5.3. Non-Fungible Assets (NFTs)

Objects you own appear in your wallet; no creation or marketplace feature exists.

### 5.4. Representing Real-World Assets (RWAs)

Vision.

### 5.5. Gaming Assets

Vision.

### 5.6. Asset Lifecycle Management (Common Operations)

Today: receive and send. Creation, metadata and burning of assets of your own are not built.

### 5.7. Advanced Asset Interactions with the IOTA Kiosk Pattern

Vision.

### 5.8. Security and Access Control for Assets

Ownership on the ledger is by key: an object moves only with its owner's signature.

### 5.9. Phase 2: Asset Governance and On-Chain Anchoring

Design.

## 6. Data Fragmentation and Recomposition

Your profile on the ledger is a set of independent entries — one per encrypted value, plus the keys and settings around them — and your catalogue a set of independent file records. You disclose fragments one at a time: one detail to one verifier, one file to one recipient.

## 7. Security and Privacy Considerations for Storage

Content is encrypted everywhere: every value and every file is ciphertext before it leaves your device, and the ledger holds no plaintext content. Structure is public: labels, timestamps, file sizes and categories, hashes, who vouched for whom, who shared with whom. Closing the remaining metadata gaps — the file category, for instance, is readable — is owed work, stated as such. Access to anything on the ledger is by key; access to the cloud's bytes is by short-lived links the server issues only to the owner or an un-revoked recipient.

## 8. Future Considerations

Storing files in a cloud you choose (designed); hiding the public structure with commitments and zero-knowledge proofs (planned, no date); re-keying a shared file after a revocation (named, not designed).

| Capability | State | Since |
|---|---|---|
| Encrypted values on the ledger | live | December 2025 |
| Encrypted Files: catalogue on the ledger, bytes in the wot.id cloud by default, optional encrypted device copy | live | cloud default since 28 July 2026 |
| Sharing with one person; revocation and restoration on the ledger | live | July 2026; restoration since August 2026 |
| Sharing with a whole chat group | live | 10 September 2026 |
| Sealed file names and types on the ledger | live | September 2026 |
| Mailbox in the cloud by default, with opt-out | live | July 2026; opt-out since August 2026 |
| Files in a cloud you choose | designed, not built | — |
| Linking a file to the values extracted from it | designed, not built | — |
