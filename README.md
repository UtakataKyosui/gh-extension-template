# Agent-Native gh Extension Template

A Go-based GitHub CLI extension template designed for both human developers and Coding Agents.

> Build GitHub CLI extensions that are easy for humans to develop and reliable for Coding Agents to operate.

## Overview

Agent-Native gh Extension Template is a starter template for developing GitHub CLI (`gh`) extensions using Go.

Traditional CLI templates typically focus on project scaffolding, command implementations, testing, and distribution.

However, Coding Agents require additional considerations:

- Machine-readable command responses
- Predictable and documented exit codes
- Structured errors with actionable recovery information
- Safe execution of commands with side effects
- Non-interactive execution
- Discoverable command capabilities
- Consistent behavior across subcommands

This project provides a foundation for building CLI extensions with these characteristics.

It also standardizes development workflows so both humans and Coding Agents can implement, test, and maintain extensions consistently.

## Goals

The project has two primary goals.

### 1. Agent-Friendly Development

Enable humans and Coding Agents to develop GitHub CLI extensions efficiently through:

- A consistent Go project structure
- Explicit architectural boundaries
- Development rules defined in `AGENTS.md`
- Reusable Agent Skills
- Automated testing and linting
- GitHub Actions-based CI
- Standardized Issue and Pull Request templates
- Reproducible development commands

### 2. Agent-Friendly Interface

Enable Coding Agents to interact with generated CLI extensions reliably through:

- Structured JSON responses
- Human-readable text output
- Consistent exit codes
- Structured error information
- Explicit retryability and side-effect states
- Non-interactive execution modes
- Dry-run support for applicable operations
- Machine-readable command metadata

The CLI should communicate not only whether an operation succeeded, but also what happened and what the caller can safely do next.

## Design Principles

### Contract-First Development

Define CLI behavior before implementing commands.

Each command must conform to documented contracts covering input validation, output formats, error handling, and exit status.

The authoritative contract is maintained in `docs/cli-contract.md`.

### Human and Agent Parity

Every command should remain usable by human developers while providing a predictable interface for automated consumers.

Human-readable output should prioritize clarity.

Machine-readable output should prioritize stability, unambiguity, and ease of parsing.

### Safe Automation

Commands that modify external resources must make their side effects explicit.

Where appropriate, they should support dry-run execution and non-interactive operation.

A command must not encourage automatic retries when the previous operation may already have succeeded.

### Consistency Over Cleverness

Common behavior should be implemented in shared components rather than independently across commands.

Individual command implementations should not invent their own response formats, error codes, or output conventions.

### GitHub CLI Compatibility

Generated extensions should follow established GitHub CLI conventions wherever practical.

Avoid introducing custom behavior when an existing `gh` convention already provides an appropriate solution.

## Technology Stack

| Category | Technology |
| --- | --- |
| Language | Go |
| CLI framework | Cobra |
| Host CLI | GitHub CLI (`gh`) |
| GitHub integration | GitHub CLI / GitHub API |
| Testing | Go standard testing tools |
| Linting | golangci-lint |
| CI | GitHub Actions |
| Distribution | GitHub Releases / gh-extension-precompile |
| Agent instructions | AGENTS.md |
| Agent workflows | Agent Skills |
| Output contracts | JSON Schema |

Some implementation details may change during architecture design.

## Planned Features

### CLI Runtime

- Shared response formatting
- Structured errors
- Documented exit codes
- JSON and text output
- Input validation
- Non-interactive execution
- Dry-run execution
- Side-effect reporting
- Machine-readable command metadata

### Development Tooling

- Go project scaffolding
- Standard command implementation patterns
- Unit and contract tests
- Golden tests for CLI output
- Automated linting and formatting
- CI workflows
- Release automation

### Agent Development Support

- Repository-wide instructions through `AGENTS.md`
- Reusable Agent Skills
- Clear implementation and testing conventions
- Documentation of architectural decisions
- Explicit validation commands

## CLI Response Example

The following examples illustrate the intended design. The final response schema has not yet been fixed.

### Success

```json
{
  "ok": true,
  "data": {
    "number": 42,
    "url": "https://github.com/example/repo/issues/42"
  },
  "error": null
}
```

### Failure

```json
{
  "ok": false,
  "data": null,
  "error": {
    "code": "AUTH_REQUIRED",
    "message": "GitHub authentication is required.",
    "retryable": false,
    "suggested_action": "gh auth login",
    "side_effect": "none"
  }
}
```

CLI exit codes will be defined separately from application-level error identifiers.

Their mapping must remain documented and testable.

## Planned Repository Structure

```text
.
├── .agents/
│   └── skills/
├── .github/
│   ├── ISSUE_TEMPLATE/
│   └── workflows/
├── cmd/
├── internal/
│   ├── cli/
│   └── github/
├── schemas/
├── test/
│   ├── contract/
│   └── golden/
├── docs/
│   ├── requirements.md
│   ├── cli-contract.md
│   ├── architecture.md
│   ├── implementation-plan.md
│   └── decisions/
├── AGENTS.md
├── CONTRIBUTING.md
├── README.md
├── go.mod
└── main.go
```

This structure is provisional and may evolve during implementation.

## Getting Started

The template is currently in the design and implementation planning stage.

Once the initial implementation is complete, users will be able to:

1. Create a repository from this GitHub template.
2. Initialize its project name and Go module path.
3. Install the required development tools.
4. Implement commands using the shared CLI infrastructure.
5. Run automated quality checks.
6. Install and test the extension locally using `gh`.
7. Publish precompiled releases.

Exact setup commands will be documented after the scaffolding process is implemented and verified.

## Documentation

| Document | Purpose |
| --- | --- |
| `AGENTS.md` | Development instructions for Coding Agents |
| `docs/requirements.md` | Functional and non-functional requirements |
| `docs/cli-contract.md` | CLI output and behavior contracts |
| `docs/architecture.md` | Architecture and module responsibilities |
| `docs/implementation-plan.md` | Development phases and milestones |
| `docs/decisions/` | Architecture Decision Records |

## Project Status

**Status: Planning / Initial Development**

The project is not yet a production-ready GitHub CLI extension template.

Its design goals and preliminary conventions are documented, but runtime behavior and CLI contracts remain subject to implementation and validation.

## References

- [GitHub CLI Extensions](https://docs.github.com/en/github-cli/github-cli/creating-github-cli-extensions)
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [Cobra](https://github.com/spf13/cobra)
- [gh-extension-precompile](https://github.com/cli/gh-extension-precompile)
- [cli-agent-spec](https://github.com/cli-agent-spec/cli-agent-spec)
- [cli-output-spec](https://github.com/javimosch/cli-output-spec)

## License

To be determined before public release.
