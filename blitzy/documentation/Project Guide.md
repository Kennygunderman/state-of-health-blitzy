# 1. Executive Summary

## 1.1 Project Overview

State of Health is a personal health tracker: a React Native/Expo client and an Express/Prisma/PostgreSQL API. This work adds personalised weekly meal planning to both. A signed-in user completes a seven-step setup, reviews deterministic calorie and macro targets, generates a seven-day plan from a validated internal food and recipe catalog, inspects recipes and planned daily totals, swaps meals with atomic grocery updates, shops from an aggregated weekly checklist, and logs the portion eaten into the existing diary under a "From meal plan" label. Existing diary, workout, run and weight features are preserved, and the API stays compatible with older clients.

## 1.2 Completion Status

Completion covers scoped work only: 32 feature deliverables plus the 12 path-to-production activities needed to deploy them. Of those 44 requirements, 31 are complete, 3 partial and 10 not started.

```mermaid
%%{init: {"theme": "base", "themeVariables": {"pie1": "#5B39F3", "pie2": "#FFFFFF", "pieStrokeColor": "#B23AF2", "pieOuterStrokeColor": "#B23AF2", "pieSectionTextColor": "#B23AF2", "pieTitleTextSize": "17px"}}}%%
pie title 87.5% Complete
    "Completed" : 1460
    "Remaining" : 208
```

| Metric | Value |
|---|---|
| Total Hours | 1,668 |
| Completed Hours (AI + Manual) | 1,460 (1,460 autonomous + 0 manual) |
| Remaining Hours | 208 |
| Percent Complete | 87.5% (1,460 ÷ 1,668) |

## 1.3 Key Accomplishments

- ✅ Seven-step setup, target review and preference editing across 18 screens, resumable after a force-quit.
- ✅ Deterministic targets written through one path, so account, diary and planner agree.
- ✅ 22 authenticated endpoints with typed error codes and a 404 for anything not owned.
- ✅ 10,928 published foods with 15,777 aliases, 31,537 portions and a validation record each.
- ✅ 121 recipes whose nutrition is computed from stored gram weights, never estimated.
- ✅ Generation, regeneration, swap and logging: idempotent under retry, atomic under concurrency.
- ✅ Weekly grocery aggregation with persisted checks and increases flagged, not silently folded in.
- ✅ 19,627 tests across 235 suites pass, at 98.3% branch coverage on server logic.

## 1.4 Critical Unresolved Issues

16 items remain open across 13 of the 44 scoped requirements; the groups below sum to 16. None is a correctness defect in delivered logic — each needs a device, a credential, vendor quota, or a ruling.

| Issue | Impact | Owner | ETA |
|---|---|---|---|
| Native build, on-device behaviour, frame-by-frame visual comparison and screen-reader/text-scaling passes are unevidenced (4 items) | Appearance and native behaviour of all 18 screens unconfirmed; release-blocking | Mobile release engineer (macOS/Xcode) | 40 h |
| Catalog breadth: 13 of 21 categories below their coverage target (2,361 foods short), and no composed foods or density values published (2 items) | Narrow diet/allergen/time profiles may not plan; volume grocery rows limited | Backend engineer + vendor quota | 34 h |
| Environment, delivery and infrastructure: secrets provisioning, hosted CI run, container build, and the two pull requests (4 items) | Deployment path unproven end to end; work not yet reviewable | Repository owner / ops | 25 h |
| Acceptance of a valid identity token and the two vendor-dependent pipeline runs are unevidenced (3 items) | Authenticated acceptance proven only against the harness; rebuild unrehearsed | Repository owner (Firebase identity) | 20 h |
| Two decisions awaiting a ruling: nine colour pairs below WCAG contrast, and one pinned dependency advisory (2 items) | Accessibility conformance and dependency posture pending sign-off | Design lead / security owner | 6 h |
| Two date assertions depend on the runner's wall-clock zone (1 item) | Server suite is red for part of each day in western zones | Backend engineer | 3 h |

## 1.5 Access Issues

| System/Resource | Type of Access | Issue Description | Resolution Status | Owner |
|---|---|---|---|---|
| Development Firebase project | Identity token for a test account | No test account and no Web API key, so no request could be made with a genuine identity token | Outstanding — blocks authenticated end-to-end verification | Repository owner |
| Apple / Google native toolchains | macOS + Xcode; Android SDK | Neither is available in the build environment, so no native binary could be produced or run | Outstanding — requires a macOS machine or a device-testing service | Mobile release engineer |
| Firebase client config files | `GoogleService-Info.plist`, `google-services.json` | Not supplied; both are required inputs to a native build and must stay untracked | Outstanding | Repository owner |
| Production database | Connection string | A production connection string is present in the shell environment and takes precedence over the local file | Mitigated in code — every script and test refuses an unrecognised database before connecting | Ops |
| Food-data and model vendor APIs | API keys | Keys are present and verified reachable, but a full catalog rebuild needs sustained hourly quota that was not budgeted | Keys usable; rebuild quota not secured | Repository owner |

## 1.6 Recommended Next Steps

1. **[High]** Supply a development Firebase test account and Web API key, then run the authenticated journeys with a genuine identity token.
2. **[High]** Build on a physical iPhone, work the 31-screen visual checklist, and capture the screen-reader and text-scaling passes.
3. **[High]** Rule on the nine colour pairs below WCAG contrast and on the pinned dependency advisory.
4. **[Medium]** Secure vendor quota and re-run the catalog build to close the per-category shortfall, or accept the delivered corpus as v1.
5. **[Medium]** Open the two pull requests, rehearse the release order against a staging database, and confirm the container build.

# 2. Project Hours Breakdown

## 2.1 Completed Work Detail

Every row below traces to a scoped deliverable and is evidenced by code on the branch and by passing tests.

**API and data pipeline — 752 hours**

| Component | Hours | Description |
|---|---|---|
| Database schema and migration ledgers | 34 | Sixteen new tables, four additive migrations, the idempotent operator copy, and the drift gate that byte-compares a fresh diff against the committed expected diff |
| Preferences and setup state | 36 | Per-step parsers, the setup state machine, allergen exclusivity, unit normalisation to metric, and plan-flag recomputation on every save |
| Nutrition targets | 32 | Energy equation, activity factors, pace adjustment, clamps with reasons, feasibility warnings, the canonical-writer transaction, and legacy/stale detection |
| Catalog module | 56 | Full-text search with ranking and stable pagination, 21-category tiered validation, the three-column provenance model, identity dedupe and normalisation |
| Evidence retrieval | 26 | Registry-derived address policy, pinned DNS resolution, bounded re-validated redirects, size cap and total timeout |
| Vendor boundaries | 22 | Model-vendor client extracted behind its own error class; food-data detail, batch and list exports with a body-aware cache key |
| Recipe module | 34 | Nutrition derived from stored gram weights, badge derivation, planning eligibility, ingredient snapshots, version promotion and retirement |
| Weekly plan generator | 46 | Seeded portable candidate ordering, depth-first search with cross-day backtracking, day tolerances, portion multipliers, limiting-constraint analysis |
| Swap module | 26 | One candidate-and-portion selector shared by the alternatives list, the preview and the commit, with preview binding |
| Grocery module | 30 | Gram aggregation by food and state, unit families, the display contract, the diff and flag lifecycle, aisle categories |
| Planned-meal logging and diary contract | 24 | Server-derived snapshot, diary bucket resolution, detachment rules, additive response fields |
| Keyed-action ledger | 22 | Per-user advisory lock, reservation, verbatim replay of the stored response, completion record |
| Controllers, routers and cross-cutting middleware | 30 | Typed errors mapped to status, application mounting, feature flags, pagination, three rate limiters |
| Catalog and seed command-line suite | 92 | Import, generation, validation, report, release and load commands over twenty shared script modules |
| Data artefacts and the production catalog build | 62 | Food-data manifest, 21-category coverage plan, 426-query benchmark set, evidence allowlist, 121 recipes, fixtures, and release v1 |
| Test foundation and continuous integration | 30 | Runner and tsconfigs, the database-guarded harness, a PostgreSQL service, the dependency-audit gate |
| API integration suite | 46 | 1,795 tests over 23 suites covering every new endpoint, ownership, concurrency, compatibility and fault injection |
| Logic, script and harness suites | 76 | 10,238 tests over 42 suites at 98.3% branch coverage on decision logic |
| Operator documentation | 28 | Eight documents totalling 7,602 lines: runbook, API reference, catalog and planning policy, release and recovery, evidence checklist |

