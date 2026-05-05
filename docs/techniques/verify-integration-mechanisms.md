---
doc_id: fc.technique.verify-integration-mechanisms
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
relates_to:
  - fc.technique.specific-observation-to-specific-claim
  - fc.technique.check-tools-before-claiming-inability
concepts:
  - verification-discipline
  - cross-service-pattern-matching
  - provisional-marking
---

# Verify Integration Mechanisms Before Recommending

Before recommending a specific named integration mechanism — an email-bot invite address, a webhook endpoint, a settings toggle path, a product feature with a specific name — verify the mechanism actually exists. Do not pattern-match across services. Different services have different shapes.

## Context

A practitioner is composing a recommendation that involves a specific external mechanism: "invite `assistant@x.ai` to the calendar event," "configure the webhook at Settings → Integrations → Y," "use the `X-Foo-Header` to authenticate," "enable Z in the Plan tab." The recommendation is consumed by the operator (or another agent) who will act on it directly — they will paste the email, click the path, set the header. If the named mechanism does not exist, the operator's time is wasted and trust in future recommendations degrades.

## Problem

How do you keep recommendations trustworthy when the AI is generating them from a mix of training data, recently-read docs, and pattern-matching across services it has seen?

## Forces

- **Cross-service pattern-matching is fast but lossy.** "Read.ai has an invitable bot, so Otter probably does too" is the kind of inference the model produces routinely; it is correct often enough to feel reliable but wrong often enough to break operator trust on the wrong day.
- **Verification cost varies.** Sometimes verification is one library-source read or one doc fetch; sometimes it requires probing the user's own account; sometimes it is gated behind a Cloudflare-protected help portal.
- **Provisional vs. confident framing.** The fix is not always "verify before stating" — sometimes it is "state with explicit provisional framing." A confident assertion of a wrong mechanism is worse than a provisional assertion of an unverified one.
- **Multi-service recommendations compound the risk.** When a single message recommends mechanisms across three services, the temptation is to verify one and assume the rest. Each service needs its own verification.

## Pattern

**Verification options, in order of cost:**

1. **Read the library source** if there is a wrapper (e.g., a project-local `service.py` that lists actual API methods).
2. **Hit the official docs** via web fetch. Note: many help-portal URLs are bot-protected and may not respond cleanly to programmatic fetch — be prepared to fall back.
3. **Probe the API directly** with the user's auth, if available.
4. **Check the user's account settings UI** if they are at the keyboard.
5. **Ask the user** if none of the above resolve cheaply.

**The verify-then-state contract.** A specific named mechanism in the recommendation is a contract with the operator that the mechanism is real. If verification did not happen, the contract is violated — even if the mechanism turns out to be real.

**The provisional-marking fallback.** When verification is too expensive in the moment, mark the recommendation explicitly:

> *"I think Otter has an invitable assistant email — please confirm in your Otter account settings before relying on this. If it does not exist, fall back to native calendar integration."*

The provisional marker is honest and lets the operator decide whether to verify themselves. A confident assertion robs them of that choice.

**Per-service verification.** When recommending mechanisms across multiple services in one message, run verification for *each* service. Do not verify one and assume the others. The cost is per-service; the failure mode is also per-service.

## Anti-pattern: cross-service pattern-matching

The temptation is to reason: "Service A has feature F; service B is similar; therefore B has F too." This inference is unreliable because:

- Different services have different go-to-market shapes (consumer vs. enterprise, free vs. paid).
- Different services have different integration philosophies (email-bot, OAuth, calendar-native, webhook-only).
- Different services have different feature gates (some features are paid-tier only, region-locked, or behind beta flags).
- The "similar" judgment is itself a pattern-match — it can be wrong about which features are actually parallel.

The correct move when tempted to pattern-match: **mark the recommendation provisional and verify**, or stop short and ask the operator.

## Evidence

**Trace 1 — Otter integration recommendation, IndigenomicsAI session 2026-05-05.** The iterator confidently said: *"invite Otter's assistant email (e.g., `assistant@otter.ai`)."* The operator pushed back: *"I'm not seeing the assistant email, are you sure that exists?"* The iterator was not — the recommendation was a pattern-match from other meeting bots that *do* have invitable emails (Read.ai, Fathom, Granola). For Otter, the actual mechanism is calendar-integration-only; there is no separate email-invite flow. The pattern-match could have wasted operator time clicking through Otter settings looking for a feature that does not exist.

Source memory rule: `feedback_verify_integration_mechanisms_before_recommending.md` in the IndigenomicsAI project memory, discovered 2026-05-05.

## Generalization

The pattern applies to any "[do this specific thing]" recommendation: shell commands with specific flags, API endpoints with specific paths, library functions with specific signatures, configuration keys with specific names. The shape is verify-then-state, not state-then-hope.

This is the forward-looking complement to [`specific-observation-to-specific-claim`](./specific-observation-to-specific-claim.md), which governs backward-looking PASS markers. Together they constitute a verification-discipline pair: do not assert past success without specific observation, and do not propose future actions involving specific named mechanisms without verifying the mechanisms exist.

## See Also

- [Specific Observation to Specific Claim](./specific-observation-to-specific-claim.md) — companion discipline for backward-looking claims.
- [Check Tools Before Claiming Inability](./check-tools-before-claiming-inability.md) — adjacent discipline; both refuse default-pattern-matching as a substitute for verification.
- [Spec-Driven Development](../foundations/spec-driven-development.md) — recommendations that flow into a spec's "Constraints" or "Assumptions" sections benefit from this verification discipline at authoring time.

## Open Questions

- **Provisional-marking calibration.** When is a provisional mark sufficient versus when must verification happen before the recommendation ships? The current rule defaults to "verify when cheap, mark when not"; the threshold for "cheap" is fuzzy.
- **Verification cache.** Once verified, a mechanism's status is stable for a session but may change across sessions (services deprecate features, change paths, rename headers). A short-lived verification cache keyed to time-of-fetch would help, but its invalidation policy is unclear.
- **Adversarial-source risk.** Reading official docs verifies *as documented*; the docs themselves can be wrong or out of date. The strongest verification is direct probe; the weakest is "I have seen this in training data." Recommendations should weight verification source explicitly.
