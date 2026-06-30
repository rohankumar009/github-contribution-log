# github-contribution-log
My Open Source Contribution Logs

# Contribution 1: Splitting existing transaction causes UI glitches as splits disappear and reappear

**Contribution Number:** 1
**Student:** Rohan Kumar
**Issue:** https://github.com/actualbudget/actual/issues/5217
**Status:** Phase IV Complete

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
- `packages/desktop-client/src/components/transactions/TransactionsTable.tsx` — main transaction table and split row rendering
- `packages/loot-core/src/shared/transactions.ts` — core split transaction logic (`makeChild`, parent/child relationships)

---

## Reproduction Process

### Environment Setup
Reproduced on the live demo instance at https://demo.actualbudget.org — no local setup required.

### Steps to Reproduce
1. Go to any account (e.g. Checking)
2. Click an existing transaction to select it
3. Click the Category field and choose "Split transaction" from the dropdown
4. Observe: split rows flicker (disappear and reappear), and a phantom $0.00 entry appears at the bottom

### Reproduction Evidence
- **Branch:** https://github.com/rohankumar009/actual/tree/fix/split-transaction-glitch
- **Screenshots/logs:** See screen recording in the original issue — bug confirmed reproduced on demo instance
- **My findings:** The $0.00 phantom split entry consistently appears after converting an existing transaction to a split. The glitch does not occur when creating a split transaction from scratch, indicating the bug is specific to the conversion flow.

---

## Solution Approach

### Analysis
Two distinct bugs occur only when converting an existing transaction into a split (not when creating a split from scratch):
1. The first child row defaulted to $0.00 instead of inheriting the parent transaction's amount, leaving a phantom zero-amount entry.
2. The split rows flickered (disappeared and reappeared) during conversion because the UI dispatch that marks splits as "expanded" was firing after the data update that triggers re-render, instead of before it.

### Proposed Solution
- In `transactions.ts`, fix `makeChild` to pass in the parent transaction's amount so the first child row inherits the full amount instead of defaulting to $0.00. Also corrected the calculation logic that determines whether there's an unallocated remaining amount.
- In `TransactionsTable.tsx`, move the `splitsExpandedDispatch` call to fire before `onSplitProp`, ensuring the split rows are marked as expanded before the data update triggers a re-render — eliminating the flicker.

### Implementation Plan

**Understand:** Converting an existing transaction to a split caused a $0.00 phantom row and a UI flicker, both absent when creating a split from scratch.

**Match:** Compared the "create new split" code path against the "convert existing transaction" path to isolate where they diverged — found it in `makeChild`'s amount handling and in dispatch ordering in `TransactionsTable.tsx`.

**Plan:**
1. ✅ Fix `makeChild` in `transactions.ts` to pass the parent amount to the first child row
2. ✅ Fix the unallocated-amount calculation logic in the same function
3. ✅ Reorder `splitsExpandedDispatch` to fire before `onSplitProp` in `TransactionsTable.tsx`
4. ✅ Manually test against reproduction steps
5. ✅ Submit PR

**Implement:** https://github.com/actualbudget/actual/pull/8364

**Review:** Followed Actual Budget's CONTRIBUTING.md guidelines, including disclosing AI tool usage (Claude) in the PR description per their AI Usage Policy

**Evaluate:** Manually verified the fix resolves both bugs, with no regression on new split transactions created from scratch

---

## Testing Strategy

### Unit Tests
- [ ] Splitting an existing transaction does not produce a $0.00 split entry
- [ ] Split rows render correctly without flickering after conversion
- [ ] New split transactions (from scratch) are unaffected

### Integration Tests
- [ ] Full split flow on an existing transaction behaves the same as on a new transaction

### Manual Testing
Testing steps performed:
1. Go to any account (e.g. Checking)
2. Click an existing transaction to select it
3. Click the Category field and choose "Split transaction" from the dropdown
4. Observed (before fix): split rows flicker, phantom $0.00 entry appears at the bottom

