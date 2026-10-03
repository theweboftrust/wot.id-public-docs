# 08: Frontend, UX, and Client Applications

*As of 2026-10-03. The public version of the foundational document of the same name: the app in your browser — what it holds, how it protects your keys, what you meet in the first hour, and how you get back in. Screen names are given as the app shows them. General mechanism, no implementation detail.*

---

## 1. UX Philosophy and Core Principles

- **Absolute user control.** Keys are created and kept on your device; every change is yours to sign.
- **wot.id manages values, not files — plus the Encrypted Files catalogue.** The Identity and Linked Accounts sections lead with the values you recorded and where they came from. Encrypted Files is a separate section for files you chose to link; the name says what it is.
- **Guaranteed human identity.** Verification is clear, face to face, and attributable.
- **Open and accessible.** Minimal friction: a sign-in you already know, or a passkey, and no wallet to set up.
- **Clarity and simplicity.** Trust is shown as counts you can open, never as an unexplained number.
- **Transparency and optional complexity.** The default view is simple; every count opens to its evidence and every record to the ledger.
- **Privacy by design.** Nothing you record leaves the device unencrypted.
- **Slow where it matters.** A new account cannot continue until its private key is backed up; verification is designed to happen in person; a new guardian set becomes usable only after 48 hours.

## 2. Frontend Architecture and Technology Stack

### 2.1. Current Status

The app runs at https://wot.id and installs like an app from the browser. The sign-in page offers, in this order, a passkey, the providers Google, Apple and GitHub, and the private key; under the passkey button a *New here?* entry creates an identity for a visitor who has none. The app has four places: **Talk** (conversations), **Transfer** (your wallet), **Trust** (getting verified and verifying others), and **Me**, with the sections *Identity & Personal Info*, *People & Groups*, *Agents*, *Digital Assets*, *Encrypted Files*, *Linked Accounts* and *Recovery*. There is no offline mode and there are no push notifications.

### 2.2. Architecture Principles: Frontend as Display Layer

The app displays what is on the ledger and holds nothing of its own except your keys and, for Talk, your conversation history on this device. It encrypts before sending, decrypts after reading, and signs every change. It reads objects from the ledger directly where it can and broadcasts every signed transaction to the network itself.

### 2.3. High-Level Architecture (December 2025)

Browser app → wot.id's server (prepare, confirm, relay, cloud links) and → IOTA mainnet (broadcast, read). The server never holds a key; the ledger never holds plaintext content ([02](02_System_Architecture.md) §5.1).

### 2.4. Technology Stack

A web application served from wot.id's hosting, running in current browsers: Chrome or Edge on a computer, Safari on an iPhone or iPad, Chrome on Android. On an iPhone or iPad, installing it to the home screen matters: the browser deletes a website's stored data after a period without a visit, and your keys and conversations live in that storage; an installed app is exempt. Library and framework versions are not part of the public description.

### 2.5. Trust Scale System and Attestation Workflow

The Trust page carries *Get Verified* (show a code: human verification only, or selected details) and *Verify Others* (scan a code, confirm each detail, sign). *My Attestations* lists what you received and what you gave, each row with a status, the check against the ledger and a link to the record; the summary line counts people and details. No score is shown for any person; the −100…+100 scale is reserved ([07](07_Trust_Architecture_And_Management.md)).

### 2.6. Phase 2: Governance and On-Chain Anchoring Frontend Integration

There is no governance surface in the app; the contracts' proposal building blocks have no user interface ([10](10_Governance_And_Conflict_Resolution.md) §4).

### 2.7. Development Best Practices

Internal.

## 3. Client-Side IOTA Integration and Security

### 3.1. Client-Side Security & Gas Station Pattern

You need no IOTA tokens and no wallet extension. The app derives your keys from your 24 words and signs every transaction in the browser; wot.id's gas wallet pays the fee and co-signs only for that payment. Sending coins is the one operation paid from your own balance.

### 3.2. Backend API Integration

For every write the app asks the server for a prepared transaction, signs it, broadcasts it to the network itself, and reports the digest back for confirmation. For reads it asks the server or reads the ledger directly.

### 3.3. PQC Identity Field Encryption/Decryption (December 2025)

