# PayCrypto.Me Core PHP

**A platform-independent payment domain for cryptocurrency payment acceptance.**

PayCrypto.Me Core models how a payment is represented, instructed, routed,
allocated, correlated, and verified.

Its concepts, invariants, and decisions remain meaningful regardless of the
external platform, payment UI, or public crypto/protocol implementation.

> **Core owns payment behavior.**

---

## Why this project exists

Cryptocurrency payment acceptance is easy to reduce to an address and a
transaction ID. That reduction breaks down when a payment has multiple routes,
a fixed or derived receiving address, a hosted invoice, reused account-based
addresses, more than one transfer, or ambiguous payment evidence.

Core takes a different path:

```text
Payment
  ↓
enabled routes
  ↓
payment instruction
  ↓
receiving source, where needed
  ↓
transaction evidence
  ↓
trusted acceptance
```

Core makes payment behavior explicit enough to evolve, verify, and apply
consistently without letting an external platform, an address-derivation
mechanism, or a library's types define the domain.

---

## What makes Core different?

### Payment-first

A payment is the domain center. Every payment has a mandatory internal
`PaymentId`; a `PaymentReference` may provide external correlation, but no
external identifier becomes Core identity.

One payment may have zero or more transactions or other evidence. A test
transfer followed by a remaining transfer is still evidence for one payment,
not an architectural exception.

### Instruction-driven

`PaymentInstruction` answers how a payment should be paid. The known
behavioral families are address payments—fixed or derived—and
hosted/invoice-style payments such as Lightning.

The top-level question is not merely which address to derive. Lightning, for
example, is an invoice/hosted-provider-style payment and must not be forced
into an address-derivation model. The final type hierarchy and API remain open.

### Route-aware

Asset, Network, Rail, and Representation are distinct concepts. A
`PaymentRoute` composes the relevant dimensions for a concrete way to pay.
Its exact fields remain open.

Merchants configure enabled routes. Payers choose among them. A recommendation
can assist that choice, but cannot make it or impose one network on every
asset.

### Allocation only when needed

A fixed receiving address is valid when derivation or allocation is unnecessary.
Where a public receiving source requires a per-payment address, Core expresses
that intent through an admitted policy, source, and path.

`PaymentAllocation` exists only when a scarce or exclusive receiving resource
must be reserved. Receiving-source pools may support rotation, reservation,
expiry, overflow, and safe reuse. Correct allocation must be possible with
ordinary database transactions, constraints, and locking.

### Evidence before acceptance

Correlation and economic acceptance are separate decisions. A payer-supplied
transaction ID is an untrusted `TransactionClaim`; it does not prove ownership
or payment intent.

Verification establishes the trusted binding between evidence and a payment.
Insufficient or ambiguous evidence fails closed and may require review.

Economic and protocol arithmetic uses atomic units or another exact
representation, never binary floating point.

Account-based networks can reuse addresses and may lack a memo or tag. Decimal
amount anchors can help reconciliation in limited cases, but never establish
identity. Ambiguity must lead to review, never silently bind evidence to the
wrong payment.

---

## Current architectural direction

```text
Payment
  ↓
enabled PaymentRoutes
  ↓
PaymentInstruction
  ↓
fixed address | derived address | hosted / invoice payment
  ↓
transaction evidence
  ↓
verification and acceptance
```

Receiving-source allocation participates only when a route and its receiving
source require reservation. It is not universal to every payment.

---

## OWN / COMPOSE / DELEGATE

A simple rule guides Core.

**We own** payment identity, instructions, routes, receiving-source and
allocation behavior, exact economic semantics, correlation, and verification
rules.

**We compose** those domain concepts into a payment flow appropriate to its
enabled route and payment instruction.

**We delegate** admitted low-level public crypto/protocol work to a capability
that owns that composition.

For example, Core can request a public receiving address for an admitted
policy, source, and path. It does not construct BIP32 derivation,
elliptic-curve operations, hashing, Base58/Bech32 encodings, or
library-specific types.

> **Core does not make low-level public crypto/protocol mechanics part of the payment domain.**

---

## PayCrypto.Me Public Architecture

Before reading or changing this repository's architecture, start with
[PayCrypto.Me Public Architecture](./docs/PUBLIC-ARCHITECTURE.md). It is the
shared entry point and authority map for domain boundaries.

Then read the [Core canonical architecture](./docs/paycrypto-core-canonical-v0.1.md).
The public architecture document provides context and navigation; the Core
canonical remains the authority for Core's decisions and evolution.

For human and AI-agent work, the default context is:

```text
PUBLIC-ARCHITECTURE.md
        +
current Core canonical
```

Load other canonicals and evidence only when the work crosses or may affect
their architectural boundaries.

---

## Architecture is part of the project

The canonical architecture explains why Core exists, which decisions are
accepted, what remains open, what has been rejected, and how the domain may
legitimately evolve.

Before introducing a payment abstraction:

```text
What payment behavior requires it?
        ↓
Does an existing domain concept express that behavior?
        ├── yes → compose it
        └── no
             ↓
Where does the behavior actually diverge?
             ↓
Is the proposed abstraction supported by evidence?
        ├── yes → justify it
        └── no  → keep it open
```

The canonical architecture is a contribution contract, not background reading.

---

## Project status

Core has an accepted pre-1.0 architectural baseline. Its domain boundary and
accepted decisions are established; its internal components will materialize
from the payment model and the candidates preserved for specialist review.

For the current source of truth, read the
[canonical architecture](./docs/paycrypto-core-canonical-v0.1.md).

---

## Contributing

Contributions should preserve the payment-first model.

Before proposing a domain abstraction, provider contract, source-selection
policy, or persistence design, identify:

1. the concrete payment behavior;
2. the existing domain concepts that can express it;
3. the point where behavior actually diverges;
4. the invariants that must remain true;
5. why composition cannot express it, when proposing a new abstraction; and
6. the evidence that validates the behavior.

A platform name, chain name, transaction ID, or library API is not sufficient
architectural evidence by itself.

Architectural changes should update the canonical documentation rather than
allowing implementation drift to redefine the payment domain silently.

---

## Security

Core operates in PayCrypto.Me's wallet-read-only public architecture. It never
requires private keys, extended private keys, seeds, mnemonics, signing
authority, or custodial key material.

Value-bearing payment workflows require careful review of the supported
behavior, evidence model, allocation policy, and reconciliation rules for the
specific use case.

---

## License

PayCrypto.Me Core PHP is released under the [MIT License](./LICENSE).

---

## The guiding idea

> **The goal is not to predict every payment route the project will support. The goal is to make the next legitimate payment behavior costly only in proportion to what is genuinely new about it.**

PayCrypto.Me Core exists as a coherent payment domain in its own right.

**Model it. Verify it. Build on it.**
