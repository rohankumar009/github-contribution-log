# github-contribution-log
My Open Source Contribution Logs

# Contribution 1: Splitting existing transaction causes UI glitches as splits disappear and reappear

**Contribution Number:** 1
**Student:** Rohan Kumar
**Issue:** https://github.com/actualbudget/actual/issues/5217
**Status:** Phase I Complete

---

## Why I Chose This Issue

This issue caught my attention because it's a concrete, reproducible UI bug with clear steps and even a screen recording from the reporter. The scope is well-defined — it's isolated to the transaction splitting flow — which makes it realistic to fix within a few weeks.

It's also labeled `good first issue` and `help wanted` by the maintainers, signaling that it's approachable for new contributors. Actual Budget is a well-maintained React + TypeScript project, which aligns with my skills, and I'm hoping to get hands-on experience tracing a UI state bug through a real production codebase.

---

## Understanding the Issue

### Problem Description
When a user converts an existing transaction into a split transaction, the UI glitches — the split rows flash, disappear, and reappear unexpectedly. After finishing, a leftover $0.00 split entry remains. This does not happen when creating a split transaction from scratch.

### Expected Behavior
Splitting an existing transaction should behave the same as creating a new split transaction — no flickering, no phantom rows, no leftover $0.00 entry.

### Current Behavior
After entering the first split amount and pressing Enter, the split rows visually disappear and reappear (glitch). Once done, a $0.00 split transaction is left behind.

### Affected Components
- Transaction editing / split transaction flow
- Likely the UI state management around transaction form updates
- React components handling split row rendering

---

## Reproduction Process

### Environment Setup
*To be completed during Phase II.*

### Steps to Reproduce
1. Create a transaction as a non-split transaction
2. Edit the transaction's category to convert it into a split transaction
3. Enter the first split amount and press Enter
4. Observe: UI glitches as split rows flash/disappear, and a $0.00 split remains

> Note: Reproducible on the live demo at https://demo.actualbudget.org — no local setup required to confirm the bug.

### Reproduction Evidence
- **Commit showing reproduction:** *To be added*
- **Screenshots/logs:** See screen recording in the original issue
- **My findings:** *To be added during Phase II*

---

## Solution Approach

### Analysis
*To be completed during Phase II after tracing the splitting logic in the codebase.*

### Proposed Solution
*To be determined.*

### Implementation Plan

**Understand:** When an existing transaction is converted to a split, something in the state update cycle causes the UI to re-render incorrectly, and a $0.00 row is left behind.

**Match:** *To be determined — will look for similar state-handling patterns in the transaction form components.*

**Plan:**
1. Reproduce locally and identify the React component(s) responsible for rendering split rows
2. Trace what triggers the re-render glitch (likely a state update ordering issue)
3. Fix the root cause and clean up the $0.00 split row logic
4. Update or add tests

**Implement:** *Links to be added as work progresses*

**Review:** Follow Actual Budget's CONTRIBUTING.md guidelines

**Evaluate:** Manually test the fix against the reproduction steps; confirm no regression on new split transactions

---

## Testing Strategy

### Unit Tests
- [ ] Splitting an existing transaction does not produce a $0.00 split entry
- [ ] Split rows render correctly without flickering after conversion
- [ ] New split transactions (from scratch) are unaffected

### Integration Tests
- [ ] Full split flow on an existing transaction behaves the same as on a new transaction

### Manual Testing
*To be completed during Phase II.*

---

## Implementation Notes

*To be filled in weekly as work progresses.*

### Code Changes
- **Files modified:** *TBD*
- **Key commits:** *TBD*
- **Approach decisions:** *TBD*

---

## Pull Request

**PR Link:** *Not yet submitted*
**PR Description:** *TBD*
**Maintainer Feedback:** *TBD*
**Status:** Not started

---

## Learnings & Reflections

*To be completed at the end of the contribution.*

---

## Resources Used
- [Issue #5217](https://github.com/actualbudget/actual/issues/5217)
- [Actual Budget contributing guide](https://github.com/actualbudget/actual/blob/master/CONTRIBUTING.md)
- [Actual Budget demo instance](https://demo.actualbudget.org)
