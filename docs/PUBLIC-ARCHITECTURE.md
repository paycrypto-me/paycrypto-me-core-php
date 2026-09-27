# PayCrypto.Me Public Architecture

> **Role:** authoritative entry point and authority map.\
> **Scope:** the **PayCrypto.Me Public Architecture**.\
> **Rule:** navigation only; this file must not become a catch-all
> substitute for canonicals.

# Canonical scope

The super-domain is **PayCrypto.Me Public Architecture**. It is
wallet-read-only and does not handle private keys, extended private
keys, seeds, mnemonics, signing authority, or custodial key material.

These are Public Architecture constraints, not permanent claims about
every possible PayCrypto.Me architecture.

# Functional topology

``` text
Consumer -> SDK -> Core -> Primitives
```

Supply Chain Assurance is first-class but orthogonal to this functional
chain.

Primitives may also be independently consumed:

``` text
External Project -> Primitives
```

External Project is outside the PayCrypto.Me topology; this does not
authorize Consumer bypasses.

# Authority map

## Public Architecture

`paycrypto-public-architecture-canonical-v1.0.md`

Owns scope, recognized domains, topology, inter-domain boundaries, SDK
change-containment role, Consumer/Customer terminology, independent
Primitives consumption, Assurance relationship, systemic properties,
cross-domain invariants, rejected/superseded directions, and
cross-domain opens.

## Primitives

Latest accepted
`paycrypto-primitives-canonical-architecture-reference-*`; reviewed
authority at this checkpoint: **v1.4**.

Owns Primitives internals: capabilities, composition/divergence,
definitions, OWN/COMPOSE/DELEGATE, BIP32/ECC/hashing/encoding
boundaries, implementation delegation/replaceability, verification
semantics, and Primitives open decisions.

Do not reconstruct Primitives from the old transversal document.

## Core

`paycrypto-core-canonical-v0.1.md` --- **pre-1.0 / incomplete domain
architecture**.

Preserves established Core decisions and specialist handoff: payment
identity/instructions, routes, Asset/Network/Rail/Representation,
PaymentExperience candidate, receiving sources/allocation,
claims/reconciliation, exact arithmetic, verification, and
Core/Primitives intent boundary.

## SDK

`paycrypto-sdk-canonical-v0.1.md` --- **pre-1.0 / incomplete domain
architecture**.

Preserves SDK boundary, change containment, Consumer/Core isolation, PHP
materialization, and open future cardinality.

## Consumer

`paycrypto-consumer-canonical-v0.1.md` --- **pre-1.0 / incomplete
generic architecture**.

Preserves Consumer role, Customer distinction, platform containment,
WooCommerce evidence, Guided/Advanced and multi-route evidence, and
future Customer extension direction.

## Supply Chain Assurance

First-class domain; Public Architecture boundary is established. Its
internal canonical belongs to its specialist and is intentionally not
fabricated here.

# Version vs decision status

`v0.x` means a domain is incomplete. It does **not** make accepted
decisions optional. Each canonical marks accepted/frozen vs
candidate/open/rejected knowledge.

# Existing implementation rule

Current WooCommerce code is requirement/flow evidence, not Public
Architecture, compatibility blueprint, or behavioral-preservation
contract.

# Historical transversal reference

`paycrypto-architecture-canonical-reference-v1.0.md` was a valuable
mixed checkpoint. Its legitimate knowledge has now been partitioned by
authority. It must not override newer canonicals or resurrect superseded
ideas such as old WooCommerce behavioral preservation, Primitive Watch
inside Primitives, or Customer as the architectural-domain term.

# Repository distribution and agent context rule

Every repository that belongs to the PayCrypto.Me Public Architecture
**SHOULD carry a synchronized copy of this `PUBLIC-ARCHITECTURE.md`** as
its architectural entry point.

The copies are not independent authorities. They are synchronized
projections of the same architecture map and must not diverge by
repository. A repository-specific change to this file must therefore be
reconciled with the authoritative version rather than creating a local
variant.

For human and AI-agent work, the default context-loading rule is:

``` text
Always load:
PUBLIC-ARCHITECTURE.md
        +
current domain canonical

Load on demand:
PayCrypto.Me Public Architecture Canonical
other domain canonicals
references / evidence
```

Other canonicals should be loaded when the task crosses, depends on,
evaluates, or may change their architectural boundaries. The complete
Public Architecture Canonical need not be injected into every ordinary
domain-local session merely because this navigation document is present.

The purpose of this rule is to preserve architectural awareness with a
small default context while keeping domain work anchored to the correct
source of truth.

This file remains a **navigation and authority map**, not an additional
domain canonical.

# Continuation rule

A future human/agent should: 1. start here to locate authority; 2. read
the Public Architecture Canonical for cross-domain rules; 3. read the
owning domain canonical before changing that domain; 4. preserve
epistemic status; 5. explicitly reconcile cross-domain changes; 6. never
depend on the deleted/historical conversation as architectural
authority.

This documentation set is intentionally designed so the originating
conversation can be retired without loss of relevant architectural
knowledge.