Each detail is encrypted on your device with a key derived from your private key, one key per field, before it is saved; it is decrypted on your device when shown. What the ledger holds is the field's label and when it changed, never the value ([02](02_System_Architecture.md) §10.2).

## 4. Key User Journeys & Features

### 4.0. The user-level identity model, and where it is taught *(added 2026-09-01)*

1. **One secret holds it all together.** Your identity, your encrypted values, your web of trust and your assets live on the public network, not on wot.id's servers. One secret in your hands both signs and encrypts.
2. **The pair.** A private key signs; its public key lets anyone check the signature; the private half cannot be derived from the public.
3. **The 24 words are the private key** — the same number in word spelling, not a password to a key held elsewhere. Elsewhere the words are also called a *recovery phrase*, *seed phrase* or *mnemonic*; the app calls them your private key. Your address is a fingerprint of the public key. The DID is a separate, permanent name: the id of your profile object, minted by your first signed transaction, independent of the key.
4. **The vault.** The key persists per device in the browser's storage, wrapped under your passkey when you have one, so that opening it takes a biometric; the private key re-creates the pair on a new device.
5. **One identity, different doors.** Google, Apple or GitHub open a session — convenience. The private key is the master door, and one of three ways to put the key on a new device: the words, the passkey backup on the ledger, and guardian recovery. A compromised provider account yields a session, never the key.
6. **Every write is the same move.** The server builds and co-signs for the fee, your device signs, your device broadcasts, the server confirms, the network verifies.
7. **The trade.** No password resets, words on paper, devices as cheap copies.
8. **Guardian recovery is the safety net beyond the words.** Two people you chose, from those who have verified you, each hold a sealed share of your private key; both shares and your 12-word recovery code are needed to rebuild it, and neither they nor wot.id can use it without you. Inside: your private key is first encrypted under a key derived from the recovery code, then split into two shares, each sealed to one guardian's keys and recorded with your signature; the set becomes usable 48 hours after you create or change it, a delay fixed in the contract. In a recovery you start a request from a new device for your DID; the ledger accepts it only from that device's new key; you read a short fingerprint of that key to each guardian in person or by phone; each approves at wot.id, and their share travels with the approval, re-sealed to your new device; with both shares your browser rebuilds the wrapped key and opens it with your recovery code. Your original 24 words are back; nothing about your identity changes on the ledger. You can cancel any request you did not start with *This wasn't me*. Limits: lose the recovery code and this path cannot help; both guardians must be reachable; it restores the same key and does not replace a stolen one; changing a guardian set is supported by the contract and not yet by the app.

The app teaches this model on its How-to and About pages.

### 4.1. New User Onboarding & Identity Creation (as of June 2026 — v16, two user-signed transactions)

Two doors create an identity; both lead to the same kind of object on the ledger, controlled by a key only you hold.

**With a passkey alone.** On the sign-in page, *New here? Create my identity with a passkey*. Your browser generates the 24 words and shows them once; you write them down. It proves possession of the new key to wot.id's server, which sponsors the creation; your DID appears, `did:wot:0x…`, the id of your new profile object. Your public keys are published, the backup of your private key is written to the ledger under a secret only your passkey can produce, and the passkey is set up last. wot.id sponsors only so many creations per connection and per day; when the limit is reached the page says so and nothing is created ([11](11_Onboarding_And_Adoption.md) §5).

**With Google, Apple or GitHub.** The provider confirms your e-mail; under **Me → Identity & Personal Info** you press *Create New Profile*; your browser generates the 24 words and signs the creating transaction; wot.id's server prepares it and pays its fee; your DID appears. If the provider supplied your name, the app offers it for you to confirm or edit before anything is saved.

**The backup you cannot skip.** Before a new account goes any further, the app asks for a backup of the private key: a passkey backup first — your private key encrypted under a secret only your passkey can produce and written to the ledger beside your profile, so a later device in the same passkey ecosystem can restore it with one biometric — or, where the device cannot do that, the 24 words written on paper and proven by re-entering two of them. Your 24 words stay valid either way. A signed-in user can see the 24 words again from the Identity section.

### 4.2. Profile Data Management

