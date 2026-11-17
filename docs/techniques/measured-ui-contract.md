---
doc_id: fc.technique.measured-ui-contract
doc_kind: pattern
status: draft
depends_on:
  - fc.spec-driven-development
relates_to:
  - fc.technique.batched-review-trust-primitive
  - fc.technique.verify-integration-mechanisms
concepts:
  - mock-first
  - measured-constraints
  - negative-control
  - independent-verification
---

# Measured UI Contract

When an agent changes a user interface, agree the limits as numbers before building. Measure the mock with a script, then measure the build with the same script, run it against the live version as a negative control, look at the screenshots, and have a second agent do its own pass. Each of these catches defects the others miss.

## Context

An agent can produce a plausible interface quickly, and the practitioner's attention is the scarce resource. Screenshots read as "looks fine" at a glance. A phone layout fails in small, physical ways: a 29 px button, a panel over the thing being read, a label cut mid-word, a control under another control. None of these show in a diff, and a passing unit suite says nothing about them.

## Problem

How do you get an interface change from an approved picture to a releasable build without the practitioner having to find its defects by hand?

## Forces

- **Pictures persuade.** A mock or a screenshot makes a decision easy to take and a defect easy to miss.
- **Checks only test what they name.** A passing script can't prove that a control it doesn't list is fine.
- **Agents agree with themselves.** The agent that built the change reads its own screenshots with the same blind spots it built with.
- **Shared machines.** Builds and browser runs from several sessions compete for one laptop.

## Pattern

1. **Write the limits down as numbers in the brief,** for example a top row of at most 56 px, 44 × 44 px targets with 8 px between them, text of at least 12 px, nothing floating over an open sheet, and nothing cut off.
2. **Mock first, measured.** Draw the change over real screenshots, measure every frame with a script, and show the practitioner one page: the frames, two or three choices each with a recommendation, and the measured table. Build nothing until they answer.
3. **Build, then measure with the same limits.** Add behaviour checks driven by real touch taps, including two things opened in turn. Report counts by kind (behaviour, limits, page health, setup), not one total.
4. **Negative control.** Run the same checks on the live version. They must fail there. A check added for a reported defect must fail before its fix.
5. **Look.** Read the screenshots for what draws the eye and for anything the checks don't name.
6. **Independent pass.** Another agent, with its own script and no access to yours, does a touch pass. Each finding gets a check, and the check fails before the fix.
7. **One heavy job at a time.** Gate each build or browser part on the machine's load, with before and after notices to the other sessions.

## Evidence

**Trace: Salish Sea Atlas phone chrome (U1 + U3), 2026-11-17.** Three agent sessions were involved: a builder, a plan owner that also did the touch passes, and a third that shared the machine and co-wrote the research.

- **The mock.** Fourteen frames, all measured. The practitioner answered three choices "as you recommend". That took one message.
- **The build.** It took nine local commits in about two hours, from 09:40 to 11:29. The final candidate passed 663 phone checks (214 behaviour, 371 measured limits, 74 page health, 4 setup) at 390 × 664 and 360 × 640, dark and light, plus 320 × 568, 844 × 390 and a 1280 px wide layout. Its unit tests passed 193/193 and its gateway suite 78/0.
- **The negative control.** The same script on the live version failed 49 of 67 behaviour checks and 96 of 119 limit checks.
- **What each layer caught:**
  - **The measured limits, first run:** the credits touching another button, controls left visible under an open menu, a mode switch cut off by an older CSS rule, and 11.5 px text in the merged sheet.
  - **Looking at the screenshots:** a credits button that a dark-theme colour inversion turned into a blank white disc, a second search field the mock had promised to remove, and the same state named twice on one screen. All three passed the measured limits.
  - **The second agent's touch passes:** a menu whose last row needed a scroll at 360 × 640, a note covering the record being read, and a button sitting on top of another because a removed bottom bar moved it. The last one was in no selector list, so the checks couldn't see it.
  - **The checks added for those findings:** they caught one more bug, a search query that hid the state its own pick depended on. That bug was found by a check written for a different finding.
  - **A shared measuring script, written from this work and run last:** an 11.5 px dropdown inside the new sheet, which makes iOS zoom the page when it's tapped. The site's own script didn't measure inputs.

## Generalization

The same shape fits any change whose defects are visual or physical: email templates, slide decks, printed handouts, dashboards. Say the limits as numbers, measure the picture and the product with one script, prove the script fails on the old version, then add the human look and a second agent. The script keeps the numbers honest, the look catches what nobody listed, and the second agent catches what the builder can't see.

## See Also

- `skills/phone-ui/` in darren-workflow: the rules, the limits, a reusable measuring script with per-site selector configs, and the research behind them (two independent notes and a synthesis, 2026-11-17).

## Open Questions

- How should the selector list be kept complete? A control the list doesn't name can't fail a check (the moved button above). One option is to measure every element with a click handler or a role, and keep exceptions as an allow-list.
- Which of text resize, contrast, Back behaviour and motion are worth a check on every page, and which only per release?
