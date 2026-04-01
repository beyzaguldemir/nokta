# Changelog

All notable changes to Nokta will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Added
- Implementation CI workflow (Path C) — to be completed in Phase 0

---

## [0.1.0-dev] - 2025-04-01

### Added
- **program.md** — Complete spec (source of truth) with all 12 sections filled per Karpathy's autoresearch pattern
- **mobile-skeleton.md** — Template structure for spec sections with TODO placeholders
- **README.md** — Public-facing overview explaining 3 contribution paths (A: edit program.md sections, B: add specs/*.md, C: implement code)
- **specs/TEMPLATE.md** — Feature spec template with 5 required sections (IDENTITY, NON-GOALS, DATA CONTRACTS, OBJECTIVE FUNCTION, RATCHET RULE)
- **checklists/** — Spec scoring rubrics:
  - `section_XX.yml` (12 files) — Checklist for each program.md section
  - `spec_generic.yml` — Generic checklist for specs/*.md files
- **scripts/section_score.py** — Python scoring engine supporting:
  - `--section N` — Score a single program.md section
  - `--all` — Score all program.md sections
  - `--spec-file specs/X.md` — Score a feature spec file
  - `--ci-comment` — Generate CI-formatted markdown output
- **.github/workflows/md-ratchet.yml** — CI workflow for Path A (program.md section edits):
  - Triggers on changes to `program.md` (currently aliased to program.md)
  - Scores changed sections against main baseline
  - Auto-merges if score ≥ main, rejects if score < main
  - Posts sticky PR comment with score breakdown
- **.github/workflows/spec-ratchet.yml** — CI workflow for Path B (specs/*.md additions):
  - Triggers on changes to `specs/**/*.md`
  - Scores new/modified spec files against generic rubric
  - First merge establishes baseline, subsequent PRs must match or beat it
  - Auto-merges if eligible
  - Posts sticky PR comment with score breakdown
- **AGENT.md** — Comprehensive AI agent instructions:
  - Distinguishes 3 contribution paths (A/B auto-merge, C human-review)
  - Human-in-loop protocol for implementation PRs
  - Spec-implementation validation checklist
  - Architectural invariants and anti-patterns
  - Git workflow and commit conventions
- **CLAUDE.md** — Quick reference for Claude Code sessions:
  - Tech stack summary
  - Common development commands
  - File structure overview
  - Immutable files list
  - Hard gates and golden flow tests
  - Task-specific workflows
- **PLAN.md** — Phased implementation roadmap:
  - 10 phases from Phase 0 (documentation) to Phase 10 (v1.0.0 release)
  - Current status tracking (Phase 0 at 90% complete)
  - Deliverables and acceptance criteria per phase
  - Version history table
- **CHANGELOG.md** — This file

### Changed
- README.md now clearly separates Path A, Path B, and Path C contribution workflows
- Scoring system now supports both program.md sections and standalone spec files

### Verification
- Tested section scoring: `python scripts/section_score.py --section 1` → 100/100 (IDENTITY section complete)
- Tested spec scoring: `python scripts/section_score.py --spec-file specs/TEMPLATE.md` → Score calculated (fails due to TODO placeholders, as expected)
- Tested md-ratchet CI: Mock PR with program.md changes triggers workflow correctly
- Tested spec-ratchet CI: Mock PR with specs/*.md changes triggers workflow correctly

---

## Version Numbering

- **v0.x.y** — Development versions (pre-stable)
- **v1.0.0** — First stable release (target: Phase 10 complete, all golden flows pass at 100%)

### Development Phase Mapping

| Version | Phase | Status |
|---------|-------|--------|
| v0.1.0-dev | Phase 0: Documentation & Infrastructure | 🟡 In Progress (90%) |
| v0.2.0 | Phase 1: Expo Scaffold & TypeScript Contracts | 🔴 Not Started |
| v0.3.0 | Phase 2: Storage Service & State Management | 🔴 Not Started |
| v0.4.0 | Phase 3: Screen 1 — Idea List | 🔴 Not Started |
| v0.5.0 | Phase 4: LLM Service (Mock Mode) | 🔴 Not Started |
| v0.6.0 | Phase 5: Screen 2 — Idea Chat | 🔴 Not Started |
| v0.7.0 | Phase 6: Screen 3 — Spec Card | 🔴 Not Started |
| v0.8.0 | Phase 7: Golden Flow Tests | 🔴 Not Started |
| v0.9.0 | Phase 8: UI Config System | 🔴 Not Started |
| v1.0.0-rc1 | Phase 9: Bundle Optimization & Polish | 🔴 Not Started |
| v1.0.0 | Phase 10: First Stable Release | 🔴 Not Started |

---

## Notes

- All changes in v0.x.y are **spec contributions** (Path A/B). No implementation code exists yet.
- Implementation (Path C) begins in Phase 1 (v0.2.0).
- See PLAN.md for detailed phase breakdown.
- See program.md for complete product spec (source of truth).
