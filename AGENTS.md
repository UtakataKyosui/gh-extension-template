# AGENTS.md

## Purpose

This document defines development instructions for Coding Agents working on Agent-Native gh Extension Template.

The project provides a Go-based template for developing GitHub CLI extensions that are reliable for both human users and automated Coding Agents.

All contributions must preserve two properties:

1. **Agent-Friendly Development:** The repository must remain easy for humans and Coding Agents to understand, modify, and verify.
2. **Agent-Friendly Interface:** CLI extensions generated from this template must expose predictable, machine-readable, and automation-safe behavior.

These principles apply to architecture, implementation, testing, documentation, and maintenance.

## 1. Source of Truth

Read the relevant documentation before making changes.

| Document | Responsibility |
| --- | --- |
| [Epic #18: 要件定義](https://github.com/UtakataKyosui/gh-extension-template/issues/18) and its spec sub-issues | Product requirements and acceptance criteria |
| [Epic #19: CLI外部契約](https://github.com/UtakataKyosui/gh-extension-template/issues/19) and its spec sub-issues | Public CLI behavior and output contracts |
| [Epic #20: アーキテクチャ設計](https://github.com/UtakataKyosui/gh-extension-template/issues/20) and its spec sub-issues | Internal architecture and responsibilities |
| [GitHub Issues](https://github.com/UtakataKyosui/gh-extension-template/issues) | Implementation work, order, dependencies, and unresolved decisions |
| `docs/decisions/` (created as decisions are accepted) | Architectural decisions |
| `README.md` | Project overview and user-facing documentation |

Specifications are maintained as GitHub Issues labeled `spec`. Each Epic links its spec sub-issues; edit the relevant issue body to change a specification and record the reason in a comment. Keep spec issues open while they remain authoritative.

Do not treat conversation notes or historical design proposals as authoritative specifications.

If documentation conflicts:

1. Identify the conflicting statements.
2. Determine which document owns the relevant specification.
3. Explain the discrepancy.
4. Propose a resolution.
5. Do not silently change established public contracts.

If a referenced document does not exist, report the missing documentation and continue only with work that does not depend on it.

Do not invent missing requirements.

## 2. General Development Rules

### 2.1 Prefer Simple Solutions

- Prefer Go standard library functionality where practical.
- Avoid unnecessary dependencies.
- Avoid premature abstraction.
- Keep functions focused on a single responsibility.
- Prefer explicit behavior over implicit conventions.
- Do not introduce frameworks without a clear requirement.

### 2.2 Maintain Responsibility Boundaries

Use the following architectural responsibilities as a starting point:

- `cmd/`: Cobra commands, flags, argument validation, and CLI integration.
- `internal/cli/`: Common response formatting, error handling, exit codes, and CLI conventions.
- `internal/github/`: GitHub API and GitHub CLI integration.
- `schemas/`: Machine-readable public contracts.
- `test/contract/`: CLI contract and compatibility tests.
- `test/golden/`: Expected CLI output fixtures.

The final structure is governed by the architecture specification ([#20](https://github.com/UtakataKyosui/gh-extension-template/issues/20)).

Do not introduce dependencies between packages that violate documented architectural boundaries.

### 2.3 Separate Business Logic from CLI Presentation

Cobra handlers should remain thin.

A command should:

1. Parse and validate input.
2. Invoke the appropriate application operation.
3. Convert its result into a shared response representation.
4. Return errors through the shared error-handling mechanism.

Business logic should not directly depend on terminal formatting.

## 3. CLI Interface Contract

The public CLI contract is defined in the CLI contract specification ([#19](https://github.com/UtakataKyosui/gh-extension-template/issues/19)).

This file is the authoritative source for:

- Output formats
- Exit codes
- Error identifiers
- JSON response schemas
- Standard output and standard error behavior
- Non-interactive execution
- Dry-run behavior
- Side-effect reporting
- Compatibility rules

Do not independently redefine these conventions in command implementations.

### 3.1 Machine-Readable Output

When a command supports JSON output:

- Output must be valid JSON.
- Output must conform to its documented schema.
- Do not print human-readable progress messages to the JSON result stream.
- Do not depend on terminal colors or formatting for machine-readable information.
- Do not expose secrets, credentials, or authentication tokens.
- Use stable field names and documented data types.

Where practical, use a centralized formatter.

### 3.2 Human-Readable Output

Human-readable output should be concise and useful.

- Prefer clear status messages.
- Avoid unnecessary verbosity.
- Keep diagnostic output separate from result data.
- Respect non-interactive environments.
- Do not assume color or terminal capabilities are available.

Follow established GitHub CLI conventions wherever practical.

### 3.3 Exit Codes

Exit codes must have consistent, documented meanings.

- Successful commands must report success consistently.
- Input validation failures must be distinguishable from runtime failures.
- Authentication and authorization failures must be distinguishable where the contract requires it.
- Do not assign new exit codes without updating the CLI contract.
- Do not silently change an existing exit code's meaning.

Whenever an exit code is added or changed, update the corresponding tests and documentation.

### 3.4 Structured Errors

Errors should expose sufficient information for an automated caller to determine the next action.

Where supported by the contract, structured errors should include:

- Stable machine-readable error identifier
- Human-readable explanation
- Retryability information
- Side-effect state
- Suggested recovery action

Never infer retryability solely from the presence of a network error.

A failed command may have already modified an external resource.

### 3.5 Safe Automation

Commands with side effects require additional care.

- Document the operations that modify external state.
- Support dry-run behavior where practical.
- Do not perform destructive operations unexpectedly.
- Avoid interactive prompts in explicitly non-interactive mode.
- Never assume an interrupted operation had no effect.
- Do not automatically retry non-idempotent operations without a safe recovery strategy.

When an operation's completion state is unknown, the response must not claim that it definitely failed without side effects.

### 3.6 Command Discoverability

Command names, flags, arguments, and usage information should be predictable.

- Provide clear command descriptions.
- Keep help output accurate.
- Prefer established `gh` conventions.
- Expose machine-readable command metadata where required by the contract.
- Document command-specific behavior that differs from shared defaults.

## 4. Go Implementation Guidelines

### 4.1 Code Style

- Follow idiomatic Go conventions.
- Format code using `gofmt`.
- Use `go vet` for static analysis.
- Use the configured linter.
- Handle errors explicitly.
- Avoid unnecessary global mutable state.
- Prefer composition over unnecessary abstractions.

### 4.2 Error Handling

- Return errors instead of terminating the process inside application logic.
- Use error wrapping where appropriate.
- Preserve sufficient context for debugging.
- Avoid leaking sensitive information.
- Map errors to CLI-specific error identifiers at the appropriate boundary.

Do not scatter `os.Exit` calls throughout application packages.

Process termination should be controlled by the executable entry point.

### 4.3 Dependencies

Before introducing a dependency:

1. Verify that the standard library cannot reasonably satisfy the requirement.
2. Explain the dependency's responsibility.
3. Consider maintenance and compatibility.
4. Avoid adding overlapping libraries.
5. Update Go module files consistently.

Do not update unrelated dependencies as part of a feature implementation.

## 5. Testing Requirements

Every new behavior must be covered by appropriate tests.

### 5.1 Unit Tests

Test application logic independently from CLI presentation and external services.

Prefer table-driven tests where appropriate.

### 5.2 CLI Contract Tests

Validate:

- Command exit codes
- JSON schema compatibility
- Error classification
- Standard output and standard error
- Input validation
- Non-interactive execution
- Dry-run behavior
- Side-effect reporting

### 5.3 Golden Tests

Use golden tests for stable, human-readable output and other output formats where snapshot comparison is appropriate.

Avoid fragile snapshots that depend on timestamps, random identifiers, or environment-specific values.

### 5.4 GitHub Integration Tests

External GitHub operations should be isolated behind testable interfaces.

- Use fakes or mocks for unit tests.
- Do not depend on real GitHub credentials for ordinary test execution.
- Do not modify real repositories during unit tests.
- Use dedicated test resources for integration tests when required.

### 5.5 Regression Prevention

When fixing a bug:

1. Reproduce the issue.
2. Add or update a regression test.
3. Implement the fix.
4. Run the relevant tests.
5. Check for unintended changes to CLI behavior.

## 6. Development Workflow

Follow this workflow for implementation tasks:

1. Read the relevant specifications.
2. Identify the affected packages and public contracts.
3. Determine the expected behavior and acceptance criteria.
4. Add or update tests.
5. Implement the smallest reasonable change.
6. Format and lint the code.
7. Run relevant automated tests.
8. Review documentation consistency.
9. Summarize the result.

For larger changes, divide work into independently verifiable steps.

Do not perform unrelated refactoring during focused implementation tasks unless necessary.

## 7. Validation

Use the project's documented validation commands.

The intended baseline checks are:

```bash
gofmt -l .
go vet ./...
go test ./...
go build ./...
```

`gofmt -l` lists unformatted files; successful formatting verification requires an empty result.

Additional checks may include:

```bash
golangci-lint run
make check
```

These additional commands must only be used once they are configured in the repository.

Do not report successful validation unless the relevant commands were actually executed.

If validation cannot run, explain why.

## 8. Documentation Rules

Update documentation when behavior changes.

| Change | Required documentation |
| --- | --- |
| New command | README or command documentation |
| New public flag | CLI contract or command documentation |
| Output format change | CLI contract and schema |
| New error identifier | CLI contract |
| Exit code change | CLI contract |
| Architectural change | Architecture documentation |
| Important design decision | ADR |
| Development workflow change | AGENTS.md or CONTRIBUTING.md |

Avoid duplicating authoritative specifications across multiple files.

Prefer linking to the source of truth.

## 9. Agent Skills

Reusable task-specific procedures should be stored as Agent Skills.

The planned development Skill is:

```text
.agents/skills/gh-extension-development/SKILL.md
```

It should describe procedures for:

- Adding new commands
- Implementing structured responses
- Defining CLI errors
- Writing contract tests
- Validating GitHub CLI compatibility
- Preparing extension releases

`AGENTS.md` defines repository-wide rules.

Skills provide focused workflows.

Do not duplicate the entire repository rule set inside a Skill.

## 10. Git and GitHub Safety Rules

Coding Agents must not perform the following operations without explicit user approval:

- Push commits to remote repositories
- Merge or close Pull Requests
- Create or publish releases
- Delete remote branches
- Delete GitHub Issues or other remote resources
- Change repository permissions or settings
- Perform destructive changes to external resources

Local commits may be created when explicitly requested as part of the task.

Do not rewrite unrelated Git history.

Do not discard uncommitted user changes.

Do not commit credentials, tokens, or sensitive information.

Use `gh` for GitHub operations where appropriate.

## 11. Implementation Boundaries

Do not introduce unrelated capabilities into the template.

In particular, avoid:

- Unnecessary background services
- Unrelated infrastructure dependencies
- Complex plugin architectures without requirements
- Mandatory TUI frameworks
- Features that duplicate existing GitHub CLI capabilities without justification

Keep the template small enough to serve as a practical starting point for new extensions.

Optional features should remain optional.

## 12. Completion Criteria

Before reporting a task as complete, verify that:

- The implementation satisfies the relevant requirements.
- Public CLI contracts are preserved or explicitly updated.
- Appropriate tests have been added or updated.
- Relevant automated checks pass.
- Documentation is consistent with the implementation.
- No unrelated changes have been introduced.
- Remaining limitations are documented.

If a requirement cannot be satisfied, report the limitation rather than claiming completion.

## 13. Completion Report

When finishing a task, provide a concise summary covering:

1. Changes implemented
2. Files or modules affected
3. Tests and validation performed
4. Documentation updated
5. Known limitations or remaining work

Distinguish completed work from proposed or unverified work.

## 14. Guiding Principle

A successful implementation must be understandable to a human developer and predictable to an automated Coding Agent.

Do not optimize solely for code generation speed.

Prioritize explicit contracts, testable behavior, safe automation, and maintainability.

## Append Context

### Prior Art and Design Rationale

Before introducing new architectural conventions or CLI contracts, consult the research specification ([#21](https://github.com/UtakataKyosui/gh-extension-template/issues/21)).

It records:

- Existing projects and specifications that influenced the design.
- Features adopted, adapted, or intentionally excluded.
- Differentiation goals and their current evidence.
- Unresolved compatibility and licensing considerations.

Do not claim that a feature is unique without evidence.

Do not copy third-party implementations without checking their licenses and recording attribution.

Treat this research document as historical and contextual information, not as the authoritative implementation contract.