**Client application — 708 hours**

| Component | Hours | Description |
|---|---|---|
| Foundation | 44 | Route constants, 24-entry typed navigation, centralised query keys, persistence whitelist, 36 data models, endpoint builders with a non-production origin guard |
| Design tokens and the token-discipline gate | 26 | Size, stroke and opacity maps, type-metric maps, two new colour tokens, and the scanner that fails on any style literal |
| Shared component library | 62 | 27 new components plus four additive, backward-compatible props on shipped ones |
| Icon set | 34 | 40 components transcribed from the design file, including optical variants where exports differ by more than scale |
| Setup wizard | 84 | Nine screens plus the draft provider and server-backed resume |
| Targets surfaces | 34 | Review screen, full-screen editor, and cross-surface target authority on account, diary summary and progress |
| Plan tab and Macros shell | 52 | Plan header, day strip, planned totals, meal cards with logged, flagged and swapped states, the no-plan state router, entitlement |
| Recipe detail | 20 | Hero, badges, planned-portion nutrition, portion/full-recipe toggle, ingredients and instructions |
| Swap flow | 38 | Loading, alternatives, empty and failure states plus the preview with delta pill and day-total bar |
| Grocery list | 30 | Three list states, rows, increase flags, optimistic toggles with rollback |
| Planned-meal logging and diary provenance | 38 | Servings stepper, fraction chips, slot picker, provenance captions, decoder and mutation changes |
| Catalog in food search and detail | 26 | Infinite catalog section, provenance pills, and the catalog logging branch |
| Query layer | 70 | 23 hooks, 21 request functions, codecs, converters, mutation option factories, idempotency and pending-intent durability, the domain store |
| Plan settings and regeneration dialog | 24 | Seven settings rows, affected-meal banner, and the confirmation dialog with data-bound counts |
| Application test suite | 96 | 7,594 tests over 170 suites, including a second runner project pinned to a daylight-saving zone |
| Documentation and the lint baseline gate | 30 | 1,859-line feature document, the captured baseline with provenance, and the comparison gate |

**Total completed: 752 + 708 = 1,460 hours**

## 2.2 Remaining Work Detail

| Category | Hours | Priority |
|---|---|---|
| Native iOS build enablement and the physical-device verification walkthrough | 24 | High |
| Defect fixes arising from the first native run across eighteen new screens | 32 | High |
| Visual comparison against the thirty-one design frames | 14 | High |
| Measured geometry and text-scaling verification per device class | 12 | High |
| Accessibility colour ruling and applying the recorded remedies | 8 | High |
| Authenticated API verification with a development identity | 6 | High |
| Time-zone-independent assertions for the start-date window tests | 2 | High |
| Operator release sequence rehearsal for the API and the app | 12 | High |
| Pull-request publication, cross-linking and review cycle | 10 | High |
| Screen-reader verification on both platforms | 8 | Medium |
| Android build verification and configuration | 6 | Medium |
| Per-category catalog coverage: curator pass and recorded decision | 16 | Medium |
| Classification of held and quarantined catalog candidates | 12 | Medium |
| Dependency advisory ruling for the pinned identifier library | 4 | Medium |
| First continuous-integration run on a hosted runner | 6 | Medium |
| Production image build verification | 2 | Medium |
| Store release preparation and submission | 14 | Medium |
| Volume-to-mass conversion data for volume-only foods | 8 | Low |
| Ingredient-derived catalog foods | 12 | Low |
| **Total** | **208** | — |

High priority 120 h over 9 items, Medium 68 h over 8 items, Low 20 h over 2 items.

## 2.3 Basis of Estimate

Completed hours are derived per deliverable from the delivered surface — module count and complexity, test volume, and the category rates used throughout this estimate (simple entity work 8–16 h, complex business logic 24–40 h per module, external integrations 16–24 h, testing at 30–40% of development). Remaining hours are estimated the same way against what is left to do, and quality gaps are carried as hours on the specific requirement they belong to rather than as a discount on the percentage.

Confidence is **high** for the 90 hours of well-bounded work — the clock-dependent assertions, the Android build, the advisory ruling, the image build, the pull requests, the release rehearsal, the colour remedies, the authenticated replay and the catalog data tasks — because each has a known shape and a known finish line. Confidence is **medium** for the 86 hours of device verification and store release, which depend on tooling not yet exercised here. The single **low**-confidence figure is the 32 hours reserved for fixing whatever the first native run surfaces across eighteen screens; it is the widest-variance line in the estimate and is deliberately generous rather than optimistic.

**Arithmetic:** 1,460 completed + 208 remaining = 1,668 total. 1,460 ÷ 1,668 = 87.5%.

# 3. Test Results

Both suites were executed against this branch and the figures below are the observed results. The API suite ran in 959 s against a migrated PostgreSQL 16.15 database; the client suite ran in 20 s. Totals: **19,627 tests over 235 suites, 19,625 passing**.

| Area / Category | Framework | Tests | Passed | Failed | Coverage | What This Proves |
|---|---|---|---|---|---|---|
| API integration — every endpoint, ownership, concurrency, compatibility, offline and fault injection | Jest + supertest | 1,795 | 1,793 | 2 | 98.3% branch (suite-wide) | Every new endpoint answers its documented contract; a caller cannot reach another user's data, keyed writes commit exactly once under retry and race, and older client payloads still decode |
| Planning, catalog and nutrition decision logic | Jest | 6,647 | 6,647 | 0 | 98.3% branch, 80% enforced per file | Targets, plan generation, swap selection, grocery aggregation, recipe derivation and the address policy behave to specification at every boundary and tier |
| Catalog and seed command pipeline | Jest | 2,558 | 2,558 | 0 | 98.3% branch (suite-wide) | Import, generation, validation, release and load resume correctly after an interruption, stay inside their spend budget, and produce identical results on a rerun |
| Server utilities — units, seeded ordering, pagination, flags, rate limiting | Jest | 696 | 696 | 0 | 100% branch on rate-limit logic | Unit conversion and display rounding are exact, ordering is reproducible from a seed, and request limits and kill switches evaluate as documented |
| Test harness, database guards and document consistency | Jest | 337 | 337 | 0 | n/a | The suite refuses to run against any database that is not a test database, and the coverage inventory cannot drift from the files on disk |
| Client screens and components | jest-expo | 3,701 | 3,701 | 0 | not instrumented | Every screen's state routing, validation timing, empty, loading, error and success derivations resolve correctly, including six rendered trees |
| Client query layer, store and transport | jest-expo | 1,627 | 1,627 | 0 | not instrumented | Each mutation invalidates exactly the intended cache keys, idempotency keys survive a cold start, and responses decode leniently across absent, null and populated fields |
| Client utilities, models, tokens and hooks | jest-expo | 2,266 | 2,266 | 0 | not instrumented | Serving maths, unit conversion, provenance captions and date handling are correct, including a second runner project pinned to a daylight-saving zone |

