# Codex Skills Library

A curated library of reusable Codex skills for developers, individuals, and teams.

This repository helps you turn OpenAI Codex into a consistent, reliable AI assistant by packaging common workflows into reusable skills instead of repeating prompts.

## What is this?

Codex Skills Library is a collection of focused skills for common developer workflows:

- Code generation and debugging
- Refactoring and optimization
- Testing and documentation
- System design and decision support
- Engineering operations and reliability

Each skill is small, focused, triggered by natural language, and designed for progressive disclosure.

## Repository structure

Skills are organized by domain under `skills/`. Each skill is a folder with a `SKILL.md` that includes frontmatter (`name`, `description`) and a short workflow.

## Skills by domain

### Foundation
- `codebase-orientation`: Map entry points, key modules, and build/run paths; include local setup commands, env vars, and a safe starter task.
- `git-basic-helper`: Provide minimal, safe git commands with clear intent and warnings for destructive actions.
- `debugging-checklist`: Give a prioritized checklist from repro to isolation, logging, and hypothesis validation.
- `error-message-explainer`: Translate compiler/runtime errors into plain language, likely root causes, and targeted fixes.
- `linter-fix-guide`: Explain lint rules, show the expected pattern, and propose the smallest fix.
- `config-file-explainer`: Summarize purpose, key sections, defaults, and which settings are safe to change.
- `data-structure-chooser`: Recommend a structure based on operations and constraints, with time/space tradeoffs.
- `dependency-install-helper`: List required runtimes, install steps by platform, and verification commands.
- `small-script-generator`: Generate tiny automation scripts with safe defaults and a usage example.
- `ticket-breakdown`: Turn a request into small, testable steps with dependencies and validation checks.
- `log-summarizer`: Group errors, identify the first failure, and propose concrete next actions.

### Docs
- `readme-polish`: Add missing setup details, env vars, and troubleshooting guidance while keeping it concise.
- `function-docstrings`: Document purpose, parameters, return values, and error conditions in project style.
- `release-notes-drafter`: Group changes by feature/fix/breaking, translate to user impact, and add migration notes.
- `team-onboarding-guide`: Provide access/setup steps, key services, and a first-week learning plan.

### Testing
- `unit-test-starter`: Draft tests around core behavior, edge cases, and failure paths with run instructions.
- `integration-test-planner`: Identify integration points, scenarios, data flows, and needed fixtures/mocks.
- `bug-repro-plan`: Produce a minimal repro with exact steps, environment details, and expected vs actual.

### API
- `api-request-builder`: Build curl/fetch requests with auth, headers, and response inspection tips.
- `api-contract-checker`: Compare endpoints and payloads, flag breaking changes, and suggest versioning.
- `api-error-taxonomy`: Standardize error codes, payload shape, and logging guidance.
- `graphql-query-optimizer`: Reduce query depth and N+1 patterns using batching, caching, and pagination.

### Frontend
- `accessibility-basic-check`: Check contrast, labels, focus order, and keyboard navigation for regressions.
- `css-layout-helper`: Diagnose layout intent and provide minimal flex/grid fixes with rationale.
- `cli-ux-improver`: Improve CLI defaults, help text, and errors with actionable next steps.

### Backend
- `caching-strategy-helper`: Choose cache type, TTLs, and invalidation triggers based on freshness needs.
- `queue-processing-patterns`: Define idempotency, retries, visibility timeouts, and dead-letter routing.
- `feature-flag-playbook`: Plan targeting, rollout phases, monitoring, and cleanup criteria.
- `system-design-draft`: Outline components, data flow, storage, and tradeoffs with open questions.

### Data
- `sql-query-starter`: Build SELECT queries with filters, ordering, limits, and parameter placeholders.
- `db-migration-reviewer`: Check lock risk, backfills, ordering, and rollback safety.
- `data-governance-check`: Map data sensitivity, retention, access, and audit requirements.

### Infra
- `ci-failure-triage`: Classify failures, identify flake sources, and propose stabilizing fixes.
- `observability-setup`: Define key metrics, add correlation IDs, and set alert thresholds.
- `iac-reviewer`: Validate security groups, IAM rules, state changes, and deletion risk.
- `platform-migration-plan`: Create phased migration steps with compatibility and rollback.
- `multi-region-strategy`: Choose active-active or active-passive with data consistency tradeoffs.
- `cross-service-debugger`: Correlate logs/traces across services and isolate the failing hop.
- `zero-downtime-migration`: Plan dual writes, cutover checks, and rollback triggers.

### Security
- `security-quick-scan`: Check auth, input validation, secrets, and risky dependencies.
- `threat-modeling`: Identify assets, trust boundaries, threats, and mitigations.
- `dependency-risk-audit`: Review licenses, vulnerabilities, and maintainer health.
- `compliance-readiness`: Map controls to evidence, highlight gaps, and assign remediation.
- `config-hardening`: Enforce safer defaults, validation, and secret handling.

### Performance
- `performance-trace-guide`: Capture stable traces, identify hotspots, and propose fixes.
- `scalability-assessment`: Estimate growth, find bottlenecks, and suggest scale strategies.
- `performance-budgeting`: Set budgets, thresholds, and CI or release enforcement.
- `cost-optimization-review`: Identify cost drivers, right-sizing, and savings estimates.

### Reliability
- `reliability-slo-sla`: Define SLIs, error budgets, and alert thresholds tied to impact.
- `incident-postmortem`: Create a timeline, root cause analysis, and action items.

### Architecture
- `architecture-review`: Evaluate assumptions, bottlenecks, and failure modes with tradeoffs.
- `domain-modeling`: Identify entities, invariants, and bounded contexts with interfaces.

### Planning
- `refactor-roadmap`: Sequence refactors with tests, flags, and checkpoints.
- `tech-debt-portfolio`: Catalog debt, estimate impact, and prioritize with ROI.
- `roadmap-prioritization`: Score initiatives by impact, effort, and dependencies.
- `org-standardization`: Propose minimal standards and adoption enforcement.
- `vendor-evaluation`: Compare vendors by security, integration, cost, and risks.
- `dependency-upgrade-plan`: Order upgrades, note breaking changes, and plan rollback.
- `simple-refactor`: Improve naming and structure without behavior changes.
- `pr-reviewer`: Identify logic bugs, edge cases, and missing tests with severity.

## Get started

1. Browse `skills/` and open a `SKILL.md` that matches your task.
2. Trigger the skill by asking Codex for that workflow.
3. Improve or extend skills as your team learns what works best.

Step-by-step tutorial: https://proflead.dev/posts/codex-skills-explained-101/
Video tutorial: https://youtu.be/d3Ydt6LyGeY


## Contribute

Contributions are welcome. Please open an issue for ideas or send a PR with new skills, improvements, or fixes. If this library is useful, please star the repo and share it with your team.
