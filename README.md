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

The authoritative contract is maintained in [the CLI contract specification (#19)](https://github.com/UtakataKyosui/gh-extension-template/issues/19).

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

## Getting Started

The repository currently contains design documents and development instructions; the Go scaffold and runtime are not implemented yet. Verified setup commands will be added when template initialization is implemented.

For the proposed response examples and output rules, see [the CLI contract (#19)](https://github.com/UtakataKyosui/gh-extension-template/issues/19). For package responsibilities and the proposed layout, see [the architecture (#20)](https://github.com/UtakataKyosui/gh-extension-template/issues/20).

## Work Tracking

Implementation work, unresolved decisions, acceptance criteria, and dependencies are tracked in GitHub Issues. Requirements, public CLI behavior, internal architecture, and design rationale are maintained as `spec` Epics and their sub-issues, listed under [Documentation](#documentation).

| Phase | Issue |
| --- | --- |
| MVP | [CLI契約・JSON Schemaの初版と互換性方針を確定する (#1)](https://github.com/UtakataKyosui/gh-extension-template/issues/1) |
| MVP | [Go/Cobraの実行基盤とパッケージ構成を実装する (#2)](https://github.com/UtakataKyosui/gh-extension-template/issues/2) |
| MVP | [共通レスポンス・構造化エラー・終了コードを実装する (#3)](https://github.com/UtakataKyosui/gh-extension-template/issues/3) |
| MVP | [Cobraのパースエラーと非対話動作を共通Runtimeへ統合する (#4)](https://github.com/UtakataKyosui/gh-extension-template/issues/4) |
| MVP | [GitHub Port・Fake AdapterとAPIエラーの正規化を実装する (#5)](https://github.com/UtakataKyosui/gh-extension-template/issues/5) |
| MVP | [読取サンプルとdry-run対応の書込サンプルを実装する (#6)](https://github.com/UtakataKyosui/gh-extension-template/issues/6) |
| MVP | [CLI契約・Golden・gh拡張互換性テストを整備する (#7)](https://github.com/UtakataKyosui/gh-extension-template/issues/7) |
| MVP | [format・vet・test・build・lintを実行するCIを追加する (#8)](https://github.com/UtakataKyosui/gh-extension-template/issues/8) |
| MVP | [テンプレートの名前・Go module初期化とfresh-template E2Eを実装する (#9)](https://github.com/UtakataKyosui/gh-extension-template/issues/9) |
| MVP | [precompiledリリースの雛形と配布検証手順を整備する (#10)](https://github.com/UtakataKyosui/gh-extension-template/issues/10) |
| MVP | [開発Skill・CONTRIBUTING・Issue/PRテンプレートを整備する (#11)](https://github.com/UtakataKyosui/gh-extension-template/issues/11) |
| Post-MVP | [コマンド能力を公開するJSON manifestを設計・実装する (#12)](https://github.com/UtakataKyosui/gh-extension-template/issues/12) |
| Post-MVP | [gh式のJSONフィールド投影・jq・template互換レイヤーを検討する (#13)](https://github.com/UtakataKyosui/gh-extension-template/issues/13) |
| Post-MVP | [副作用付き応答の拡張と構造化リカバリー情報を検討する (#14)](https://github.com/UtakataKyosui/gh-extension-template/issues/14) |
| Post-MVP / Evaluation | [Post-MVPの文書生成・任意UI・API例の採否を評価する (#15)](https://github.com/UtakataKyosui/gh-extension-template/issues/15) |
| Pre-release validation | [差別化の実証・OSS帰属・外部仕様の適合範囲を検証する (#16)](https://github.com/UtakataKyosui/gh-extension-template/issues/16) |

## Documentation

| Document | Purpose |
| --- | --- |
| `AGENTS.md` | Development instructions for Coding Agents |
| [Epic #18: 要件定義](https://github.com/UtakataKyosui/gh-extension-template/issues/18) | Functional and non-functional requirements |
| [Epic #19: CLI外部契約](https://github.com/UtakataKyosui/gh-extension-template/issues/19) | CLI output and behavior contracts |
| [Epic #20: アーキテクチャ設計](https://github.com/UtakataKyosui/gh-extension-template/issues/20) | Architecture and module responsibilities |
| [Epic #21: 先行調査・設計根拠・差別化分析](https://github.com/UtakataKyosui/gh-extension-template/issues/21) | Prior art and design rationale |
| [GitHub Issues](https://github.com/UtakataKyosui/gh-extension-template/issues) | Implementation work, decisions, and dependencies |
| [Milestones](https://github.com/UtakataKyosui/gh-extension-template/milestones) | MVP, Post-MVP, and pre-release validation scope |

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

See [LICENSE](LICENSE). OSS attribution and release documentation checks are tracked in [#16](https://github.com/UtakataKyosui/gh-extension-template/issues/16).