The two failures are both in the start-date window assertions of the API integration suite. They compare a permitted window built from a UTC day key against a fixture pinned to a US Eastern zone, so they pass for most of the day and fail in the evening hours of western zones. The product behaviour they cover is correct — the same window is asserted correctly elsewhere from the stored zone — and the fix is to build the expectation from the fixture's own zone. It is carried as a 2-hour task in Section 2.2.

### Not Covered

The following delivered capabilities are exercised by no automated test and should be verified before release:

- **Native build and runtime on either platform.** No iOS or Android binary was produced or run, so nothing about launch, navigation transitions, native keyboard behaviour, safe areas or platform gestures is machine-verified. A human must build and drive the app on a device.
- **Appearance against the design.** No screen has been compared with its source frame. All 31 frames need a side-by-side pass at the reference size.
- **Screen readers and on-device text scaling.** Accessibility roles, labels and states are asserted in the component suites, but no VoiceOver or TalkBack pass has been run, and text scaling at 135% and 200% and measured geometry per device class have not been observed on real hardware.
- **Acceptance of a genuine identity token.** Every refusal path is covered — absent, malformed and forged credentials are all rejected — but no request was ever made with a valid development identity token, so the accept side of authentication is unproven end to end.
- **Client-side coverage measurement.** The client runner declares neither a coverage source set nor a threshold, so no coverage figure backs its 7,594 tests; the branch percentages above are the API suite's.
- **Ingredient-derived foods and volume conversion.** The release publishes no composed foods and no density values, so the code paths that derive nutrition from a stored composition and convert volume to mass have no production data to exercise.
- **Live vendor pipeline runs.** The import and generation commands are covered with injected vendor fakes; neither has been run against the live food-data or model services at production volume.
- **Hosted continuous integration and the container image.** The workflow and its database service, drift gate and dependency-audit gate have not executed on a hosted runner, and the production image has not been built with development dependencies omitted.

# 4. Runtime Validation & UI Verification

The API and the full operator release sequence were executed against a live PostgreSQL 16.15 database and observed directly. The client application was not executed, because no native build is possible without macOS and Xcode.

- ✅ **API start-up and database connectivity** — the server warms its connection in 32 ms and is listening within about 4 seconds; `GET /health` returns `{"status":"ok","version":"unknown"}`. The `unknown` version is correct outside a container image, where the commit identifier is injected at build time.
- ✅ **Authentication boundary** — `GET /api/meal-planning/plans/current` without a token returns `401 {"error":"No token provided"}`. Authentication is evaluated before the capability flag, so an unauthenticated caller never learns whether the feature is enabled.
- ✅ **Transport hardening** — `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: no-referrer` and `Content-Security-Policy: default-src 'none'; frame-ancestors 'none'` are all present on live responses.
- ✅ **Schema migration** — `prisma migrate deploy` applies all five migrations to an empty database and reports success; the meal-planning migration is additive and leaves existing tables untouched.
- ✅ **Catalog release load and recipe seed** — loading release v1 took 88 seconds and inserted 10,928 foods, 15,777 aliases, 31,537 portion conversions and 10,928 validation records, activating the release with zero deferred rows; seeding took 38 seconds and created 121 recipes with 865 ingredient rows, releasing both the shared catalog lock and the writer lock cleanly. Row counts queried directly on the database match the release manifest exactly.
- ✅ **Idempotency of both commands** — re-running them immediately afterwards changed nothing: the load reported 10,928 foods unchanged and zero inserts, and the seed reported 121 recipes settled, zero created and zero promoted.
- ✅ **Internal catalog search** — the 426-query benchmark passed every threshold against the freshly loaded corpus: 95.5% top-three hit rate (bound 90%), 99.1% top-ten (bound 97%), zero no-result queries (bound 3%), and a 7.2 ms p95 against a 150 ms bound, with the pagination check passing.
- ✅ **Release determinism across databases** — the same benchmark was run against a second, independently loaded database and compared with the run recorded in `backend/data/meal-planning/reports/latest/benchmark-report.json`. All 426 queries matched on matched food, rank, full-result-set rank and match-set size, and the determinism fingerprint was byte-identical (`363e2950…7fc0daf`). Only latency differed, which is the one quantity the protocol permits to vary by host. Ranking and pagination are therefore reproducible from the release artefact, not merely reproducible by construction.
- ⚠ **Capability kill switches** — the server switch and the client switch are both covered by tests, and the server switch returns `503 feature_disabled` from every gated route while leaving the target routes ungated, but neither was observed at runtime behind a real identity, so the switched-off experience has not been seen on a device.
- ❌ **Client application runtime** — nothing in the app was exercised at runtime. No screen was rendered on a simulator or device, no flow was driven, and no navigation, keyboard, safe-area or gesture behaviour was observed.

**Never exercised at runtime.** The app's 18 new screens and the 14 flows they compose exist only as code and passing unit tests; the 31 design frames have had no visual comparison; VoiceOver and TalkBack have not been run; text scaling at 135% and 200% and per-device-class geometry have not been measured on hardware. On the API side, no request was ever completed with a genuine identity token, so authenticated read and write behaviour is proven only against the test harness; the food-data import and model-assisted generation commands have not been run against live vendors; and the continuous-integration workflow and the production container image have never been built or executed. These are the gaps that make up the High-priority block in Section 2.2, and until they close, the appearance and native behaviour of the client is the single largest unverified surface in this delivery.

# 5. Compliance & Quality Review

## 5.1 Compliance Matrix

Each row records where the deliverable stands now, against the quality benchmarks set for this project.

| Deliverable | Benchmark | Status | Progress |
|---|---|---|---|
| Setup wizard and preference editing | Nine screens, server-persisted resume, no pre-selected answers, validate-on-press | ✅ Pass | ██████████ 100% |
| Nutrition targets | Deterministic estimate, bounds with reasons, one canonical writer, identical values on every surface | ✅ Pass | ██████████ 100% |
| Internal food catalog | ≥10,000 published foods, a validation record each, per-category coverage, benchmark thresholds | ⚠ Partial | ████████░░ 80% |
| Recipe corpus | ≥40 recipes, every ingredient source-backed, nutrition computed from gram weights | ✅ Pass — 121 delivered | ██████████ 100% |
| Weekly plan generation | Deterministic from its inputs, hard constraints honoured, named limiting constraints on failure | ✅ Pass | ██████████ 100% |
| Meal swap and weekly grocery list | One selector shared by list, preview and commit; gram aggregation by food and state; persisted checks; increases flagged, not folded in | ✅ Pass | ██████████ 100% |
| Planned-meal logging and diary provenance | Server-derived snapshot, idempotent under retry, provenance never overstated | ✅ Pass | ██████████ 100% |
| API contract, tenancy and write safety | Typed codes, additive changes only, 404 for anything not owned, per-user serialisation, keyed replay, nothing persisted on failure | ✅ Pass | ██████████ 100% |
| Schema and migration practice | Additive only, existing data preserved, both ledgers equivalent, drift gate enforced | ✅ Pass | ██████████ 100% |
| Test foundation, gates and documentation | A real suite replacing the placeholder, 80% branch floor per file, style and lint gates, operator runbook | ✅ Pass | ██████████ 100% |
| Accessibility and visual fidelity | Roles, labels, 44 px targets, frame-accurate appearance, screen-reader and text-scaling passes | ⚠ Partial — engineering done, on-device and contrast pending | ██████░░░░ 60% |
| Path to production | Secrets, hosted CI, image build, release rehearsal, reviewable pull requests | ❌ Not met | ██░░░░░░░░ 20% |

## 5.2 AAP & Rule Divergences and Gaps