Verifying the fix:
1. Repeated the same steps above
2. Split rows now appear immediately without flickering
3. The first split entry shows the full original transaction amount, not a phantom $0.00
4. Verified that creating a brand new split transaction (on a new transaction row) still works as before — no regression

---

## Implementation Notes

### Week 1 Progress
Completed environment research and codebase analysis. Identified the key files involved in the split transaction flow and traced the divergence between the "new split" and "conversion" code paths to `transactions.ts` and `TransactionsTable.tsx`.

### Week 2 Progress
Implemented and tested the fix:
- Modified `makeChild` in `transactions.ts` so the first child row inherits the parent transaction's amount instead of defaulting to $0.00, and corrected the unallocated-amount calculation
- Reordered the `splitsExpandedDispatch` call in `TransactionsTable.tsx` to fire before `onSplitProp`, so split rows are marked expanded before the re-render is triggered, eliminating the flicker
- Manually tested against the original reproduction steps and confirmed both bugs are resolved with no regressions
- Opened PR #8364 against `actualbudget:master`

### Code Changes
- **Files modified:** `packages/loot-core/src/shared/transactions.ts`, `packages/desktop-client/src/components/transactions/TransactionsTable.tsx`
- **Key commits:** [a26bd12](https://github.com/rohankumar009/actual/commit/a26bd12) — "Fix split transaction glitch: phantom $0.00 row and UI flicker"
- **Approach decisions:** Disclosed use of Claude as an AI tool to help identify the root cause and write the fix, per the repo's AI Usage Policy. Kept the diff minimal — two files, no unrelated refactors — to make review straightforward.

---

## Pull Request

**PR Link:** https://github.com/actualbudget/actual/pull/8364

**PR Description:** Fixes two bugs that occur when converting an existing transaction into a split transaction: (1) the first child row defaulted to $0.00, leaving a phantom zero-amount entry, and (2) the split rows flickered (disappeared and reappeared) during conversion. Both issues were absent when creating a split transaction from scratch. Fixed by updating `makeChild` in `transactions.ts` to inherit the parent amount, and by reordering the `splitsExpandedDispatch` call in `TransactionsTable.tsx` to fire before the data update that triggers re-render.

**Maintainer Feedback:**
- Currently addressing pre-review requirements flagged by the repo's automated checks: removing the `[WIP]` prefix from the PR title, resolving a failing release-notes check, completing self-review, and updating the branch with upstream changes before requesting review.

**Status:** Iterating

---

## Learnings & Reflections

### Technical Skills Gained
Learned how to trace a UI bug through a real production React/TypeScript codebase by comparing two similar code paths (new split vs. converted split) to isolate where their behavior diverged. Also got hands-on experience with state update ordering issues in React — specifically how dispatching one state update before vs. after another can cause visible UI flicker.

### Challenges Overcome
The hardest part was pinpointing why the bug only happened on conversion and not on new splits, since the symptom (flicker + phantom row) suggested two separate bugs rather than one. Tracing both `transactions.ts` and `TransactionsTable.tsx` side by side made it clear they were two distinct, related issues.

### What I'd Do Differently Next Time
Would set up the local dev environment earlier in the process rather than relying solely on the demo site, since local debugging tools (React DevTools, breakpoints) would have made isolating the root cause faster.

---

## Resources Used
- [Issue #5217](https://github.com/actualbudget/actual/issues/5217)
- [Actual Budget contributing guide](https://github.com/actualbudget/actual/blob/master/CONTRIBUTING.md)
- [Actual Budget demo instance](https://demo.actualbudget.org)
- [Related issue #3465 — Split Transactions UI quirks](https://github.com/actualbudget/actual/issues/3465)
- [PR #2834 — Reapply rules to split transactions](https://github.com/actualbudget/actual/pull/2834)
- [My PR #8364](https://github.com/actualbudget/actual/pull/8364)