**Your details.** Under *Identity & Personal Info* you can record an account type (*Human*, *AI*, *Bot*, *Organization*, *IoT Device* or *Other*), names, date and place of birth, nationality, gender, address. Each value is encrypted on your device when saved. Your first and family name and your account type are readable by everyone you admit to your circles; every other detail is readable by you alone, until you choose it for a verification or an exchange. There is no public profile and no search.

**Circles.** *People & Groups* lists everyone you have verified or who has verified you. You place each of them by closeness — *Inner circle*, *Good friends*, *Friends*, *Acquaintances*, *Known faces* — and by context — *Family*, *Work* and more, or a category of your own. The circles are yours alone; nobody sees which circle they are in. Placing someone in any circle **admits** them: from then on they can read your name and account type. Two limits, stated as they are: admitting someone to any circle lets them read everything you share with any circle, and an admission cannot be withdrawn — *Remove from circles* stops their app showing your details, but the key they received is not taken back.

**Agents.** *Me → Agents* and *Trust → Agents* list the identities in your web of trust that declare an agent kind — *AI*, *Bot* or *IoT Device*. An agent holds the same kind of identity as a person and is created through the same two doors; it declares its kind as a detail like any other ([11](11_Onboarding_And_Adoption.md) §1.1).

**Linked Accounts.** *Me → Linked Accounts* adds a second provider as a door to the same identity.

**Encrypted Files.** *Me → Encrypted Files* stores a file or a folder (a folder is zipped first): encrypted on your device, stored in the wot.id cloud, entered in your catalogue on the ledger; optionally an encrypted copy in a folder on your device; *Share* for one person or a whole chat group; revoke and restore ([09](09_Data_Storage_And_Asset_Management.md) §4.7).

**Talk.** Start a conversation by code or DID, or a group from the people who have verified you; messages wait on the ledger for an offline recipient; your history follows you through the Mailbox, cloud by default, or a file you keep yourself ([06](06_P2P_Communication.md)).

**Recovery.** *Me → Recovery → Set up guardian recovery*: pick two guardians from the people who have verified you, write down the 12-word recovery code the app shows once, confirm it, sign. Usable 48 hours later (§4.0, item 8).

**Getting back in.**

| Your situation | What you do |
|---|---|
| A new device, and you made a passkey backup | Sign in with your provider, or start from the passkey; the app restores your private key from the backup with one biometric. |
| A new device, and you have the 24 words | Open the private-key panel on the sign-in page and type the words. No provider is involved. |
| Every device and the 24 words are gone, and you set up guardians | Follow the guardians link on the sign-in page to *Recover my identity* and run the recovery of §4.0, item 8. |
| Every device and the 24 words are gone, and no guardians | Nobody can restore the identity — not wot.id, not anyone. You start a new one; vouches given to the old one stay with it. |
| Someone else has your 24 words | They can act as you, and there is no way today to change the key of an identity. Start a new identity, tell the people you deal with, and treat the old one as hostile. |

### 4.3. wot.id as Comprehensive Digital Wallet (Full Vision)

**Transfer** holds your IOTA address, your balance and the history of what moved. Receiving works from the start; sending is paid from your own balance, and identity operations never needed it. The wider vision — a wallet for every kind of digital asset — is a vision ([09](09_Data_Storage_And_Asset_Management.md) §5).

## 5. Future Directions

Personalisation of trust insights, incentives for building the web of trust, and in-app education are named as directions. None is built, none is dated.

| Surface | State | Since |
|---|---|---|
| Sign-in page: passkey, providers, private key; *New here?* creation | live | 25 September 2026 · 30 September 2026 |
| Backup gate with the passkey backup first | live | August 2026 |
| Seeing the 24 words again when signed in | live | 30 September 2026 |
| Identity details, circles, Agents, Linked Accounts | live | circles since July 2026; Agents and Linked Accounts since August 2026 |
| Trust: in-person verification, records, erasure | live | erasure since July 2026 |
| Talk with groups; Encrypted Files with sharing | live | groups since August 2026; sharing since July 2026 |
| Recovery section: guardian setup, cancel a request | live | 19 September 2026 |
| Changing a guardian set in the app | not built | — |
| Offline mode, push notifications | not built | — |