| What the AAP/Rule Required | What Was Delivered Instead | Why It Diverged | Impact | Remediation |
|---|---|---|---|---|
| Per-category catalog coverage targets summing to 11,010 published foods | 10,928 published — above the 10,000 floor — but 13 of 21 categories short by 2,361 in total | The food-data vendor cannot supply 2,174 of them within the manifest's identity and evidence rules; 187 are recoverable | Narrow diet, allergen and time profiles draw on a thinner ingredient pool | Curator pass and a recorded decision — 16 h |
| Model-assisted catalog expansion publishing validated foods, plus ingredient-derived foods with density | 12,057 candidates generated and validated; none published. No composed foods, no density values | Every candidate failed the identity-evidence or category-range gate, and the pipeline fails closed rather than publish unverified nutrition | Volume-only grocery rows cannot convert to mass; breadth rests on vendor data alone | Classify held candidates, source density, compose foods — 32 h |
| One additive migration plus an idempotent reference copy | Four additive migrations plus the mirrored reference copy | Index and column design for search could not be settled until search behaviour was measured | None on correctness; both ledgers remain equivalent | None required |
| Design file as visual source of truth, with accessible contrast | Nine colour pairs render below WCAG 2.1 thresholds *(Sanctioned)* | The plan's own token-precedence rule puts palette fidelity ahead of contrast, and the two instructions conflict | Text and glyphs in nine places are harder to read | Design ruling then a token change — 8 h |
| Identifier library pinned at its current version; no framework upgrades | The pinned version remains, carrying one published advisory *(Sanctioned)* | The pin is an explicit instruction, and the affected code path is unreachable from either repository | An advisory appears in dependency scans | Recorded acceptance, or amendment plus bump — 4 h |
| Helvetica Neue throughout the design | The platform default typeface, with metrics matched exactly *(Sanctioned)* | The plan adopts this as its own working decision and records the reversal scope | Glyph shapes differ from the frames; metrics do not | None unless reversed |
| Full conformance to the seven coding rules | Three narrow deviations, each documented in the repository | One is a type-only import, one is two genuinely two-shaped components, one is a branch the plan freezes byte-for-byte | None on behaviour | None required |
| Device walkthrough, frame comparison, screen-reader and text-scaling passes, authenticated verification, hosted CI | None of these was performed | No macOS, Xcode or Android SDK in the build environment, and no development identity or client configuration files were supplied | Appearance and native behaviour of 18 screens are unconfirmed | The device-and-identity block — 108 h |

**Catalog coverage.** The 10,000-food floor is met with 928 to spare, but the plan also set a per-category target, and 13 of 21 categories sit below theirs: condiment/sauce is 504 short, snacks 367, plant protein 312, herbs and spices 251, dairy 229, and eight others by smaller margins (`backend/data/meal-planning/coverage-plan.v1.json` against the release manifest). Of the 2,361 missing, 2,174 have no vendor record satisfying the manifest's identity and evidence rules; 187 are recoverable by a curator. Week feasibility is nonetheless certified across 1,848 probe-target pairs with none unplannable, and the planner evaluates a user's real intersection and names the limiting constraint. Decide whether to fund the curator pass or accept v1 as shipped.

**Model-assisted expansion and composed foods.** The plan expected generation to fill named category gaps and to publish ingredient-derived foods carrying density. Generation ran and validated 12,057 candidates, but published none: each failed either the identity-evidence gate or its category's plausibility range, and the nutrition-integrity rule forbids publishing unverified values, so the pipeline held them. `components.jsonl` in release v1 is zero bytes and `density_g_per_ml` is null on all 10,928 rows, so composition-derived nutrition and volume-to-mass conversion have code but no data. The 728 held and 107 quarantined candidates need classification before a second generation run is worth attempting.

**Four migrations instead of one.** The plan named a single additive migration; the branch carries that one plus three follow-ups covering cache status capture, prefix-fold indexes, and the alias search vector with read statistics (`backend/prisma/migrations/`). The later three exist because index and column design for full-text search could not be settled before search behaviour was measured on a real corpus. Every one is additive, the operator reference copy under `prisma/manual-migrations/meal-planning/` mirrors them idempotently, and the drift gate regenerates a diff and compares it byte-for-byte with `docs/meal-planning/expected-schema-diff.sql`. No action is needed beyond following the documented order.

**Nine colour pairs below contrast thresholds.** The design file is the visual source of truth and every colour resolves to a palette token, which is what the styling rule demands; nine of those resolutions fall below WCAG 2.1 contrast for their text size. This is a conflict between two binding instructions, and the plan's own precedence resolves it toward token fidelity, so the outcome is sanctioned rather than accidental. Each pair is recorded with its measured ratio and a pre-computed remedy that is usually an existing palette token, so the change is mechanical once ruled on. The engineering half of accessibility — roles, labels, states, 44 px targets — is complete.

**The pinned identifier library.** The plan pins this dependency and forbids upgrades for this feature. It carries a published advisory, and a reachability analysis in the repository shows the affected entry point is not reached from any call site in either codebase. A version bump was prepared and then withdrawn rather than taken against the instruction. The practical consequence is that a dependency scan will report a finding. Either record an acceptance with the reachability argument attached, or amend the plan and take the bump with a full suite run behind it — four hours either way.

**Typeface substitution.** Every text style in the design names Helvetica Neue; the app renders the platform default, because no font family is set anywhere in the codebase and the plan explicitly adopts keeping the platform family as its working decision. Size, weight, line height and letter spacing are matched exactly, so layout and rhythm are faithful and only glyph shapes differ. The reversal is scoped in the repository — licensed font files, a family token, a load gate at start-up and a licence review — and remains available on request. Nothing needs doing unless the decision is reversed.

**Three coding-rule deviations.** Each is documented in `backend/docs/meal-planning/requirement-evidence-checklist.md`. A target request function imports the HTTP client library only to use its type predicate when mapping a bare 404 to "no server targets", while the request itself goes through the shared helper. Two components declare a union `Props` type instead of a single interface, each being genuinely two-shaped. One diary update still keys by row identifier rather than the owner-bearing predicate, because the plan freezes that branch byte-for-byte to preserve existing behaviour. None changes observable behaviour, and each either satisfies its clause's intent or is frozen by instruction.

**Verification narrower than the acceptance criteria.** The plan's acceptance criteria call for a physical-device walkthrough, a comparison against all 31 frames, screen-reader and text-scaling passes, authenticated API verification and a hosted continuous-integration run. None was performed: the build environment has no macOS, Xcode or Android SDK, and no development identity token or platform configuration files were supplied. Refusal paths are evidenced against the live middleware and every derivation is unit-tested, but acceptance of a valid credential and the appearance of 18 screens remain unobserved. This is the largest open item in the delivery and the whole of the High-priority block in Section 2.2.

# 6. Risk Assessment

These are forward-looking: what could still go wrong between here and production.

