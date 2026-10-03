# 11: Onboarding and Adoption

*As of 2026-10-03. The public version of the foundational document of the same name: what wot.id is and is not yet, how a newcomer starts, how abuse is kept in check, where it stands on the adoption curve, and what it learns from adjacent systems. General mechanism, no implementation detail.*

---

## 1. The Innovation and the Adoption Reality Today

### 1.1. What wot.id Actually Is

A working self-sovereign identity and trust system on IOTA mainnet. What ships today:

- **An identity that is yours** — an object on the ledger named by its own id, created and controlled by a key generated in your browser; no server ever holds it.
- **Three doors to it** — a passkey, a provider (Google, Apple, GitHub), or the 24 words themselves — and two ways to create it: with a passkey alone, or after a provider sign-in.
- **Encrypted details on the ledger**, one value at a time, with three privacy bands; your name and account type readable by the people you admit, everything else by you alone until you show it.
- **Vouches on the ledger**, given face to face, per detail, bound to the detail's version, counted and open to inspection; measuring trust is the long-term aim.
- **Hybrid post-quantum encryption** of everything you store and send; classical signatures until the ledger supports post-quantum ones.
- **Talk** — encrypted conversations and groups, live and offline, with history that follows you.
- **Encrypted Files** — a catalogue on the ledger, encrypted bytes in the wot.id cloud, sharing by consent.
- **Guardian recovery** — two people you chose plus your recovery code bring your 24 words back.
- **Network fees paid by wot.id for basic use.**
- **The same identity for machines.** An AI agent, a bot, a device or an organisation gets the same kind of identity, declares its kind as a detail, and can hold details, be vouched for and vouch, talk, and share files. Everything an agent signs is attributable to its identity and cannot be denied later. There is no SDK and no documented API for agents: the web app is the interface.

### 1.2. What wot.id Is Not (yet)

- **Not yet piloted with a cohort of end users.** The open beta planned for the second quarter of 2026 did not launch; reliability of the core loop was chosen first (§3).
- **No governance in the app.** Proposal and voting building blocks exist in the contracts; no interface uses them.
- **No federation with other networks**, no direct device-to-device transport in normal operation, no integration with other identity, social or credential systems.
- **No external resolution** of wot.id identities by other DID resolvers.
- **No incentives or rewards** of any kind.
- **For agents: no delegation.** A person cannot yet grant an agent bounded authority as a signed statement, an agent cannot be linked to its operator on the ledger beyond a self-declared detail, and there are no vouches in agent-specific contexts such as competence or safety.
- **No score for anyone**, no public profile, no search, no directory.

### 1.3. Where wot.id Sits on the Adoption Curve

At the innovators' end. The system is live and used by its builders and testers; the structured approach to early adopters — the open beta — has not started. Crossing to a wider audience needs social proof that does not yet exist, and wot.id's own documents say so.

## 2. Perceived Attributes of the Innovation

### 2.1. Relative Advantage

Data you own and a server that cannot read it; an identity no provider can close; signed, portable vouches; post-quantum protection of what you encrypt. Most people do not feel "the server cannot decrypt my data" as a benefit until a breach makes the news; the advantage has to be shown, not asserted.

### 2.2. Compatibility

A sign-in you already know, a wallet that needs no setup and no balance, and a passkey where the device has one. The one unfamiliar step — backing up 24 words, or trusting a passkey backup — is placed right after creation and cannot be skipped.

### 2.3. Complexity

The app hides the ledger, the keys and the transactions behind familiar screens. wot.id is self-custody from the first minute, with a usable abstraction on top; there is no path on which wot.id holds your keys for you.

### 2.4. Trialability

Anyone can create an identity, record details, get a first vouch from someone they meet, talk, store a file and set up guardians without buying anything or installing a wallet. wot.id pays the fees for basic use, within limits (§5).

### 2.5. Observability

Every vouch, every identity and every catalogue entry can be opened on the public explorer ([05](05_Move_Smart_Contracts.md) §6.1). What does not yet exist is anything that propagates on its own — no shareable profile, no public page.

## 3. The Q2 Pilot — IOTA Community Open Beta

An open beta for the IOTA community was the planned first cohort for the second quarter of 2026: reachable, able to onboard itself, with no institutional contract surface, and inclined to report failures as bugs. The quarter closed without the launch, under the decision to drive the core loop — create, back up, verify, talk, store, recover — to a reliability bar first. The pilot design remains the intended shape of the open beta; no date is given.

### 3.1. The audience choice

The IOTA community; an academic pilot was dropped.

### 3.2. What the audience is *not*

Not demographically representative — crypto-natives are not the median future user, and friction found with them will need revalidation with a non-crypto cohort — and not an early-majority audience, which needs social proof the first cohort would produce.

### 3.3. Success metric

A handful of people using wot.id independently over weeks — more than the builder's friends, fewer than a script could fake. Sign-up counts are not the measure.

## 4. The Operational Onboarding Flow (Q2)

The first hour, in the order you meet it ([08](08_Frontend_And_User_Experience.md) §4):

