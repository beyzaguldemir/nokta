# WALKTHROUGH.md — Guided Tour for New Contributors

**Welcome to Nokta!** This guide will walk you through the project structure, contribution model, and how to make your first contribution.

---

## What is Nokta?

Nokta is a mobile app that captures idea sparks (as small as a single word) and matures them into structured product specs through LLM-guided questioning.

**Metaphor:** Dot → Line → Paragraph → Page (cosmological expansion)

**Target User:** Anyone with ideas but no structured process to mature them — founders, students, researchers, product managers.

**Core Pattern:** Karpathy's autoresearch
- Humans write specs (`program.md`, `specs/*.md`)
- Machines write code (from specs)
- CI enforces quality via ratcheting metrics
- Score never drops

---

## Three Ways to Contribute

Nokta has **3 contribution paths**. Pick the one that fits your skill and interest:

| Path | What You Do | Branch | Merge | Skill Level |
|------|-------------|--------|-------|-------------|
| **A** | Edit sections of `program.md` (main spec) | `section/NN-name` | Auto (if score ≥ main) | Writing, spec design |
| **B** | Add new feature specs in `specs/*.md` | `spec/feature-name` | Auto (if score ≥ main) | Writing, product thinking |
| **C** | Implement code from specs | `implement/feature-name` | Human review required | TypeScript, React Native, testing |

**Path A/B** = Spec contributions (automated merge)
**Path C** = Implementation contributions (human-in-loop)

---

## Quick Start: Choose Your Path

### I Want to Write Spec (Path A or B)

**Path A: Edit a section of program.md**

1. **Read program.md** — This is the source of truth. Understand what Nokta is and is NOT.

2. **Pick a section** — See README.md for section list (0-12). Example: Section 4 (DATA CONTRACTS).

3. **Create a branch:**
   ```bash
   git checkout -b section/04-data-contracts
   ```

4. **Edit program.md** — Write the section. Delete TODO placeholders.

5. **Score locally:**
   ```bash
   pip install pyyaml
   python scripts/section_score.py --section 4
   ```

6. **Fix until score ≥ current main baseline.**

7. **Commit and push:**
   ```bash
   git add program.md
   git commit -m "feat(section-04): add data contracts with TypeScript interfaces"
   git push origin section/04-data-contracts
   ```

8. **Open a PR** — CI runs automatically. If score ≥ main → auto-merge. If score < main → fix and push again.

**Path B: Add a new feature spec**

1. **Copy the template:**
   ```bash
   cp specs/TEMPLATE.md specs/your-feature-name.md
   ```

2. **Create a branch:**
   ```bash
   git checkout -b spec/your-feature-name
   ```

3. **Fill all 5 sections:**
   - § 1 IDENTITY
   - § 2 NON-GOALS (at least 5 items)
   - § 3 DATA CONTRACTS (TypeScript interfaces)
   - § 4 OBJECTIVE FUNCTION (hard gates + scalar metric)
   - § 5 RATCHET RULE (merge rule in one sentence)

4. **Delete all `> TODO:` lines** — Any remaining TODO keeps your score low.

5. **Score locally:**
   ```bash
   python scripts/section_score.py --spec-file specs/your-feature-name.md
   ```

6. **Commit and push:**
   ```bash
   git add specs/your-feature-name.md
   git commit -m "spec(your-feature): describe feature"
   git push origin spec/your-feature-name
   ```

7. **Open a PR** — CI scores against `checklists/spec_generic.yml`. First PR for this file → sets baseline. Subsequent PRs → must match or beat baseline.

---

### I Want to Write Code (Path C)

**Prerequisites:**
- Read `program.md` in full
- Understand the spec you're implementing
- Have Node.js >= 18.x, npm >= 9.x, Expo CLI installed

**Steps:**

1. **Pick a spec to implement:**
   - Option 1: Implement from `program.md` (e.g., Screen 1: Idea List from § 5)
   - Option 2: Implement from `specs/*.md` (e.g., `specs/user-profile.md`)

2. **Create a branch:**
   ```bash
   git checkout -b implement/idea-list-screen
   ```

3. **Write code that implements the spec EXACTLY:**
   - Use TypeScript interfaces from spec
   - Follow architectural invariants (program.md § 11)
   - Add tests for acceptance criteria
   - Follow testing philosophy (program.md § 12)

4. **Verify hard gates locally:**
   ```bash
   npx tsc --noEmit          # TypeScript
   npx eslint . --ext .ts,.tsx   # ESLint
   npm test                  # Jest tests
   npx expo export --dump-sourcemap  # Bundle size check
   ```

5. **Commit and push:**
   ```bash
   git add <files>
   git commit -m "feat(idea-list): implement idea list screen with maturity badges"
   git push origin implement/idea-list-screen
   ```

