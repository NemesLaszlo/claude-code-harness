---
name: plan-feature
description: "Run the three-phase Feature Planner validation before implementing a feature. Use when the user says plan feature, plan a new feature, I want to add, or any request for structured feature planning with edge case and risk analysis."
---

## Context

- Current branch: !`git branch --show-current`
- Recent changes: !`git log --oneline -5`
- Project structure: !`ls -la`

## Your Task

You are invoking the **Feature Planner** agent workflow. Run all three validation phases for the following feature:

**Feature:** $ARGUMENTS

### Phase 1: Domain & Spec Validation

Before planning, validate the project's documentation and specs:

- **Completeness**: All key features specced? Features in README but missing from specs?
- **Gaps**: Missing specs, undocumented decisions, features without tasks
- **Consistency**: Do specs, README, and tasks tell the same story? Contradictions?
- **Staleness**: Specs outdated relative to completed work? CHANGELOG accurate?
- **Coverage**: Tasks cover all specced features? Orphaned tasks?
- **Clarity**: Requirements precise enough to implement? Vague language?
- **Data model**: Entities, relationships, invariants defined? Missing fields or constraints?
- **API contracts**: Endpoints, payloads, error responses, status codes documented?
- **Non-functional requirements**: Performance targets, scalability, availability, rate limits?

Report each item as PASS / WARN / BLOCK. Stop if any BLOCK items exist.

### Phase 2: Task Validation

For each task derived from the feature:

- **Requirements clarity**: Specific and testable? Flag vague language
- **Acceptance criteria**: Concrete, verifiable, can each become a test?
- **Scope**: Too big? Too small? Clear boundaries?
- **Edge cases**: Error states, empty states, concurrent access, boundary conditions, null inputs, permission denied, timeout/retry, race conditions
- **Dependencies**: Other tasks, services, APIs, feature flags?
- **Security implications**: Auth, user data, financial transactions?
- **Data implications**: Schema changes? Migration? Breaking changes?
- **Observability**: Logging, metrics, alerting needs?
- **Rollback strategy**: Safely revertible? Feature flag needed?
- **Domain consistency**: Aligns with specs and architecture?

Output a refined task definition for each task with all gaps filled.

### Phase 3: Implementation Plan Validation

Build and validate the implementation plan:

- **Spec-plan alignment**: Addresses all requirements and acceptance criteria?
- **Technical approach**: Simplest that meets requirements?
- **Pattern consistency**: Follows existing codebase patterns?
- **File coverage**: All files that need changes identified?
- **Implementation order**: Right order? Hidden dependencies?
- **Testing strategy**: Unit, integration, e2e coverage for critical paths?
- **Migration strategy**: DB migrations? Feature flags? Backwards compatibility?
- **Risk areas**: Complex integrations, performance, third-party limits?
- **Verification plan**: How to prove it works?

### Final Output

Produce a consolidated **Validated Feature Plan** with:
1. Validation summary (all 3 phases)
2. Warnings and accepted risks
3. Refined tasks in implementation order
4. Full implementation plan with file changes
5. Recommended agent assignments
6. Open questions needing human decision

Refer to the Feature Planner agent for detailed output formats.
