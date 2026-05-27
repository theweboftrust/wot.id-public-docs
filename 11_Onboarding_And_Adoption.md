# 11: Onboarding and Adoption

> **Document scope**: this document is the **strategic adoption framework** for wot.id, calibrated against the actual architecture and the operational state as of May 2026. It covers Rogers' Diffusion of Innovations *five-attributes* layer (Chapter 6 of Rogers) plus *adopter-segmentation* (Chapter 7) plus the chasm-crossing logic at a high level, and points at the concrete operational source of truth for the current Q2 pilot. **For the operational onboarding flow, funding model, Sybil defenses, and pre-launch checklist, see `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md`.** Last fact-audited 2026-05-19 (smart-contract v10 publish, May 17, is reflected in §1.1).
>
> **What this document does *not* cover** (and where to find it): the network-theoretic layer of diffusion (Rogers Chapter 8 — networks, weak ties, structural holes, complex vs simple contagion); Rogers' innovation-decision 5-stage process (Chapter 5) — knowledge / persuasion / decision / implementation / confirmation; the consequences framework (Chapter 11) and the innovation-equality gap; and the post-Rogers modern evolutions (Moore's chasm + bowling alley, Centola's complex contagion, Gladwell's Tipping Point, Berger's STEPPS, Christensen's disruptive innovation, Christakis-Fowler's three degrees of influence, TAM / UTAUT, COM-B, the startup canon of PMF / K-factor / growth loops / North Star). For all of those, see `docs/2026_Code_Work/26-05-19_Diffusion_Strategy.md` — that doc treats the broader integrated picture as a brainstorm; this doc remains the long-horizon adoption-strategy reference.
>
> The previous version of this document contained architecturally wrong claims about backend key custody and wallet-setup-in-Month-3 that were removed in the May 10 audit.

---

## 1. The Innovation and the Adoption Reality Today

### 1.1. What wot.id Actually Is, in May 2026

wot.id is a working self-sovereign identity (SSI) and trust system on IOTA Rebased mainnet (Protocol 26, Starfish consensus). What ships today:

