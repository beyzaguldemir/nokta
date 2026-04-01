# AGENT.md — Instructions for AI Agents

**This file provides guidance to AI agents (Claude Code, GitHub Copilot, Cursor, Codex, etc.) working on the Nokta repository.**

## Source of Truth

**program.md is the single source of truth for this project.**

- Before starting any task, read `program.md` in full.
- If you encounter a contradiction between `program.md` and any other file (including this one), defer to `program.md`.
- `program.md` defines what Nokta IS, what it does, and what it NEVER does.
- Never implement features not explicitly described in `program.md` or approved spec files in `specs/`.

## Repository Context

This is **Nokta** — a mobile app that matures idea sparks (dots) into structured product specs (pages) through LLM-guided questioning. The project follows Karpathy's autoresearch pattern:

- Humans write specs (`program.md`, `specs/*.md`)
- Machines write code (based on specs)
- CI enforces quality via ratcheting metrics
- Score never drops

## Two Contribution Paths

Contributors can participate in two distinct ways:

### Path A & B: Spec Contributions (NO HUMAN REVIEW)

**Path A:** Edit `program.md` sections (spec writing)
**Path B:** Add new `specs/*.md` files (feature spec writing)

These paths are **fully automated**:
- CI scores the spec using `scripts/section_score.py` and `checklists/*.yml`
- Score ≥ main baseline → ✅ Auto-merge
- Score < main baseline → ❌ Auto-reject
- No human approval required

**Branch naming:**
```
section/<number>-<description>    # section/04-data-contracts
spec/<feature-name>                # spec/user-profile
```

**Your role as AI agent:**
- Help contributors improve spec quality
- Run local scoring before PR
- Explain which checklist items failed
- Never modify immutable files (see program.md § 0)

### Path C: Implementation Contributions (HUMAN-IN-LOOP REQUIRED)

**Path C:** Implement code based on existing specs in `program.md` or `specs/*.md`

These PRs are **semi-automated** with mandatory human review:
- CI validates implementation matches spec
- CI runs hard gates (TypeScript, ESLint, tests, bundle size)
- CI calculates golden flow pass rate
- If all gates pass AND metric ≥ main → ✅ Eligible for merge
- **HOWEVER:** Maintainer must manually review and approve before merge
- Auto-merge is DISABLED for implementation PRs

**Branch naming:**
```
implement/<feature-name>           # implement/idea-list-screen
fix/<bug-description>              # fix/maturity-transition-crash
test/<test-description>            # test/golden-flow-persistence
```

**Why human review is required:**
- Code architecture compliance (program.md § 11)
- Security vulnerabilities (XSS, injection, etc.)
- API key or secret exposure
- Unintended side effects
- Design pattern violations

**Your role as AI agent:**
- Write code that implements the spec exactly
- Ensure all hard gates pass locally before PR
- Provide evidence (screenshots, recordings, APK)
- Explain what changed and why
- Run golden flow tests and report results
- Do NOT auto-merge implementation PRs — wait for human approval

## Human-in-Loop Protocol

**Items requiring explicit human approval:**

1. **Implementation PRs** (Path C)
   - All PRs that add/modify code in `src/`, `app/`, or test files
   - Even if CI passes, human must approve

2. **Dependency changes**
   - Any modification to `package.json` or `package-lock.json`
   - Requires maintainer approval via GitHub issue first

3. **Immutable infrastructure changes** (program.md § 0)
   - `.github/workflows/` — CI pipeline
   - `scripts/section_score.py` — Scoring engine
   - `checklists/*.yml` — Scoring rubrics
   - `app.json`, `tsconfig.json`, `babel.config.js`, `.eslintrc.js` — Build config
   - These are **FORBIDDEN** to all contributors

4. **Quarantine items** (program.md § 10)
   - Any file containing API keys or secrets
   - Auth-related code

**When you reach a decision point that requires human approval:**
1. Stop and ask the user for guidance
2. Present options with trade-offs
3. Wait for explicit approval
4. Document the decision in commit message

**Never assume user intent.** If unclear, ask.

## Spec-Implementation Validation

When reviewing an implementation PR, verify:

1. **Spec compliance:**
   - Does code implement the spec exactly as written?
   - Are all acceptance criteria met?
   - Are TypeScript interfaces from spec used correctly?

2. **Hard gates:**
   - `npx tsc --noEmit` → Zero errors
   - `npx eslint . --ext .ts,.tsx` → Zero errors
   - `npx expo export --dump-sourcemap` → JS bundle < 2MB
   - `git diff origin/main -- package.json` → No unauthorized deps

