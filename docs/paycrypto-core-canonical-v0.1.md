# PayCrypto.Me Core --- Canonical Architecture Reference

## Pre-1.0 Baseline v0.1

> **Role:** canonical pre-1.0 handoff for Core inside PayCrypto.Me
> Public Architecture.\
> **Status:** incomplete domain architecture; accepted decisions remain
> authoritative.\
> **Purpose:** preserve legitimate Core knowledge so a specialist can
> continue without the originating conversation.

> **Scope boundary --- read this before interpreting references to other
> domains.** Core defines the payment domain. Public Architecture,
> Consumers, SDK, Primitives, external platforms, and external libraries are
> referenced here only when their relationship with Core establishes a Core
> boundary, dependency direction, constraint, requirement provenance, or
> responsibility. Their internal architecture, implementation model,
> abstractions, APIs, domain model, orchestration, lifecycle, and decisions are
> outside this canonical's scope.
>
> Contextual references to surrounding domains are intentionally shallow. This
> canonical defines Core; it does not define the architecture of neighboring
> domains.

# 0. Epistemic status

`v0.x` means **incomplete**, not "non-authoritative". Labels used here:
**FROZEN**, **ACCEPTED**, **CANDIDATE**, **OPEN**, **DEFERRED**,
**REJECTED**.

# 1. Reason for existence

**ACCEPTED:** Core owns payment domain/application behavior
independently of concrete Consumer platforms, SDK presentation concerns,
low-level cryptographic/protocol mechanics, and external crypto
libraries.

Core answers how a payment is represented, routed, instructed,
allocated, correlated, and verified. It delegates admitted low-level
capabilities to Primitives.

Core is inside the wallet-read-only Public Architecture and must not
require secret/signing wallet material. Existing WooCommerce code is
evidence, not Core authority.

# 2. Payment identity and evidence

**ACCEPTED:** `PaymentId` is mandatory internal identity.

**ACCEPTED:** `PaymentReference` is optional external correlation;
external/platform IDs do not become Core identity.

**ACCEPTED:** one Payment may have zero-to-many transactions/evidence.
One Payment is not constrained to one blockchain transaction; a payer
may send a test transfer and then the remainder.

# 3. PaymentInstruction

**ACCEPTED:** `PaymentInstruction` is the current universal output
direction for **how a Payment should be paid**. The top-level question
is not "which address should I derive?"

Known behavioral families, with final type hierarchy still
specialist-owned:

``` text
PaymentInstruction
+-- Address Payment
|   +-- Fixed Address
|   `-- Derived Address
`-- Hosted / Invoice-style Payment
    `-- Lightning
```

Known names include `AddressPaymentInstruction` and
`LightningPaymentInstruction`; exact names/hierarchy are not frozen.

**ACCEPTED:** Lightning must not be forced into address derivation
merely because it is associated with Bitcoin. Its observed behavior is
invoice/hosted-provider-like.

**CANDIDATE/STRONG:** explored creation shape:

``` text
CreatePaymentInstruction
        -> PaymentInstructionCreator
             -> Address behavior
             -> Hosted/Invoice behavior
```

`CreatePaymentInstruction` was preferred after
Factory/Orchestrator/Generator/Router/Switcher alternatives. Preserve
the reason for separation; final API remains specialist-owned.

# 4. Asset / Network / Rail / Representation

**ACCEPTED:** Asset, Network, and Rail are distinct concepts.

``` text
USDC
+-- Ethereum representation
+-- Base representation
`-- Solana representation
```

**ACCEPTED DIRECTION:** `AssetRepresentation` (or final equivalent) owns
representation-specific facts such as decimals and token/contract
identity.

**ACCEPTED:** `PaymentRoute` is a concrete composition of relevant
dimensions, conceptually:

``` text
Asset + Network + Rail + Representation
```

Exact fields remain open.

Merchant enables/configures routes; payer may choose among enabled
routes. Recommendation is separate from decision and may differ per
asset.

# 5. PaymentExperience

**CANDIDATE --- PRESERVE:** `PaymentExperience` emerged as an aggregator
for multiple payment-method/route possibilities, e.g. a stablecoin
experience exposing multiple enabled networks.

Its lifecycle, aggregate status, API, persistence, and relationship to
`PaymentInstruction` remain OPEN. Specialist must consolidate or
explicitly supersede it rather than lose it.

# 6. Address payment

**ACCEPTED BEHAVIOR:** fixed receiving address is valid where no
derivation/allocation is needed.

**ACCEPTED BEHAVIOR:** derived public address is valid where a public
wallet policy/source requires per-payment derivation. Core expresses
intent and delegates low-level public derivation to Primitives.

Core must not assemble BIP32, ECC, hashing, Base58/Bech32, or
library-specific types.