6. **Open a PR with evidence:**
   - Screenshot(s) of working feature
   - Screen recording (< 60 seconds)
   - APK download link (optional)
   - Spec reference (which section of program.md or specs/*.md)
   - Acceptance criteria checklist

7. **Wait for human review** — Even if CI passes, a maintainer must review and approve before merge.

---

## Understanding the File Structure

```
nokta/
├── program.md              # SOURCE OF TRUTH (complete spec)
├── mobile-skeleton.md      # Template for spec sections (reference only)
├── README.md               # Public overview
├── AGENT.md                # AI agent instructions
├── CLAUDE.md               # Claude Code quick reference
├── PLAN.md                 # Phased roadmap
├── WALKTHROUGH.md          # This file
├── CHANGELOG.md            # Version history
│
├── specs/                  # Feature spec files (Path B)
│   ├── TEMPLATE.md         # Template for new specs
│   └── *.md                # Individual feature specs
│
├── checklists/             # Scoring rubrics (IMMUTABLE)
│   ├── section_00.yml      # Checklist for program.md § 0
│   ├── section_01.yml      # Checklist for program.md § 1
│   ├── ...                 # (12 section checklists total)
│   └── spec_generic.yml    # Checklist for specs/*.md
│
├── scripts/                # CI tooling (IMMUTABLE)
│   └── section_score.py    # Scoring engine
│
├── .github/workflows/      # CI pipelines (IMMUTABLE)
│   ├── md-ratchet.yml      # Path A: program.md sections
│   ├── spec-ratchet.yml    # Path B: specs/*.md files
│   └── implementation-ratchet.yml  # Path C: code PRs (to be added)
│
├── app/                    # Expo Router screens (Path C, future)
├── src/                    # App source code (Path C, future)
└── __tests__/              # Test files (Path C, future)
```

---

## The Ratchet: How CI Works

Nokta uses a **ratcheting quality system**. Score never drops.

### For Spec PRs (Path A/B)

```
You push code → CI triggers
    ↓
CI scores your spec using scripts/section_score.py
    ↓
Compare: pr_score vs main_score
    ├── pr_score >= main_score → ✅ ELIGIBLE → Auto-merge
    └── pr_score < main_score  → ❌ REJECTED → Fix and push again
```

**Example:**
- Main baseline for Section 4: 85/100
- Your PR score: 90/100 → ✅ Auto-merge
- Your PR score: 80/100 → ❌ Rejected (fix and push)

**Sticky PR comment** shows:
- Your score
- Main baseline
- Delta
- Which checklist items failed
- Detailed score breakdown

### For Implementation PRs (Path C)

```
You push code → CI triggers
    ↓
Hard Gates: TypeScript, ESLint, Bundle Size, Tests
    ├── ANY FAIL → ❌ REJECT
    └── ALL PASS ↓
Golden Flow Tests
    ├── pr_score = (passing_tests / total_tests) × 100
    ├── main_score = baseline from main
    └── pr_score >= main_score?
        ├── YES → ✅ ELIGIBLE (but NOT auto-merged)
        └── NO  → ❌ REJECT
    ↓
Human reviews (even if CI passes)
    ├── Approved → ✅ MERGE
    └── Changes requested → Fix and push
```

**Why human review?**
- Security (API keys, XSS, injection)
- Architectural compliance (program.md § 11)
- Design patterns
- Unintended side effects

---

## Understanding the Scoring System

### Spec Scoring (Path A/B)

Each spec (program.md section or specs/*.md file) has a **checklist YAML** defining scoring criteria.

**Example:** `checklists/section_04.yml` (DATA CONTRACTS section)

```yaml
- id: data_contracts_section
  weight: 15
  check_type: heading
  pattern: "## 4. DATA CONTRACTS"

- id: typescript_blocks
  weight: 10
  check_type: code_block
  language: typescript
  min_count: 1

- id: data_contracts_min
  weight: 10
  check_type: word_count
  min_words: 200
```

**Score calculation:**
- Each check has a weight (points)
- Check passes → add points
- Check fails → 0 points
- Final score = (earned_points / total_points) × 100

**Checklist types:**
- `heading` — Required section heading exists
- `code_block` — Code block with specific language
- `word_count` — Minimum word count in section
- `keyword` — Specific keyword/phrase present
- `list_items` — Minimum number of list items (e.g., 5 non-goals)
- `no_todos` — No `> TODO:` placeholders remaining

### Implementation Scoring (Path C)

**Hard Gates (binary pass/fail):**
- TypeScript compiles (`npx tsc --noEmit`)
- ESLint passes (`npx eslint . --ext .ts,.tsx`)
- Bundle size < 2MB
- No unauthorized dependencies

**Scalar Metric:**
- Golden flow test pass rate = (passing_tests / total_tests) × 100
- Must be ≥ main baseline

**Golden flows (from program.md § 8):**
1. Create Idea — FAB → enter spark → DOT idea created
2. Refinement — Chat → question → answer → maturity transitions
3. Spec View — Navigate to spec → populated fields match conversation
4. Persistence — Create → close → reopen → data intact

---

## Immutable Files (DO NOT EDIT)

These files are protected by CI. Contributors **MUST NOT** modify them:

- `.github/workflows/` — CI pipeline (only maintainer)
- `scripts/section_score.py` — Scoring engine (gaming prevention)
- `checklists/*.yml` — Scoring rubrics (source of truth)
- `app.json` — Expo identity (breaks builds)
- `tsconfig.json` — TypeScript config (defeats type safety)
- `babel.config.js` — Transpiler config (phantom errors)
- `.eslintrc.js` — Lint rules (bypasses quality control)
- `package.json` — Dependencies (only maintainer, requires issue approval)

**Why?** Fixed infrastructure prevents gaming the metric. Only editable surface changes.

---

## Common Scenarios

### Scenario 1: "I want to improve an existing spec section"

**You can!** Follow Path A:
1. Find the section in `program.md`
2. Create branch `section/NN-description`
3. Edit the section
4. Score locally (`python scripts/section_score.py --section NN`)
5. Your score must be ≥ current score on main
6. Commit, push, open PR
7. CI auto-merges if score ≥ main

**Note:** Even Section 1 (IDENTITY) is at 100/100, but you can still improve it if you maintain the score!

### Scenario 2: "I want to propose a new feature"

**Follow Path B:**
1. Copy `specs/TEMPLATE.md` → `specs/your-feature.md`
2. Fill all 5 sections (IDENTITY, NON-GOALS, DATA CONTRACTS, OBJECTIVE FUNCTION, RATCHET RULE)
3. Delete all `> TODO:` lines
4. Score locally (`python scripts/section_score.py --spec-file specs/your-feature.md`)
5. First PR for this file → sets baseline (any score > 0)
6. Subsequent PRs for same file → must match or beat baseline

### Scenario 3: "I want to implement Screen 1 from program.md"

**Follow Path C:**
1. Read program.md § 5 (Screen & Feature Spec) for Screen 1 requirements
2. Read program.md § 4 for TypeScript interfaces
3. Read program.md § 11 for architectural invariants
4. Create branch `implement/idea-list-screen`
5. Write code:
   - `app/index.tsx` — Screen
   - `src/features/idea/components/IdeaCard.tsx` — Component
   - Tests for each component
6. Verify hard gates locally
7. Commit, push, open PR with screenshots/recording
8. **Wait for human review** (do NOT expect auto-merge)

### Scenario 4: "I found a bug in the spec"

**Open a GitHub issue:**
1. Describe the bug (contradiction, ambiguity, error)
2. Reference specific section (e.g., "program.md § 7 says X but § 11 says Y")
3. Wait for maintainer to decide
4. Maintainer will update spec via Path A
5. Then you can implement against corrected spec via Path C

**Do NOT:**
- Update program.md as part of an implementation PR
- Assume spec intent
- Implement features not in spec

### Scenario 5: "CI rejected my PR — what now?"

**For Spec PRs (Path A/B):**
1. Read the CI comment on your PR
2. It will show:
   - Your score vs main baseline
   - Which checklist items failed
   - Detailed breakdown
3. Fix the failing checks
4. Push to the same branch
5. CI re-runs automatically
6. Repeat until score ≥ main

**For Implementation PRs (Path C):**
1. Check which hard gate failed (TypeScript, ESLint, tests, bundle)
2. Fix locally
3. Verify with `npx tsc --noEmit`, `npx eslint`, `npm test`
4. Push again
5. CI re-runs

---

## Anti-Patterns (What NOT to Do)

❌ **Don't** implement features not in `program.md` or approved specs
❌ **Don't** modify immutable files
❌ **Don't** add dependencies without GitHub issue approval
❌ **Don't** use different state management (Zustand is mandatory)
❌ **Don't** expect auto-merge for implementation PRs (human approval required)
❌ **Don't** write snapshot spam tests
❌ **Don't** mock everything in tests (test user behavior, not implementation)
❌ **Don't** commit WIP or broken code
❌ **Don't** add features marked as NON-GOALS (program.md § 3)
❌ **Don't** create backend, auth, analytics (v0.1 is local-only)

---

## Getting Help

- **Read program.md** — Always start here. It's the source of truth.
- **Read AGENT.md** — Detailed instructions for AI agents (useful for humans too).
- **Read CLAUDE.md** — Quick reference with commands and file structure.
- **Read PLAN.md** — See what phase the project is in and what's next.
- **Open a GitHub issue** — Ask questions, report bugs, propose features.

---

## First Contribution Checklist

**Before opening your first PR:**

- [ ] I have read `program.md` in full
- [ ] I understand the 3 contribution paths (A, B, C)
- [ ] I know which path my PR belongs to
- [ ] I have run local scoring (Path A/B) or hard gates (Path C)
- [ ] My branch name follows convention (`section/`, `spec/`, or `implement/`)
- [ ] My commit message follows conventional commits format
- [ ] I have not modified immutable files
- [ ] I have not added dependencies without approval (Path C)
- [ ] My PR is ≤ 10 files, ≤ 500 lines changed
- [ ] (Path C only) I have included evidence (screenshots, recording, APK)

---

**Welcome to Nokta! We're excited to see your contribution.**

**Remember:**
- Spec PRs (Path A/B) → Auto-merge if score ≥ main
- Implementation PRs (Path C) → Human approval required
- Score never drops (the ratchet)
- program.md is source of truth

Happy contributing!