3. **Golden flow tests:**
   - Create Idea flow passes
   - Refinement flow passes
   - Spec view flow passes
   - Persistence flow passes

4. **Architectural invariants (program.md § 11):**
   - Zustand for state (not Context API, not Redux)
   - Services never called directly from components
   - No direct AsyncStorage access from components
   - Proper file naming and location

5. **Testing philosophy (program.md § 12):**
   - Tests validate user behavior, not implementation details
   - No snapshot spam
   - No mock-everything tests
   - Minimum coverage met

## program.md Update Protocol

**Spec contributions (Path A/B) may update program.md or add specs/*.md.**
**Implementation contributions (Path C) NEVER update program.md.**

If an implementation reveals a spec error or ambiguity:
1. Open a GitHub issue describing the problem
2. Wait for maintainer to update spec
3. Then implement against the corrected spec

Do NOT:
- Update program.md as part of an implementation PR
- Assume spec intent
- Implement features not in spec
- Add "nice to have" features

## CHANGELOG.md Update Protocol

**Maintain semantic versioning discipline.**

After every significant phase or feature completion:
1. Add an entry to CHANGELOG.md following [Keep a Changelog](https://keepachangelog.com/) format
2. Use semantic versioning (semver.org): v0.x.y during development, v1.0.0 for first stable release
3. Categories: `Added`, `Changed`, `Deprecated`, `Removed`, `Fixed`, `Security`
4. Be specific: "Added IdeaListScreen with maturity badges" not "Added files"
5. Include verification details showing the feature was tested

Current version: **v0.1.0-dev** (Nokta mobile app in development)

See **Git Workflow** section for commit and tag creation instructions.

## Phantom Context Warning

**Do not hallucinate file contents or assume unverified paths.**

This repo is in early stages. Many features referenced in `program.md` do not exist yet.

Before referencing a file:
1. Check filesystem with `ls`, `Read`, or `Glob` tools
2. If file doesn't exist, tell user: "This requires implementing X, which is not yet written"
3. Ask if they want you to scaffold it now

## Language and Localization

**Code and documentation: English only for v0.1.**

- All TypeScript code, comments, variable names, git messages: English
- Documentation files (README, AGENT, program.md, etc.): English
- User-facing strings in the app: English only (i18n deferred to v0.2+, per program.md § 3)

## Code Style

- TypeScript: ESLint rules defined in `.eslintrc.js` (IMMUTABLE)
- Files: `kebab-case.ts` / `.tsx`
- Components: `PascalCase`
- Hooks: `useCamelCase`
- Types: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- 2-space indent, semicolons required
- Comments: Explain *why*, not *what* — code should be self-documenting

## Testing Philosophy

**Tests must validate user behavior, not implementation details.**

Valid test: "If I refactored internals but kept same UX, would this test pass?"
- Yes → valid test
- No → rewrite the test

**Anti-patterns (AUTO-REJECT):**
- `expect(true).toBe(true)` — tests nothing
- Snapshot spam — breaks on every change
- Mock everything — if you mock storage, LLM, navigation, AND component, you're testing nothing
- Duplicate tests with different variable names
- Tests without assertions

**Minimum requirements:**
- New component → at least 1 render test
- New service function → at least 2 tests (happy + error)
- Every maturity transition → covered by test
- Every golden flow → integration test

## Dependency Management

**Minimize external dependencies. Only maintainer may add new deps.**

Allowed dependencies are listed in program.md § 2 (Setup Protocol).

If you need a new dependency:
1. Open a GitHub issue
2. Explain why existing deps cannot solve the problem
3. Wait for maintainer approval
4. Only then modify package.json

Do NOT:
- Add a dependency without approval
- Use a different state management library (Zustand is mandatory)
- Add UI libraries beyond what's approved
- Install testing frameworks beyond Jest + React Native Testing Library

## Git Workflow

**Small, atomic commits with descriptive messages.**

Examples:
- ✅ `feat(idea-list): add maturity badges to idea cards`
- ✅ `test(golden-flow): add persistence test for idea storage`
- ✅ `fix(llm-service): handle timeout in mock mode`
- ✅ `docs(program-md): improve LLM contract section`
- ❌ `Update files` (too vague)
- ❌ `WIP` (commit meaningful units of work)

### Commit Workflow (AI Agent Responsibility)

When you complete a meaningful unit of work:

1. **Verify locally:**
   ```bash
   npx tsc --noEmit
   npx eslint . --ext .ts,.tsx
   npm test
   ```

2. **Stage and commit:**
   ```bash
   git add <files>
   git commit -m "feat(component): brief description"
   ```
   - Use conventional commit prefixes: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`
   - Keep first line under 72 characters
   - Add detailed description in commit body if needed

3. **Push to remote:**
   ```bash
   git push origin <branch-name>
   ```

4. **Do NOT create version tags** — only maintainer creates tags

### When to Create Commits

Create a commit when:
- ✅ A single feature or fix is complete
- ✅ All hard gates pass locally
- ✅ Tests pass locally
- ❌ Work is incomplete (no WIP commits)
- ❌ Breaking CI (fix locally first)

## Key Files and Their Purposes

| File | Purpose | Contribution Path |
|------|---------|-------------------|
| `program.md` | Source of truth — complete spec | Path A (sections) |
| `mobile-skeleton.md` | Template for mobile.md sections | Reference only |
| `specs/TEMPLATE.md` | Template for feature specs | Copy for Path B |
| `specs/*.md` | Feature spec files | Path B (new specs) |
| `AGENT.md` | This file — AI agent instructions | Maintainer only |
| `CLAUDE.md` | Claude Code quick reference | Maintainer only |
| `README.md` | Public-facing project overview | Maintainer only |
| `PLAN.md` | Phased implementation roadmap | Maintainer only |
| `WALKTHROUGH.md` | Guided tour for new contributors | Maintainer only |
| `CHANGELOG.md` | Semantic versioning log | Updated on releases |
| `checklists/*.yml` | Scoring rubrics for specs | IMMUTABLE |
| `scripts/section_score.py` | Spec scoring engine | IMMUTABLE |
| `.github/workflows/*.yml` | CI pipeline definitions | IMMUTABLE |
| `app/` | Expo Router screens | Path C (implementation) |
| `src/` | App source code | Path C (implementation) |
| `__tests__/` | Test files | Path C (implementation) |

## Implementation PR Checklist

When opening an implementation PR (Path C), include:

```markdown
## What Changed
[Brief description of what was implemented]

## Spec Reference
- Implements: `program.md § X` or `specs/feature-name.md`
- Acceptance criteria met: [list them]

## Evidence (REQUIRED)
- [ ] 📸 Screenshot(s) of working feature
- [ ] 🎥 Screen recording (< 60 seconds, YouTube/Loom)
- [ ] 📱 Expo Go QR code or APK download link

## Local Verification
- [ ] `npx tsc --noEmit` → Zero errors
- [ ] `npx eslint . --ext .ts,.tsx` → Zero errors
- [ ] `npm test` → All tests pass
- [ ] Golden flow tests updated (if applicable)

## Architectural Compliance
- [ ] Zustand for state management
- [ ] Services called via hooks, not directly
- [ ] No direct AsyncStorage access from components
- [ ] Proper file naming and location
- [ ] Tests validate user behavior, not implementation

## Checklist
- [ ] No new dependencies added (or approved via issue)
- [ ] No immutable files modified
- [ ] PR is ≤ 10 files, ≤ 500 lines changed
- [ ] Commit messages follow conventional format
```

## Anti-Patterns to Avoid

❌ **Don't** implement features not in `program.md` or approved specs
❌ **Don't** modify immutable infrastructure files
❌ **Don't** add dependencies without approval
❌ **Don't** use different state management (Zustand is mandatory)
❌ **Don't** auto-merge implementation PRs (human approval required)
❌ **Don't** write snapshot spam tests
❌ **Don't** mock everything in tests
❌ **Don't** commit WIP or broken code
❌ **Don't** bypass hard gates ("I'll fix later")
❌ **Don't** add features marked as NON-GOALS (program.md § 3)
❌ **Don't** create backend servers, auth systems, or analytics (v0.1 is local-only)

## When in Doubt

1. Read `program.md` again
2. Check if feature is in NON-GOALS (program.md § 3)
3. Check if file is IMMUTABLE (program.md § 0)
4. Ask the user for clarification
5. Wait for explicit approval before proceeding

---

**Remember:** This is a spec-driven, CI-enforced, ratcheting quality system inspired by Karpathy's autoresearch. Precision, spec compliance, and human oversight (for implementation) are paramount. When uncertain, ask — don't assume.

**Spec PRs (Path A/B) = Automated merge**
**Implementation PRs (Path C) = Human approval required**
