# github-contribution-log
My Open Source Contribution Logs

# Contribution 1: Splitting existing transaction causes UI glitches as splits disappear and reappear

**Contribution Number:** 1
**Student:** Rohan Kumar
**Issue:** https://github.com/actualbudget/actual/issues/5217
**Status:** Phase III Complete

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
- `packages/desktop-client/src/components/transactions/TransactionsTable.jsx` — main transaction table and split row rendering
- `packages/desktop-client/src/components/accounts/Account.tsx` — manages split open/close state via `useLayoutEffect`
- `packages/loot-core/src/shared/transactions.ts` — core split transaction logic (parent/child relationships, `is_parent`, `is_child` flags)

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
The bug likely lives in how the split conversion flow initializes child rows. When a new split is created from scratch, the rows are initialized cleanly. When an existing transaction is converted, the state update that adds child rows may be firing an extra render cycle — likely because the parent transaction data is being updated at the same time the child rows are being generated. This causes the UI flicker. The $0.00 phantom row suggests an extra empty child entry is being inserted during initialization that is never cleaned up.

Based on research into the codebase, split transactions use `is_parent` and `is_child` flags with a `sort_order` to track parent-child relationships. The conversion path likely triggers a re-render before the child rows are fully initialized, causing the visual glitch.

### Proposed Solution
Find the point in `TransactionsTable.jsx` (or the related hooks) where an existing transaction is converted to a split, and ensure:
1. The child row initialization happens atomically — no intermediate render with an empty $0.00 row
2. The extra phantom row is not being inserted during the conversion

### Implementation Plan

**Understand:** When an existing transaction is converted to a split, the state update cycle triggers an extra render that shows a $0.00 row and causes a visual flicker.

**Match:** The `Account.tsx` component uses `useLayoutEffect` to dispatch actions that close splits for parent transactions when `prependTransactions` changes — a similar synchronous-before-paint approach may be needed for the conversion flow.

**Plan:**
1. Clone the fork locally and set up the dev environment following the contributing guide
2. Open `packages/desktop-client/src/components/transactions/TransactionsTable.jsx` and find the split conversion handler
3. Compare the new-split creation path vs. the existing-transaction conversion path
4. Identify where the extra $0.00 child row is being inserted
5. Fix the state update to prevent the phantom row and eliminate the re-render flicker
6. Add a test case in `TransactionsTable.test.tsx` to cover this conversion scenario

**Implement:** https://github.com/rohankumar009/actual/tree/fix/split-transaction-glitch

**Review:** Follow Actual Budget's CONTRIBUTING.md guidelines

**Evaluate:** Manually test the fix against the reproduction steps above; confirm no regression on new split transactions

---

## Testing Strategy

### Unit Tests
- [ ] Splitting an existing transaction does not produce a $0.00 split entry
- [ ] Split rows render correctly without flickering after conversion
- [ ] New split transactions (from scratch) are unaffected
- [ ] `is_parent` / `is_child` flags are set correctly after conversion

### Integration Tests
- [ ] Full split flow on an existing transaction behaves the same as on a new transaction

### Manual Testing
- Confirmed bug reproduction on https://demo.actualbudget.org — $0.00 entry appears after splitting an existing transaction
- Will manually verify fix against all steps in the reproduction process above

---

## Implementation Notes

### Week 1 Progress
Completed environment research and codebase analysis. Identified the key files involved in the split transaction flow:
- **`TransactionsTable.jsx`** — handles split row rendering and the conversion UI
- **`Account.tsx`** — manages split open/close state synchronously using `useLayoutEffect`
- **`loot-core/src/shared/transactions.ts`** — core logic for parent/child split relationships

The conversion flow differs from the new-split flow in how child rows are initialized, which is the likely root cause of both the UI flicker and the phantom $0.00 entry. Next step is setting up the local dev environment and stepping through the conversion code path directly.

### Code Changes
- **Files to modify:** `packages/desktop-client/src/components/transactions/TransactionsTable.jsx`
- **Possibly also:** `packages/loot-core/src/shared/transactions.ts` if the phantom row is inserted at the data layer
- **Key commits:** *To be added as implementation progresses*
- **Approach decisions:** Will compare the new-split vs. conversion code paths side by side to isolate the exact divergence

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
- [Related issue #3465 — Split Transactions UI quirks](https://github.com/actualbudget/actual/issues/3465)
- [PR #2834 — Reapply rules to split transactions](https://github.com/actualbudget/actual/pull/2834)
