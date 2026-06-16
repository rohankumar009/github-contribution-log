# github-contribution-log
My Open Source Contribution Logs

# Contribution 1: Splitting existing transaction causes UI glitches as splits disappear and reappear

**Contribution Number:** 1
**Student:** Rohan Kumar
**Issue:** https://github.com/actualbudget/actual/issues/5217
**Status:** Phase II Complete

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
Reproduced on the live demo instance at https://demo.actualbudget.org — no local setup required.

### Steps to Reproduce
1. Go to https://demo.actualbudget.org
2. Navigate to the Checking account in the left sidebar
3. Click on any existing transaction to expand it
4. Click the **Category** field and select **"Split transaction"** from the dropdown
5. Enter an amount in the first split row and press **Enter**
6. Observe: split rows flash, disappear, and reappear (UI glitch)
7. After distributing all amounts, observe: a leftover **$0.00** split entry remains at the bottom

### Reproduction Evidence
- **Branch:** https://github.com/rohankumar009/actual/tree/fix/split-transaction-glitch
- **Screenshots/logs:** See screen recording in the original issue — bug confirmed reproduced on demo instance
- **My findings:** The $0.00 phantom split entry consistently appears after converting an existing transaction to a split. The glitch does not occur when creating a split transaction from scratch, indicating the bug is specific to the conversion flow.

---

## Solution Approach

### Analysis
*To be completed during Phase III after tracing the splitting logic in the codebase.*

### Proposed Solution
*To be determined.*

### Implementation Plan

**Understand:** When an existing transaction is converted to a split, something in the state update cycle causes the UI to re-render incorrectly, and a $0.00 row is left behind.

**Match:** Will look for how split transactions are initialized from scratch vs. converted from existing transactions — the difference in those two code paths is likely where the bug lives.

**Plan:**
1. Find the React component(s) responsible for rendering split rows (likely in `packages/desktop-client/src/components/transactions/`)
2. Compare the code path for creating a new split vs. converting an existing transaction
3. Identify what causes the extra $0.00 row to be inserted
4. Fix the state update ordering to prevent the phantom row and the re-render glitch
5. Update or add tests to cover this case

**Implement:** https://github.com/rohankumar009/actual/tree/fix/split-transaction-glitch

**Review:** Follow Actual Budget's CONTRIBUTING.md guidelines

**Evaluate:** Manually test the fix against the reproduction steps above; confirm no regression on new split transactions

---

## Testing Strategy

### Unit Tests
- [ ] Splitting an existing transaction does not produce a $0.00 split entry
- [ ] Split rows render correctly without flickering after conversion
- [ ] New split transactions (from scratch) are unaffected

### Integration Tests
- [ ] Full split flow on an existing transaction behaves the same as on a new transaction

### Manual Testing
- Confirmed bug reproduction on https://demo.actualbudget.org — $0.00 entry appears after splitting an existing transaction

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