- **W3C DID Core 1.0 compliant identities** (`did:iota:mainnet:<blake3-hash-of-pubkey>`) generated client-of-Backend via Ed25519 + BLAKE3. *(DID generation was inlined into the Backend API on 2026-03-07; the dedicated Identity Service microservice was retired then — see `docs/2026_Code_Work/26-03-07_Identity_Service.md`.)*
- **100% on-chain identity data VALUES** with per-VALUE trust scores (`-100..+100`) on IOTA mainnet. No traditional database. **The source documents those VALUES were extracted from (PDFs, scans, CSVs, photos) stay on the user's own device or cloud — wot.id is not a file-storage provider.** The encryption key that protects on-chain VALUES is the same key the user uses to encrypt/decrypt at the source, which is what binds the off-chain documents to the on-chain VALUES into a single coherent identity-data graph. See `docs/01_Project_Overview_And_Principles.md` Principle #4 + `docs/Claude_Primer.md` §17 + `docs/09_Data_Storage_And_Asset_Management.md` §1.
- **Post-quantum hybrid encryption** (X25519 + ML-KEM-768 via `@noble/post-quantum` v0.6.1 since 2026-05-26; previously the Dashlane `pqc-kem-kyber768-browser` WebAssembly wrapper Dec 2025 – May 2026, swapped because it had no deterministic-keygen API), ChaCha20-Poly1305 symmetric for all sensitive identity fields. **Keys are owned by the user via a 24-word BIP-39 mnemonic** — never by the backend, never by wot.id servers. Both halves of the hybrid keypair are deterministic from the BIP-39 mnemonic since 2026-05-26 (ML-KEM-768 derives via HKDF-SHA256 salt `wot.id/mlkem-768/v1` from the X25519 private key — `docs/2026_Code_Work/26-05-26_PQ_Cryptography.md`, Open-Issues #9 closed). The first mainnet PQC-encrypted transaction landed Dec 23, 2025.
- **OAuth auto-provisioning** for Google, GitHub, Apple. New OAuth users get a DID and an auto-assigned personal IOTA wallet (with exportable mnemonic) on first sign-in.
- **On-chain attestations** via the `wot_trust` Move module. Cross-device QR attestation flow operational since Nov 17, 2025; first production attestation Nov 19, 2025.
- **3-band privacy model** (`0 = Public`, `2 = Selective` via owner-issued time-limited `PrivacyAccessGrant`, `3 = Private` default) — collapsed from a 4-band model on May 8, 2026 by dropping the "Trusted Contacts" and "Temporary Access" levels that had no working resolver / had been superseded.
- **Gas-station pattern**: Backend sponsors the profile-creation transaction (~0.0076 IOTA) so a brand-new OAuth user reaches a usable identity without holding IOTA themselves.
- **Smart contract package**: v11 `0x40e24bdddd34bdac9ebcfe2d60da0585dbd3b2fa261b716264b5a43597bfe299`, deployed May 23, 2026 on mainnet. Lineage: v7 (Jan 9) → v8 (Mar 11) → v9 (May 8) → v10 (May 17) → v11 (May 23).
- **Trust-score compute**: v0 backend aggregator — flat arithmetic mean of the last 100 attestation events. The Bayesian inference engine in `docs/2026_Code_Work/26-05-01_Proprietary_Algorithm_Draft.md` is **unbuilt**; v1 (client-side aggregator) and v2 (read-side indexer) plans are in `docs/2026_Code_Work/26-05-08_Inference_Compute.md`. v0 is sufficient for Q2-scale traction.

### 1.2. What wot.id Is Not (yet)

Honest distinctions, calibrated against the May 10, 2026 plan and pilot strategy:

- **Not yet pilot-tested with end users**. The first traction goal — 5+ test users using wot.id independently by June 30, 2026 — is the active Q2 minimum-success criterion. Monthly-cohort progressive-decentralization frameworks in older drafts of this document presupposed a user base that does not yet exist.
- **No production governance UI**. The Move primitives for proposal-based governance are deployed in `wot_trust`, but the backend REST endpoints and frontend UI for proposals were deleted on 2026-03-07 because they were mock-only and returned fabricated data (`docs/2026_Code_Work/26-03-07_Dead_Endpoints_Audit.md`). Production governance is **deferred to Q3+**.
- **No federation / no libp2p**. The libp2p Rust networking stack was deleted from the backend in May 2026 (`docs/2026_Code_Work/26-05-07_2026_Q2_Plan_Update2.md`). P2P messaging today is WebSocket relay (production) + WebRTC (flagged) — see `docs/06_P2P_Communication.md`.
- **No external DID resolver integration**. wot.id DIDs are resolvable within the wot.id ecosystem; resolver.identity.foundation registration is an optional future enhancement, not a compliance requirement for an SSI system.
- **No incentive system / token rewards**. Designed in `docs/2025_Code_Work/25-12-09_Incentive_Scheme.md` and `25-12-21_Incentive_Scheme.md` but explicitly deferred until "after 5+ active users" per the April 1 Q2 plan and unchanged since.

### 1.3. Where wot.id Sits on the Adoption Curve (May 2026)

```
Innovators (2.5%)  │ Early Adopters (13.5%) │  THE CHASM  │ Early Maj. │ Late Maj. │ Laggards
───────────────────┼────────────────────────┼─────────────┼────────────┼───────────┼─────────
        🟢  Engaged (technical docs, OSS, Discord proximity)
        🟡  About to be approached
            via the IOTA-community open beta
            (Q2 2026 pilot — see 26-05-10_2026_Pilot_Strategy.md)
                                            ⬜  Not in scope until traction at Innovators+Early Adopters proves out
```

**The Q2 pilot is the structured outreach to Early Adopters via the IOTA community.** It is not "crossing the chasm" — that framing was premature in earlier drafts of this document. Crossing the chasm requires social proof from the Early Adopter cohort, which we do not yet have.

---

## 2. Perceived Attributes of the Innovation

Rogers identifies five attributes that determine adoption rate. Where each currently stands for wot.id:

### 2.1. Relative Advantage *(Is it better than what it replaces?)*

- **Today**: True data ownership (BIP-39 client-side keys, server cannot see plaintext sensitive data); portable identity; the world's first SSI platform with hybrid post-quantum encryption.
- **Today's gap**: Most non-crypto users do not perceive "the server cannot decrypt my data" as a benefit until the next breach makes the news. Until then, the relative-advantage story has to be told — observability (§2.5) is how you tell it.
- **Pilot framing**: The IOTA community Q2 pilot is the right audience to *test the story*, because it can absorb the crypto-native framing without the explainer overhead.

### 2.2. Compatibility *(How well does it fit existing values, experiences, needs?)*

- **Today**: OAuth login (Google / GitHub / Apple) lowers the entry friction to a familiar pattern. Auto-assigned wallet at signup means "no wallet to set up" — a positive (no friction) and a footnote (the user does need to back up their mnemonic to retain custody after browser data wipes).
- **Real friction observed in production**: Safari ITP wipes IndexedDB regularly, requiring the user to re-enter the 24-word mnemonic. Mitigated, not eliminated. See `docs/2026_Code_Work/26-05-06_Key_Regen_Incident.md` and `26-05-06_PR492_Deploy_Verified.md`.

### 2.3. Complexity *(How difficult is it to understand and use?)*

- **Today**: The OAuth-first onboarding hides DID, blockchain, and PTB complexity behind a familiar sign-in. The 24-word mnemonic step is the first "this is different" moment; it is currently presented as an early step in the flow.
- **Honest position**: There is no "Backend manages your keys; you can opt into self-custody later" path. wot.id is **self-custody from day one**, with a usable abstraction (auto-assigned wallet, gas sponsorship) on top. Earlier drafts of this document described a "Month 4: full sovereignty" milestone that contradicted the actual architecture and has been removed.

### 2.4. Trialability *(Can it be experimented with on a limited basis?)*

- **Q2 pilot answer**: Yes, by design. Every newly-created account is auto-funded with **1 IOTA from the gas-station wallet** (≈ 200 user-paid transactions of headroom), bounded by a hard cap of **1,000 trial accounts** (≈ €50 total budget envelope). See `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md` §3 for the full math.
- **Property**: A user can sign up, create a DID, get one attestation, see a real trust score, and transfer IOTA — all without ever buying IOTA themselves. After the trial cohort is funded, onboarding still works; users just bring their own gas.

### 2.5. Observability *(Are the results visible to others?)*

- **Today**: Trust scores are visible on the ME page. Attestations are publicly verifiable on the IOTA explorer (`https://explorer.rebased.iota.org/`). DIDs resolve within the ecosystem.
- **Gaps**: No social-proof artifacts that propagate naturally (no "I just got attested by X" share card; no shareable profile URL outside the wot.id frontend). This is a backlog item, not a Q2 blocker.

---

## 3. The Q2 Pilot — IOTA Community Open Beta

The strategic content here is short, because the operational content lives in `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md`. This section orients the strategy around the choice.

### 3.1. The audience choice

**Decided 2026-05-10**: the Q2 pilot anchor is the IOTA community (open beta). The previously-named Hochschule Stralsund / academic-pilot path is dropped from Q2 and not re-attempted this quarter (no conversation had started by May 10; the decision-by-default was already "open beta or nothing").

Rationale (per `26-05-10_2026_Pilot_Strategy.md` §2):

- **Reachable**: one Discord post + one tweet covers the candidate population.
- **Self-onboarding-capable**: tech-literate; "must not confuse a developer" is a much lower bar than "must hand-hold a non-developer."
- **No institutional contract surface**: no NDA, no GDPR-grade audit-log story, no procurement, no legal review per pilot.
- **Aligned criticism**: failures get reported as bugs ("the QR encoder strips a base64 padding char"), not as reputational damage.

### 3.2. What the audience is *not*

- **Not demographically representative**. Crypto-natives are not the median wot.id user. Friction observed in this pilot will need to be revalidated when a non-crypto cohort eventually shows up.
- **Not an Early Majority cohort**. This is targeted Early Adopter outreach. The Early Majority requires social proof that the Early Adopter cohort produces, which is still in front of us.

### 3.3. Success metric

5+ test users using wot.id independently by June 30, 2026. *5 is not a vanity number — it is "more than the developer's friends, less than what could be Sybil-attacked from a Discord post."* Definitionally: 5+ DIDs that have signed in twice on different days, with at least one attestation each, and the developer's DID is not in the attester chain.

---

## 4. The Operational Onboarding Flow (Q2)

The full design lives in `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md` §6. Brief shape, included here so this document is not solely an abstraction:

1. **OAuth signup** (existing — Google / GitHub / Apple).
2. **Mnemonic display + confirmation** (existing — `frontend/src/lib/crypto/mnemonic.ts`).
3. **DID display** with explorer link (existing).
4. **1 IOTA balance shown** (new — Backend funding handler called on first profile creation, gated by Sybil layers and a 1,000-account hard cap).
5. **First attestation** via existing QR cross-device flow or invite link (existing).
6. **Trust score** appears on the ME page after the attestation confirms (existing v0 aggregator).

Total target time for steps 1–4: under 10 minutes. Adding step 5–6 (which requires a second human) brings the realistic total to under 30 minutes for a pilot user with a willing peer.

**One open product decision** (the only one outstanding after May 10, per Q2 Plan Update 3):

| Option | Trade-off |
|---|---|
| **Immediate funding** (1 IOTA at signup) | Simplest UX. Faucet attack succeeds immediately on every fake-account success — capped at €50 by the 1,000-account hard cap. |
| **Deferred funding** (1 IOTA after first independent peer attestation) — *recommended* | Faucet attack incentive collapses to zero; Sybil ring of 1,000 self-attesting accounts gets 0 IOTA. UX cost: step 4 in the modal becomes "you'll receive 1 IOTA when someone attests to you." |
| **Hybrid** (0.1 IOTA immediate + 0.9 IOTA after first independent attestation) | Both. More code paths. |

Decision recorded in the pilot strategy doc.

---

## 5. Sybil and Abuse Defenses (Q2 Posture)

The full layered analysis is in `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md` §5. The strategic short version:

**The threat model is bounded.** With a hard cap of 1,000 funded accounts and 1 IOTA per account, the worst-case loss from a fully-successful faucet attack is ≤ €50 plus ≈ €8 of gas-station overhead. The defenses are designed to (a) raise per-account real-world cost above the per-account payoff and (b) cap aggregate loss at €50. They are **not** designed to make the attack impossible.

**What's in place** (reuse): OAuth-only signup, email normalization (`docs/2026_Code_Work/26-01-02_Email_Normalization.md`), account-linking duplicate detection (`26-01-02_Account_Linking.md`, `26-03-19_Account_Linking.md`), per-DID 24h rate limit on profile-creation gas.

**What gets added before pilot launch**: per-IP rate limit on the funding path, one-canonical-email-per-funded-account check, throwaway-email-domain blocklist, funded-account counter (the 1,000 cap enforcement).

**What is explicitly skipped**: phone/SMS verification, biometric proof-of-personhood, captcha, behavioral graph analysis, ML fraud detection. All of these are appropriate at scale; none are appropriate at €50 budget × 1,000 accounts. Q3+ if needed.

The long-horizon Sybil framework — `docs/2025_Code_Work/25-12-28_Fraud_Prevention_And_Threat_Model.md` and `25-12-11_Identity_Verification.md` — describes what wot.id will need at the 10,000+ user scale. It is **not** the Q2 frame.

---

## 6. Adopter Segmentation (Long Horizon, Past Q2)

The Rogers-curve framing remains useful as a long-horizon scaffold even though Q2 work is exclusively at the Innovators / Early Adopters end.

### 6.1. Innovators (2.5%) — Crypto-natives

**Where they engage**: GitHub (open source code), foundational docs (`01_Project_Overview_And_Principles.md`, `05_Move_Smart_Contracts.md`), IOTA developer channels. **Already engaged.** The codebase, the Move source, and this docs tree are the artifact.

### 6.2. Early Adopters (13.5%) — Web3-aware professionals, opinion leaders

**The Q2 pilot is the structured approach to this segment.** They do not need (and do not want) the technical-novelty pitch — they need a polished product that does something useful. The pilot's bet is that "verifiable on-chain attestations + portable DID + post-quantum encryption + €0 cost to try" is enough to hold their attention for a 30-minute first session.

### 6.3. The Chasm

The classical Threads case study, kept here for the Q3+ framing because the lesson is real:

- **Don't mistake initial sign-ups for crossing the chasm.** Threads hit 100M signups and failed to retain users because it lacked feature depth and a unique identity beyond "not Twitter."
- **Q2 implication**: 200 signups in the pilot is *not* a crossing-the-chasm signal. 5 users actively using wot.id over weeks is. The Q2 minimum-success metric is calibrated for the right thing.

### 6.4. Early Majority (34%) — Pragmatists

**Not a 2026 audience.** Pragmatists adopt only when an innovation is proven and provides practical benefits — meaning *credentials and use cases the Pragmatist's daily life depends on*, like a verifiable medical record, a renter-credit attestation, or a professional-license check that someone they trust accepts. None of these exist for wot.id today. **The Pragmatist conversation is post-pilot, post-traction-proof.**

The OAuth-first onboarding flow is *infrastructure for* this audience, not a strategy for *reaching* them. The strategy for reaching them is "credible attestations of practical things, by attesters the Pragmatist already trusts." That's a Q4+ conversation.

### 6.5. Late Majority and Laggards

Out of horizon. Adopt only after wot.id-style identity is the de facto standard. Mentioned for completeness; no work item.

---

## 7. Lessons from Adjacent Cases

### 7.1. Threads (2023)

Original lesson, still applies: feature completeness is non-negotiable. 100M signups is not adoption.

**Q2 application**: ship the onboarding flow as a complete loop (signup → DID → balance → attestation → trust score), not as a half-built welcome modal. The acceptance criterion in the pilot strategy doc enforces this.

### 7.2. Worldcoin (2023–2025)

Lesson not in the previous draft of this doc, added because it's adjacent: a proof-of-personhood mechanism that requires biometric capture has *real* friction and *real* trust burden on the issuer. wot.id explicitly does not go down this road for Q2 (`docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md` §5.3 — biometric proof-of-personhood is "vastly disproportionate for a €50 budget; runs against the SSI ethos"). Q3+ research scope only.

### 7.3. Bluesky / AT Protocol (2024–2026)

Lesson: a federated/portable identity primitive without a real product around it is a primitive without a market. Bluesky's adoption story is largely "Twitter alternative" not "decentralized identity," even though the second is the architectural truth. **For wot.id**: the product story has to be about the verifiable trust artifact (the attestation, the trust score) — not about the underlying decentralization, which is a means, not the end the user is buying.

---

## 8. What Earlier Drafts Got Wrong (corrected here)

For audit traceability — these claims appeared in earlier versions of this document and have been corrected:

| Earlier claim | Reality | Source |
|---|---|---|
| "Backend manages your keys" (Month 1 of progressive-decentralization timeline) | Backend has **never** stored user keys. BIP-39 client-side from PQC stack rollout in Dec 2025. | `docs/02_System_Architecture.md` §10.2; `frontend/src/lib/crypto/mnemonic.ts` |
| "Wallet Setup in Month 3 — connect personal wallet" | Personal wallet is auto-assigned at signup with exportable mnemonic. Users do not "connect a wallet" later. | `docs/02_System_Architecture.md` §2 |
| "Phase 2 Complete — governance operational" | Move primitives deployed; backend endpoints + UI deleted as mock-only on 2026-03-07; production governance deferred to Q3+. | `docs/2026_Code_Work/26-03-07_Dead_Endpoints_Audit.md`; `docs/10_Governance_And_Conflict_Resolution.md` §4.1 |
| "30%+ weekly active users" as a Phase 4 metric | Q2 success metric is **5+ users**. The percentage was calibrated to a phase wot.id has not yet entered. | Q2 plan series — `docs/2026_Code_Work/26-04-01_2026_Q2_Plan.md`, Updates 1–3 |
| "20%+ users set up personal wallets" as a Phase 5 metric | All users have wallets at signup. The metric is meaningless as written. | `docs/02_System_Architecture.md` §2 |
| Implicit Hochschule Stralsund / academic pilot framing | Dropped from Q2 on 2026-05-10. IOTA community open beta is the actual pilot. | `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md`; `docs/2026_Code_Work/26-05-10_2026_Q2_Plan_Update3.md` |
| Monthly progressive-decentralization timeline (Month 1: backend keys → Month 4: self-custody) | Architecture is self-custody from day 1. The timeline structurally cannot map to the system that ships. | `docs/02_System_Architecture.md` §10.2 |

This audit is recorded here rather than in the pilot strategy doc because it's properly part of *this* document's history.

---

## 9. Open Questions (Long Horizon)

These do not block Q2 work but should be answered before Q3+ planning:

1. **What is the first product story for the Pragmatist audience?** Likely candidates: medical records, professional credentials, renter/payment-history attestations. None exist as a workable product today.
2. **What partnerships unlock the Pragmatist trust transfer?** Credentialing institutions, professional bodies, regulators, employers. Q2 dropped Hochschule Stralsund; Q3 may surface a different path.
3. **When does v1 (client-side Bayesian aggregator) become a user-visible improvement?** Today's v0 flat-mean is good enough for 5–10 users. At what scale does it visibly fail? See `docs/2026_Code_Work/26-05-08_Inference_Compute.md` §7.
4. **When do Sybil defenses need to escalate beyond the §5 layers?** First post-pilot review.
5. **Does an external DID resolver integration become valuable, or is it Q3+ optional?** Depends on whether external systems start asking for it.

---

## 10. References

**Operational (this quarter)**:
- `docs/2026_Code_Work/26-05-10_2026_Pilot_Strategy.md` — operational source of truth for Q2 onboarding, funding, and Sybil defenses
- `docs/2026_Code_Work/26-05-10_2026_Q2_Plan_Update3.md` — current Q2 plan (May 10 → June 30)

**Architecture**:
- `docs/01_Project_Overview_And_Principles.md` — atomic-data-point model, principles
- `docs/02_System_Architecture.md` — system components, the "wot.id is interface, not owner" model, PQC architecture, personal-wallet-at-signup
- `docs/05_Move_Smart_Contracts.md` — current package (v11, May 23, 2026), modules, privacy levels (3-band since v9 May 8, 2026)
- `docs/06_P2P_Communication.md` — WebSocket relay + WebRTC (libp2p deleted May 2026)
- `docs/07_Trust_Architecture_And_Management.md` — attestation flow, trust score (v0)

**Diffusion theory (broader framework, modern evolutions, audit of this doc)**:
- `docs/2026_Code_Work/26-05-19_Diffusion_Strategy.md` — full Rogers framework + network theory + modern evolutions (Moore, Centola, Gladwell, Berger, Christensen, Christakis-Fowler, TAM, COM-B, Hook, startup canon) + audit of this doc (its §32)

**Decisions and history**:
- `docs/2026_Code_Work/26-05-17_SC_V10_Upgrade.md` — v10 publish (user-signed identity-creation entry wrapper)
- `docs/2026_Code_Work/26-05-08_SC_Upgrade.md` — v9 publish (privacy-level cleanup)
- `docs/2026_Code_Work/26-05-08_Inference_Compute.md` — v0/v1/v2 trust-compute plan
- `docs/2026_Code_Work/26-05-07_2026_Q2_Plan_Update2.md` — privacy-level decision, libp2p delete decision
- `docs/2026_Code_Work/26-05-03_2026_Q2_Plan_Update1.md` — original "5+ users by June 30" goal
- `docs/2026_Code_Work/26-03-07_Identity_Service.md` — Identity Service retirement
- `docs/2026_Code_Work/26-03-07_Dead_Endpoints_Audit.md` — governance UI deletion

**Long-horizon Sybil framework** (Q3+ scope):
- `docs/2025_Code_Work/25-12-28_Fraud_Prevention_And_Threat_Model.md`
- `docs/2025_Code_Work/25-12-11_Identity_Verification.md`

**External**:
- W3C DID Core v1.0: https://www.w3.org/TR/did-core/
- IOTA Identity SDK reference (`identity.rs`) — wot.id does **not** use this SDK; W3C DID generation is inlined in the Backend.

---

**Document status**: Active, calibrated to May 2026 architecture (smart-contract v10 May 17) and Q2 pilot decision (IOTA-community open beta, May 10). Scope is Rogers' five attributes + adopter-curve segmentation; broader diffusion theory is in `docs/2026_Code_Work/26-05-19_Diffusion_Strategy.md` (its §32 audits *this* doc and recommends a Tier-2 or Tier-3 expansion in a future session).
**Next revision trigger**: pilot launches and produces first-cohort observations; or Q2 retrospective in early July; or significant adopter-curve advancement (e.g., first Pragmatist conversation initiated); or a decision to elevate this doc to a broader-framework treatment (Tier 3 per the May-19 brainstorm).