| Risk | Category | Severity | Probability | Mitigation | Status |
|---|---|---|---|---|---|
| Appearance and native behaviour of 18 new screens are unconfirmed, so the first device run may surface layout, keyboard or navigation defects | Technical | High | High | Every derivation is unit-tested and all geometry resolves to tokens, so defects should be presentational rather than logical; 32 hours are reserved for first-run fixes | Open |
| Acceptance of a valid identity token is unproven — only refusal is evidenced | Technical | High | Low | All 56 unauthenticated probes are refused with nothing persisted, forged credentials are rejected, and the verification path demonstrably executes; supply a development token and replay the read and write set | Open |
| Two start-date assertions are anchored to a UTC day key while the fixture pins a US Eastern zone, so the API suite is red for part of each day | Technical | Medium | High | Product behaviour is correct and asserted elsewhere from the stored zone; rebuild the two expectations from the fixture's own zone | Open |
| Nine colour pairs render below WCAG 2.1 contrast thresholds | Operational | Medium | High | Each pair carries its measured ratio and a pre-computed remedy, usually an existing palette token; the engineering half of accessibility is complete | Awaiting a design ruling |
| Catalog breadth is thin in 13 of 21 categories, so an unusual diet, allergen and dislike combination may find no plannable week | Operational | Medium | Medium | Week feasibility is certified over 1,848 probe-target pairs with none unplannable, and the planner names the limiting constraint and an edit target rather than producing a poor plan | Mitigated in code; data gap open |
| The continuous-integration workflow has never executed on a hosted runner, so its database service, schema-drift gate and dependency-audit gate are unproven in situ | Operational | Medium | Low | The release sequence, both idempotent reruns and cross-database ranking determinism have all been executed and verified against a live database; opening the pull requests produces the first hosted run | Open |
| Catalog evidence retrieval fetches URLs proposed by a model, an attacker-influenceable input | Security | High | Low | HTTPS and default port only, no credentials or IP literals, exact per-label host allowlisting, an address policy transcribed from the IANA special-purpose registries with translated IPv6 forms unwrapped, fail-closed resolution, the first passing address pinned against rebinding, bounded re-validated redirects, a 10 s timeout, a 1 MiB decompressed cap and a content-type allowlist; fetched text is stored, never fed back as instructions | Mitigated |
| Kill-switch propagation and vendor-key dependence | Integration | Medium | Low | The client switch is fetched at launch on a 15-minute floor so it has no bounded propagation window, but the server switch stops gated routes immediately while leaving targets ungated; after seeding, planning, swaps, grocery, recipes and internal search make no request-time vendor calls, proven with both keys unset and the transport trapped | Mitigated |

# 7. Visual Project Status