**CANDIDATE/STRONG:** `WalletPolicy` is the Core-side expression of how
a public receiving source should be interpreted for derivation.
Descriptors may later produce a WalletPolicy; they are not required now.

# 7. Allocation and ReceivingSourcePool

**ACCEPTED:** `PaymentAllocation` exists only when a scarce/exclusive
receiving resource must be reserved. It is not universal.

**ACCEPTED DIRECTION:** `ReceivingSource`/`ReceivingSourcePool` support
multiple sources, rotation/high volume, reservation, overflow,
expiration, and deallocation where needed. Multiple pools may exist.

**CANDIDATE:** source selection should be policy-driven;
`OverflowSourceSelector` was illustrative, not frozen. Round-robin is
not universal.

**ACCEPTED REQUIREMENT:** reserved resources have a payment window;
expiry must permit safe deallocation/reuse according to
policy/reconciliation rules.

**ACCEPTED REQUIREMENT:** concurrency must be correct without requiring
Redis merely for a normal single-database deployment. DB
transactions/constraints/row locking such as `SELECT ... FOR UPDATE` are
known suitable techniques; final persistence design is open.

# 8. Stablecoin/account-based correlation

Account-based networks often reuse addresses. Stablecoins on networks
without memo/tag create correlation problems.

**CANDIDATE WITH LIMITS:** decimal amount anchors may aid
reconciliation, e.g. `100.00001` vs `100.00002`. They are not
cryptographic identity and are scoped to cases lacking stronger
per-payment identifiers.

**ACCEPTED REQUIREMENT:** rounding/tolerance can make correlation
ambiguous; ambiguity must fail toward review rather than silently bind
the wrong Payment.

**ACCEPTED:** a customer-supplied txid is an untrusted
`TransactionClaim`. A txid alone is not proof that the claimant
owns/intended it for a Payment. Verification must establish trusted
binding.

Preserved attack case: attacker B copies the txid legitimately paying A
and claims it for B. B must not become paid merely because the txid
exists.

If no claim is supplied and automatic correlation cannot safely resolve
the payment, manual review may be required. Final reconciliation
workflow is OPEN.

# 9. Arithmetic, acceptance, multiple transactions

**ACCEPTED:** economic/protocol arithmetic is exact; use atomic units or
equivalent exact representation, not binary floating point.

**ACCEPTED:** correlation is separate from economic acceptance.

**ACCEPTED:** verification fails closed when evidence is
insufficient/ambiguous.

**ACCEPTED:** one Payment may be satisfied through multiple transfers.
Partial/over/underpayment and expiry semantics remain OPEN.

# 10. Merchant/payer choice

**ACCEPTED:** merchant configures/enables routes; payer chooses among
enabled routes. System may recommend, but recommendation is not
decision. No "apply one recommended network to all assets" invariant
exists.

# 11. Core / Primitives contract

**FROZEN:** Core expresses intent; Primitives satisfies admitted
low-level capability.

``` text
Core: derive a public receiving address for this admitted policy/source/path
Primitives: perform required public crypto/protocol composition
```

Core does not know selected crypto backends. Primitives does not know
Payment/Order/WooCommerce semantics.

# 12. Architecture principles preserved for specialist review

**ACCEPTED:** composition over inheritance where behavior composes;
evidence-driven abstractions; specialize at behavioral divergence rather
than coin label; do not conflate data/definitions with behavior.

**CANDIDATE:** Template Method and Bridge-like separation were
considered where they accurately describe real behavior. Pattern names
are never requirements.

# 13. Rejected

``` text
AddressDerivationEngine as top-level Core business abstraction
Core switching on coin/network names to assemble crypto
Core depending on crypto implementation libraries
Lightning as merely another derived address
PaymentAllocation for every Payment
one universal rotation policy
Redis required for basic allocation correctness
txid as sufficient proof
decimal anchors as strong identity
WooCommerce vocabulary as Core domain vocabulary
current plugin behavior as Core authority
```

# 14. Open specialist agenda

The specialist must mature: - Payment aggregate/lifecycle; - final
PaymentInstruction model/names; - Payment / PaymentInstruction /
PaymentExperience relationship; - provider contracts and creation
orchestration; - final PaymentRoute and
Asset/Network/Rail/Representation models; - WalletPolicy; -
ReceivingSource/allocation state machine and policies; -
persistence/concurrency; - reconciliation/evidence; - stablecoin
correlation/tolerance; - partial/over/underpayment and expiry; -
error/result taxonomy; - event model if justified; - hosted/invoice
provider boundary; - recommendation policy; - public Core API consumed
by SDK.

Do not fill gaps from framework conventions or speculative future needs.

# 15. Handoff

A Core specialist starts here, not from zero. Preserve accepted/frozen
decisions, validate candidates, resolve opens from evidence, explicitly
supersede changes, and reconcile the Public Architecture Canonical if a
Core decision changes a cross-domain boundary.