1. **Open https://wot.id** and consider installing it to your home screen, which on an iPhone or iPad protects your stored keys from the browser's periodic clean-up.
2. **Create your identity** — *New here?* with a passkey alone, or sign in with Google, Apple or GitHub and press *Create New Profile*. Your 24 words are generated in your browser; your DID appears.
3. **Back up the private key** — the passkey backup first, or the words on paper, proven by re-entering two of them. The app does not continue without this.
4. **Add your details** under *Identity & Personal Info* — as much or as little as you like; nothing is required.
5. **Get verified** on *Trust*, face to face: show a code, let someone who knows you scan it and confirm what they have checked; vouch for them in turn. The vouch appears on both sides with its link to the ledger.
6. **Place people in circles** under *People & Groups*, knowing that admission hands them your name and account type and cannot be withdrawn.
7. **Talk, store a file, set up guardians** — the guardian set becomes usable 48 hours later, so set it up early.

Steps 1 to 4 take minutes; steps 5 to 7 need a second person and a little more time.

## 5. Sybil and Abuse Defenses (Q2 Posture)

The defences are proportionate to what is at stake — wot.id's sponsored fees — and are designed to raise the cost of mass creation above its payoff, not to make it impossible.

- **Through the provider door, one identity per e-mail address**; common spelling variants of the same address are treated as the same address.
- **Through the passkey door, a daily limit** on identities wot.id sponsors, per internet connection and for everyone together. People who share a connection — a household, an office — share its allowance. When the limit is reached, the sign-in page says so and nothing is created; you try again the next day, or start with a provider.
- **For every identity, a daily allowance of sponsored operations**, and a limit for the whole service — more than any person needs, less than a script wants.
- **Sign-in attempts are limited** per address and per account.

Not in place, by decision: phone verification, biometric proof of personhood, captchas, behavioural analysis. Human verification is a vouch by a named person who met you — evidence, not a uniqueness guarantee. The numbers behind the limits are not part of the public description.

## 6. Adopter Segmentation (Long Horizon, Past Q2)

Innovators — technically minded people close to the IOTA ecosystem — are reached today through the running system and these pages. Early adopters — people who want a polished product that does something useful — are the open beta's audience. The early majority adopts only when credentials and use cases their daily life depends on exist, attested by parties they already trust; that is a later conversation, and the current onboarding is infrastructure for it, not a strategy for reaching it.

## 7. Lessons from Adjacent Cases

### 7.1. Threads (2023)

Sign-ups are not adoption. The onboarding must be a complete loop — create, back up, verify, talk, store, recover — not a welcome screen.

### 7.2. Worldcoin (2023–2025)

Proof of personhood by biometric capture carries real friction and a real trust burden on its issuer. wot.id does not go down that road: it collects no biometrics — the Face ID or Touch ID behind a passkey stays on your device — and its closest equivalent is a human verification by a named person, face to face. Its brakes on mass creation are the limits of §5, not a proof of personhood.

### 7.3. Bluesky / AT Protocol (2024–2026)

A portable identity primitive without a product around it is a primitive without a market. wot.id's story has to be what you can do — be verified, talk, store, recover — not the architecture underneath. Related comparisons, stated as they are: provider login is one door among three here and never sees your key or data; a PGP signature says "this key belongs to this person", a wot.id vouch says "this detail of this person is true" and comes with keys people can keep and recover; on-chain names and social graphs put a name or a follow graph on a ledger, wot.id puts signed statements about encrypted details there; a credit score is a central, opaque number, wot.id computes none and would open any future number to its evidence.

## 8. What Earlier Drafts Got Wrong (corrected here)

Earlier internal drafts described the server managing users' keys at first, a wallet to connect later, governance as operational, and metrics for a user base that did not exist. None was ever true of the system that ships: keys have been client-side from the start, the wallet exists from creation, governance has no interface, and the measures are small and honest. The corrections are recorded internally; this page states only the current reality.

## 9. Open Questions (Long Horizon)

What the first product story for pragmatic users is — medical records, professional credentials, payment-history attestations are candidates, none a product today; which partnerships would carry trust to them; when measuring trust becomes a visible improvement over counting it; when the abuse defences must escalate; whether external DID resolution becomes valuable. Open, undated.

## 10. References

- The other pages of this set: [01](01_Project_Overview_And_Principles.md) principles and status; [02](02_System_Architecture.md) architecture and security; [05](05_Move_Smart_Contracts.md) contracts and ids; [07](07_Trust_Architecture_And_Management.md) trust; [08](08_Frontend_And_User_Experience.md) the app.
- W3C DID Core 1.0: https://www.w3.org/TR/did-core/
- The IOTA mainnet explorer: https://explorer.rebased.iota.org

| Capability | State | Since |
|---|---|---|
| Creating an identity after a provider sign-in | live | November 2025 |
| Creating an identity with a passkey alone | live | 30 September 2026 |
| Backup gate — passkey backup first, then the words | live | August 2026 |
| Agents as ordinary identities with a declared kind; Agents sections | live | August 2026 |
| Sponsored fees with limits per identity, per connection and per day | live | 15 September 2026 · 30 September 2026 |
| Open beta | planned, no date | — |
| Key-first onboarding for agents, SDK; bounded delegation | not built | — |