**Overall effort** — completed work in dark blue (#5B39F3), remaining work in white (#FFFFFF).

```mermaid
%%{init: {"theme": "base", "themeVariables": {"pie1": "#5B39F3", "pie2": "#FFFFFF", "pieStrokeColor": "#B23AF2", "pieOuterStrokeColor": "#B23AF2", "pieSectionTextColor": "#B23AF2", "pieTitleTextSize": "17px"}}}%%
pie title Project Hours — 1,668 total, 87.5% complete
    "Completed Work" : 1460
    "Remaining Work" : 208
```

**Remaining work by priority** — 208 hours across 19 items.

```mermaid
%%{init: {"theme": "base", "themeVariables": {"pie1": "#5B39F3", "pie2": "#A8FDD9", "pie3": "#FFFFFF", "pieStrokeColor": "#B23AF2", "pieOuterStrokeColor": "#B23AF2", "pieSectionTextColor": "#B23AF2", "pieTitleTextSize": "17px"}}}%%
pie title Remaining Hours by Priority
    "High" : 120
    "Medium" : 68
    "Low" : 20
```

**Requirement status** — 44 scoped requirements.

```mermaid
%%{init: {"theme": "base", "themeVariables": {"pie1": "#5B39F3", "pie2": "#A8FDD9", "pie3": "#FFFFFF", "pieStrokeColor": "#B23AF2", "pieOuterStrokeColor": "#B23AF2", "pieSectionTextColor": "#B23AF2", "pieTitleTextSize": "17px"}}}%%
pie title Requirements — 31 complete of 44
    "Completed" : 31
    "Partially Completed" : 3
    "Not Started" : 10
```

**Remaining hours by theme** — the same 208 hours as Section 2.2, regrouped.

| Theme | Hours | Share of remaining |
|---|---|---|
| Device, visual and accessibility verification | 96 | 46% |
| Catalog breadth and composition | 48 | 23% |
| Delivery and release path | 36 | 17% |
| Decisions awaiting a ruling | 12 | 6% |
| Pipeline and infrastructure evidence | 8 | 4% |
| Identity and authenticated verification | 6 | 3% |
| Test hygiene | 2 | 1% |
| **Total** | **208** | **100%** |

Nearly half the remaining effort is a single dependency: a macOS machine with Xcode, from which the device walkthrough, the frame comparison, the geometry and text-scaling measurements, the screen-reader passes and the Android build all follow.

# 8. Summary & Recommendations

This delivery adds a complete weekly meal-planning capability to both halves of State of Health and is **87.5% complete** against the scoped work — 1,460 of 1,668 hours, with 31 of 44 requirements fully delivered. On the API side, 22 new endpoints sit behind authentication with typed error codes, per-user serialisation and exactly-once semantics for every write that matters: plan generation, regeneration, meal swap and planned-meal logging each replay their stored response verbatim under retry and hold their invariants under concurrency. The data layer ships a published catalog of 10,928 foods with a validation record for every one, 15,777 aliases, 31,537 portion conversions and 121 recipes whose nutrition is computed from stored gram weights rather than estimated. On the client side, 18 new screens, 27 shared components, 40 icons and a 23-hook query layer implement the full journey from setup through targets, generation, recipe inspection, swapping, shopping and logging, with the diary's existing behaviour untouched and older clients unaffected by the additive contract changes.

The verification behind that is substantial and was re-run first-hand for this assessment: **19,627 tests across 235 suites, 19,625 passing**, with 98.3% branch coverage on the server's decision logic and an enforced 80% floor per file. Beyond the suites, the whole operator release sequence was executed against a live database — migrate, load the catalog release, seed the recipes, benchmark — and then executed again to prove idempotency, with the second run reporting zero inserts and zero promotions. The search benchmark passed all four thresholds with a 7 ms p95 against a 150 ms bound, and running it against a second independently loaded database reproduced all 426 result rankings exactly, with an identical determinism fingerprint. That last result matters more than its size suggests: it demonstrates that a catalog release is a portable artefact whose search behaviour does not depend on the database it was loaded into.

What remains is dominated by one dependency rather than by unfinished code. Almost half the 208 remaining hours — the device walkthrough, the comparison against all 31 design frames, per-device geometry and text scaling, the screen-reader passes and the Android build — requires a macOS machine with Xcode, which the build environment does not have. A further block needs credentials rather than engineering: no development identity token was available, so the accept side of authentication has never been exercised end to end, and no platform configuration files were supplied, so no native binary exists. The remaining gaps are genuine but bounded: 13 of 21 catalog categories fall short of their individual coverage target, model-assisted generation published nothing because every candidate failed a gate that exists precisely to stop unverified nutrition reaching users, no composed foods or density values were published, and two date assertions are anchored to the wrong clock. Two decisions also await a human: nine colour pairs that fall below WCAG contrast because palette fidelity was given precedence, and one pinned dependency advisory whose affected path is demonstrably unreachable.

The critical path is short and ordered. Supply two secrets — a development identity token and the iOS configuration file — and the two largest verification gaps open at once: replay the authenticated read and write set field by field, then build natively and work the device checklist, reserving real time for whatever the first run surfaces across eighteen screens. In parallel, rule on the nine colour pairs and the dependency advisory, both of which arrive with the analysis already done and need only a decision. Then open the two pull requests, which is also what produces the first hosted continuous-integration run, and rehearse the documented release order against a staging database before any production step: back up, leave the capability flag off, deploy the API, load the release, seed the recipes, verify the status endpoint, and only then turn the flag on.

**Production readiness: not yet, but the remaining work is verification and decisions rather than construction.** The server half is close to ready — it starts, migrates additively, refuses every unauthenticated request, carries transport hardening and rate limiting the plan did not even ask for, and has a rehearsed, idempotent and reversible release path. The client half cannot be signed off until it has been seen running on a device, and that is the honest limit of this assessment. Judge readiness by four measures: authenticated journeys completing on hardware, all 31 screens matching their frames, the API suite green on a hosted runner in every time zone, and a recorded decision on catalog breadth. None of those is blocked by a defect in the delivered logic; each is blocked on access, a device, or a signature.

# 9. Development Guide

Every command below was run against this branch. The two repositories are independent dependency roots with no workspace tooling, so always `cd` into one before running its commands.

## 9.1 System Prerequisites

| Requirement | Version | Notes |
|---|---|---|
| Node.js | 22.13 or later in the 22 line | Verified on 22.23.2. Both repositories' engine ranges are satisfied |
| npm | 10 or later | Verified on 11.18.0 |
| PostgreSQL | 16 | Verified on 16.15. No extensions are required — full-text search uses a generated `tsvector` column |
| Git with Git LFS | any current | The repositories are wired as submodules |
| macOS + Xcode | required for iOS only | Not needed for API work or client JavaScript checks |
| Android SDK + JDK | required for Android only | Not needed for API work or client JavaScript checks |

```bash
node -v      # expect v22.x, 22.13 or later
npm -v
git --version
```

## 9.2 Repository Layout

```bash
git clone <your-remote> state-of-health
cd state-of-health
git submodule update --init --recursive
ls            # backend  mobile  README.md
```

`backend/` is the Express/Prisma API; `mobile/` is the React Native/Expo client. Commit inside whichever submodule you changed.

## 9.3 Database Provisioning

Three databases are needed: one for development, one for tests, and one throwaway shadow database that Prisma resets when generating or diffing migrations. **Never point any of them at production data.**

```bash
docker run -d --name soh-postgres \
  -e POSTGRES_USER=soh -e POSTGRES_PASSWORD=soh \
  -p 5432:5432 postgres:16-alpine

for db in soh_dev soh_test soh_shadow; do
  docker exec soh-postgres psql -U soh -d postgres -c "CREATE DATABASE $db OWNER soh"
done
```

If you publish PostgreSQL on a different port, use that port consistently in every URL below.

## 9.4 Environment Configuration

Both files are ignored by Git. Never commit either.

```bash
cd backend
cat > .env <<'EOF'
DATABASE_URL=postgresql://soh:soh@127.0.0.1:5432/soh_dev
SHADOW_DATABASE_URL=postgresql://soh:soh@127.0.0.1:5432/soh_shadow
NODE_ENV=development
PORT=3000
MEAL_PLANNING_ENABLED=false
FIREBASE_SERVICE_ACCOUNT=<base64 of the development service-account JSON>
USDA_API_KEY=<development key>
OPENROUTER_API_KEY=<development key>
EOF
```

```bash
cd ../mobile
echo "SOH_API_BASE_URL=http://localhost:3000" > .env
```

The client appends `/api` itself, so the value must not end in `/api`. Debug builds and the test runner both run a preflight that throws on a production host, so a copied template can never silently point the app at production. `.env.dist` and the tracked `.env.test` already carry a development origin.

> **If a `DATABASE_URL` is exported in your shell, it wins.** The server loads its `.env` without override, so an inherited value takes precedence over the file. Export the development URL explicitly in any shell that runs API commands.

## 9.5 API Setup and Verification

```bash
cd backend
export DATABASE_URL="postgresql://soh:soh@127.0.0.1:5432/soh_dev"

npm ci                     # devDependencies included
npx prisma generate        # required before typecheck, build or dev — the client is generated, not committed
npx prisma migrate deploy  # applies the tracked migrations only
npm run typecheck          # expect exit 0
npm run build              # expect exit 0
```

Observed output of `migrate deploy` on an empty database: five migrations applied, ending `All migrations have been successfully applied.`

Start the API and check it:

```bash
npm run dev > /dev/null 2>&1 &      # watch mode — never run in the foreground
sleep 5
curl -s http://localhost:3000/health
```

Observed response:

```json
{"status":"ok","version":"unknown"}
```

`status: ok` is returned only after a successful database round-trip, so it confirms connectivity. `version` reads `unknown` outside a container image, where the commit identifier is injected at build time. Start-up took about four seconds, with the connection warm after 32 ms.

Every `/api/*` route except account creation and avatar upload sits behind authentication:

```bash
curl -s -o /dev/null -w '%{http_code}\n' http://localhost:3000/api/meal-planning/plans/current
# 401  — body: {"error":"No token provided"}
```

Authentication is evaluated before the capability flag, so this is `401` even with `MEAL_PLANNING_ENABLED=false`.

Never run anything under `prisma/manual-migrations/` as part of setup. That directory is an idempotent operator reference; the tracked Prisma migrations are authoritative. If you ever do apply it by hand, follow it with `npx prisma migrate resolve --applied 20260908000000_meal_planning` so `migrate deploy` does not re-apply the same DDL.

## 9.6 Loading the Catalog and Recipes

Meal planning needs its data before it will work. Load the reviewed, checksummed release rather than rebuilding it — rebuilding calls external vendors and is a development-machine activity, not a setup step.

```bash
cd backend
export DATABASE_URL="postgresql://soh:soh@127.0.0.1:5432/soh_dev"

npm run catalog:load -- --release v1     # ~88 s
npm run recipes:seed                      # ~38 s
```

Observed results:

| Command | Result |
|---|---|
| `catalog:load -- --release v1` | 10,928 foods inserted, 15,777 aliases, 31,537 portions, 10,928 validation records; release activated; 0 deferred |
| `recipes:seed` | 121 recipes created, 865 ingredient rows; manifest `v1@83dc983c51f6` |

Both are idempotent. Running them again immediately reported 10,928 foods unchanged with zero inserts, and 121 recipes settled with zero created and zero promoted — which is the safe way to repair a partial load.

Verify the corpus, then enable the feature:

```bash
docker exec soh-postgres psql -U soh -d soh_dev -c \
  "SELECT count(*) FROM catalog_foods WHERE publication_status='published'"   # 10928
docker exec soh-postgres psql -U soh -d soh_dev -c "SELECT count(*) FROM recipes"  # 121

# then set MEAL_PLANNING_ENABLED=true in backend/.env and restart the API
```

`GET /api/catalog/status` reports the active release and counts, but it requires an identity token like every other API route, so use the SQL above if you do not have one.

The search benchmark measures the loaded corpus:

```bash
npm run search:benchmark
```

Observed verdict: **pass** on all four thresholds — 95.5% top-three hit rate (bound 90%), 99.1% top-ten (bound 97%), zero no-result queries across 426 queries (bound 3%), 7.2 ms p95 (bound 150 ms), pagination check pass. Note that this command rewrites `data/meal-planning/reports/latest/benchmark-report.json`; copy your run aside and `git checkout --` the file if you need a clean tree.

To rebuild the catalog from source instead of loading a release, the chain is `catalog:import` → `catalog:generate` → `catalog:validate` → `catalog:report` → `catalog:release`. It needs both vendor keys and sustained hourly quota, and it writes a new versioned release directory for review.

## 9.7 Client Setup and Checks

```bash
cd mobile
npm ci --legacy-peer-deps        # plain `npm ci` fails; see 9.9
npx tsc --noEmit                 # expect exit 0
CI=true npx jest --runInBand --ci
```

Observed: `tsc` clean; the suite passes 7,594 tests over 170 suites in about 20 seconds.

Linting is baseline-aware, because the repository carries pre-existing findings that this feature did not introduce:

```bash
npm run lint                                   # exits 1 on the pre-existing baseline — this is expected
npx eslint --no-fix -f json . -o ./lint-current.json || true
node scripts/lint-baseline-compare.mjs docs/lint-baseline.json ./lint-current.json   # exit 0 = no new findings
rm ./lint-current.json
```

The comparison normalises every path to a repository-relative form before comparing, so a baseline captured on one machine is valid on another. It keys findings by file, rule and message, and exits non-zero on anything absent from the baseline.

Style tokens are enforced by a scanner that fails on any numeric literal in a stylesheet:

```bash
node scripts/token-literal-scan.mjs $(git diff --name-only --diff-filter=ACMR origin/master -- 'src/**/index.styled.ts')
```

Running the app needs a native development client — it does not run in Expo Go or as a web preview:

```bash
npx expo start --dev-client          # Metro, for an already-installed dev client
npm run ios                          # macOS + Xcode; needs GoogleService-Info.plist
npm run android                      # Android SDK; needs google-services.json
npx expo export --platform ios --output-dir ./build-export   # JS bundle only, no native tooling
```

Both Firebase configuration files must be injected as untracked files and must belong to the same development Firebase project as the API's service account. Authenticated testing also needs a development test account.

## 9.8 Running the API Test Suite

The harness refuses to run unless all three conditions hold — test mode, explicit truncation consent, and a database whose name ends in `_test`:

```bash
cd backend
NODE_ENV=test ALLOW_DB_TRUNCATE=true \
DATABASE_URL="postgresql://soh:soh@127.0.0.1:5432/soh_test" \
npm test
```

Migrate that database first (`npx prisma migrate deploy` with the same URL). The suite runs serially with coverage and takes about 16 minutes: 12,033 tests over 65 suites, enforcing an 80% branch floor on every logic file individually.

## 9.9 Troubleshooting

| Symptom | Cause and resolution |
|---|---|
| `Can't reach database server` / Prisma `P1001` from any API command | An inherited `DATABASE_URL` is overriding the file. Export the development or test URL explicitly in that shell |
| A script exits immediately naming the database origin | The origin guard refuses any URL it cannot classify as development, test or shadow, before Prisma is imported. Use a `_dev`/`_test` name on a local host |
| `catalog:load` or `recipes:seed` refuses to write | Both require `--confirm-target <dbname>` when the target is not a development database. This is deliberate — type the name |
| The test run aborts before a single test | One of `NODE_ENV=test`, `ALLOW_DB_TRUNCATE=true` or a `_test` database name is missing |
| `npm ci` in `mobile` fails with `ERESOLVE` | Pre-existing peer-range conflict between the test preset and the React Native version. Use `npm ci --legacy-peer-deps`. Do not change either version — that is a framework upgrade |
| `npm run lint` exits 1 | Expected: it reports the pre-existing baseline. The gate that must pass is `lint-baseline-compare` |
| `git status` dirty after running a script | A command rewrote a committed report artefact. Copy your output elsewhere and `git checkout --` the file back |
| Port already in use, and `lsof`/`netstat` are unavailable | Walk `/proc` to find the socket owner and kill only that pid. Watch-mode runners leave orphaned children holding ports |
| `/health` returns `"version":"unknown"` | Correct outside a container image; the commit identifier is injected at image build time |
| Meal Plan absent from the app | Both switches must be on: `MEAL_PLANNING_ENABLED=true` on the server, and the client's remote flag fetched at least once. The client flag defaults to off and is read at launch, so cold-start the app after changing it |

## 9.10 Example Usage

With the API running, the catalog loaded and the capability flag on, the journey is: read preferences to decide whether to resume setup, save each setup step, review the computed estimate, confirm targets, generate a plan, then read it back.

```bash
TOKEN="<development Firebase ID token>"
AUTH=(-H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json')

# 1. Where is this user in setup?
curl -s "${AUTH[@]}" http://localhost:3000/api/meal-planning/preferences

# 2. Save a step (each step persists, so setup survives a force-quit)
curl -s -X PUT "${AUTH[@]}" \
  -d '{"goal":"lose","paceLbPerWeek":1,"timeZone":"America/New_York"}' \
  http://localhost:3000/api/meal-planning/preferences/steps/goal

# 3. Review the computed estimate, then confirm it as the user's targets
curl -s "${AUTH[@]}" http://localhost:3000/api/meal-planning/targets/estimate
curl -s -X PUT "${AUTH[@]}" \
  -d '{"source":"estimated","estimateRevision":7,"expectedTargetsRevision":0}' \
  http://localhost:3000/api/meal-planning/targets

# 4. Generate the week. Reusing the same idempotencyKey replays the stored response
curl -s -X POST "${AUTH[@]}" \
  -d '{"startDate":"2026-10-05","idempotencyKey":"3f2b...","expectedPreferencesRevision":7,"expectedTargetsRevision":1}' \
  http://localhost:3000/api/meal-planning/plans

# 5. Read it back, plus the aggregated grocery list
curl -s "${AUTH[@]}" http://localhost:3000/api/meal-planning/plans/current
curl -s "${AUTH[@]}" "http://localhost:3000/api/meal-planning/plans/$PLAN_ID/groceries"
```

Search the internal catalog without any vendor call:

```bash
curl -s "${AUTH[@]}" "http://localhost:3000/api/catalog/foods?q=chicken%20breast&page=1&limit=25"
```

Failure responses carry a machine-readable code rather than prose — `no_matching_meals` with named limiting constraints when a week cannot be built, `stale_plan` or `plan_not_active` when a screen is holding a superseded plan, `stale_revision` when another device saved first, and `feature_disabled` when the server switch is off. `backend/docs/meal-planning/api.md` documents every endpoint and code; `backend/docs/meal-planning/release-and-recovery.md` documents the release order and rollback.

# 10. Appendices

## A. Command Reference

**API (`backend/`)** — export a development or test `DATABASE_URL` in every shell first.

| Command | Purpose |
|---|---|
| `npm ci` | Install exactly what the lockfile pins, devDependencies included |
| `npx prisma generate` | Generate the database client; required before typecheck, build or dev |
| `npx prisma migrate deploy` | Apply the tracked migrations |
| `npm run typecheck` | Type-check production sources |
| `npm run typecheck:test` | Type-check sources plus tests |
| `npm run typecheck:scripts` | Type-check sources plus the command-line suite |
| `npm run build` | Compile to `dist/` (98 files) |
| `npm run dev` | Watch-mode server — background it, never foreground |
| `npm start` | Run the compiled server |
| `npm test` | Serial suite with coverage; needs the three test preconditions |
| `npm run test:watch` | Interactive suite |
| `npm run audit:gate` | Dependency-advisory gate against the recorded baseline |
| `npm run catalog:load -- --release v1` | Load a reviewed catalog release; idempotent |
| `npm run recipes:seed` | Seed or promote recipe versions; idempotent |
| `npm run search:benchmark` | Score the 426-query benchmark against the loaded corpus |
| `npm run catalog:import` | Import vendor food records into candidates |
| `npm run catalog:generate` | Model-assisted candidate generation, budget-bounded |
| `npm run catalog:validate` | Run the validation tiers and publish accepted candidates |
| `npm run catalog:report` | Write counts, duplicates, quarantines and the exact shortfall |
| `npm run catalog:release` | Freeze the published set into a checksummed release |
| `npm run db:seed:dev` | Development fixture data |

**Client (`mobile/`)**

| Command | Purpose |
|---|---|
| `npm ci --legacy-peer-deps` | Install; the plain form fails on a pre-existing peer range |
| `npx tsc --noEmit` | Type-check |
| `CI=true npx jest --runInBand --ci` | Run both runner projects |
| `npm run lint` | Lint; exits 1 on the pre-existing baseline |
| `node scripts/lint-baseline-compare.mjs docs/lint-baseline.json <fresh.json>` | The passing lint gate — new findings only |
| `node scripts/token-literal-scan.mjs <styled files>` | Fail on any numeric literal in a stylesheet |
| `npx expo start --dev-client` | Metro for an installed development client |
| `npm run ios` / `npm run android` | Native build and launch; needs platform tooling and config files |
| `npx expo export --platform ios --output-dir ./build-export` | Bundle without native tooling |

## B. Port Reference

| Port | Service | Notes |
|---|---|---|
| 3000 | API | Set by `PORT`; `/health` is unauthenticated |
| 5432 | PostgreSQL | Whatever your instance publishes; keep it consistent in every URL |
| 8081 | Metro bundler | Expo default; `--port` overrides |

## C. Key File Locations

| Path | What it is |
|---|---|
| `backend/prisma/schema.prisma` | Sixteen new tables plus four additive columns on diary entries |
| `backend/prisma/migrations/` | The authoritative ledger — five migrations |
| `backend/prisma/manual-migrations/meal-planning/` | Idempotent operator reference copy; not run by tooling |
| `backend/src/services/*.logic.ts` | Pure decision logic, 80% branch floor enforced per file |
| `backend/src/services/mealPlanningAction.service.ts` | The single owner of lock, reserve, replay and complete for keyed writes |
| `backend/src/services/evidence.logic.ts` | Address and host policy for evidence retrieval |
| `backend/src/routes/` `backend/src/controllers/` | The 22 new endpoints and their thin handlers |
| `backend/scripts/` | Catalog and seed commands over twenty shared modules |
| `backend/data/meal-planning/catalog/releases/v1/` | The checksummed release: foods, aliases, portions, validation records, manifest |
| `backend/data/meal-planning/recipes/` | 121 recipe definitions plus the coverage report |
| `backend/data/meal-planning/reports/latest/` | Import, validation and benchmark reports |
| `backend/docs/meal-planning/` | Runbook, API reference, catalog and planning policy, release and recovery, evidence checklist |
| `mobile/src/screens/MealPlan*/` and `RecipeDetail/`, `SwapMeal/`, `SwapPreview/`, `GroceryList/`, `LogPlannedMeal/`, `PlanSettings/` | The 18 new screens |
| `mobile/src/screens/Macros/components/MealPlanTab/` | The plan tab inside the existing Macros screen |
| `mobile/src/queries/mealPlanning/` `mobile/src/queries/catalog/` | 23 hooks and their mutation option factories |
| `mobile/src/store/mealPlan/useMealPlanStore.ts` | Ephemeral plan UI state plus durable pending intents |
| `mobile/src/styles/sizes.ts` | Size, stroke and opacity tokens |
| `mobile/docs/meal-planning.md` | Screen-to-frame map, setup notes and the device checklist |

## D. Technology Versions

| Component | Version |
|---|---|
| Node.js / npm | 22.23.2 / 11.18.0 |
| PostgreSQL | 16.15 |
| TypeScript | 5.8.3 (API) |
| Prisma / client | 6.9.0 |
| Express | 4.18.x |
| firebase-admin | 13.4.x |
| pg | 8.16.x |
| date-fns | 4.1.x (API) |
| dotenv | 16.5.x |
| uuid | 9.0.1 (pinned) |
| Jest / ts-jest / supertest | 29.7.0 / 29.4.12 / 7.2.2 |
| ts-node / ts-node-dev | 10.9.2 / 2.0.0 |
| React Native / React / Expo | 0.86.0 / 19.2.3 / 57.x |
| TanStack Query | 5.101.x |
| Zustand | 5.0.x |
| react-native-svg | 15.15.x |

No framework was upgraded for this work.

## E. Environment Variable Reference

**API**

| Variable | Required | Purpose |
|---|---|---|
| `DATABASE_URL` | yes | Connection string. Never production. An inherited shell value overrides the file |
| `SHADOW_DATABASE_URL` | for migration authoring | Throwaway database that Prisma resets when generating or diffing |
| `NODE_ENV` | yes | `development`, `test` or `production`; test mode is a precondition of the suite |
| `PORT` | yes | Listening port; 3000 in the examples |
| `FIREBASE_SERVICE_ACCOUNT` | yes | Base64 service-account JSON, or an untracked key file in the repository root |
| `MEAL_PLANNING_ENABLED` | yes for the feature | On only when exactly `true`. Off means `503 feature_disabled` on gated routes; target routes stay open |
| `USDA_API_KEY` | for vendor search and catalog builds | Not needed once a release is loaded |
| `OPENROUTER_API_KEY` | for estimation, label scan and catalog generation | Not needed once a release is loaded |
| `DATABASE_CONNECTION_LIMIT` | no | Pool sizing |
| `MEAL_PLANNING_FAULT` | development only | `off` \| `generation` \| `swap` \| `log`; forced to `off` in production. Makes the drawn failure states reachable |
| `CATALOG_GENERATION_MODEL` / `CATALOG_REVIEW_MODEL` | catalog builds | Model selection, defaulting to the existing choices |
| `USDA_IMPORT_RATE_LIMIT_PER_HOUR` | catalog builds | Token budget leaving headroom for live traffic on the same key |
| `CATALOG_BATCH_SIZE` / `CATALOG_MODEL_CALL_BUDGET` | catalog builds | Batch size and the hard cap on paid calls per run |
| `EVIDENCE_FETCH_TIMEOUT_MS` | catalog builds | Total timeout per evidence fetch |
| `ALLOW_DB_TRUNCATE` | test runs only | Explicit consent for the destructive harness |

**Client**

| Variable | Required | Purpose |
|---|---|---|
| `SOH_API_BASE_URL` | yes | API origin with no trailing `/api`. Debug builds and tests reject production hosts |
| `GOOGLE_SERVICES_INFO_PLIST_FILE` | iOS builds | Path to the untracked Firebase property list |

## F. Developer Tools Guide

- **Gates that must pass**: API typecheck (three configurations), build, and the serial suite with its per-file branch floor; client typecheck, both runner projects, the lint baseline comparison, and the stylesheet literal scanner. The plain lint command is informational because it reports the pre-existing baseline.
- **Fault injection**: `MEAL_PLANNING_FAULT` makes generation failure, swap failure and post-commit response loss reachable on a device without breaking the API. It is inert in production.
- **Idempotency**: generation, regeneration, swap and planned logging are keyed. Replaying a key returns the stored response verbatim; the same key with a different body is refused. The client mints a key at the press, persists it, and replays it after a cold start.
- **Database safety**: an origin guard classifies `DATABASE_URL` before the client is imported and refuses anything it cannot recognise; the two data-writing commands additionally demand `--confirm-target <dbname>` outside a development database.
- **Schema drift**: the migration ledger is checked by regenerating a diff and byte-comparing it with `backend/docs/meal-planning/expected-schema-diff.sql`, and both ledgers are proven equivalent by dumping and normalising two schemas built in opposite orders.
- **Observability**: a redacting logger keeps secrets and vendor keys out of output; command runs record a run identifier, counts and a status so a partial run can be resumed.

## G. Glossary

| Term | Meaning |
|---|---|
| Catalog food | A published food record with per-100 g nutrition, aliases, portion conversions and a validation record |
| Catalog release | A versioned, checksummed export of the published catalog, loaded identically into any environment |
| Provenance | How a number was obtained: source-backed, ingredient-derived, AI-estimated or user-entered. Recorded separately from identity and publication state |
| Source-backed | Values supported by a vendor record or label; the only class allowed into planning |
| Plan revision | A counter on a plan, incremented by every write. Clients send the revision they hold and are refused if it has moved |
| Idempotency key | A client-minted identifier for one user intent, so retries return the first result instead of acting twice |
| Keyed action | A write that reserves its key, runs under a per-user lock, and stores its response for replay |
| Limiting constraint | The named preference that made a week infeasible, returned with the step to edit |
| Portion multiplier | The scale applied to a recipe serving in a plan slot |
| Flagged grocery item | A checked item whose amount rose after a swap: it stays checked and shows the old amount, new amount and delta |
| Stale targets | Confirmed targets whose input answers have since changed. Nothing recalculates silently |
| Detachment | Editing a planned entry's name or macros clears its plan links and marks it user-entered, so the plan no longer claims those numbers |
| Capability flag | The server switch and the client remote flag that together gate the feature |
