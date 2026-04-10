soroban-audit-cli
A static analysis CLI for Soroban smart contracts — catch security vulnerabilities, gas inefficiencies, and style violations before you deploy to mainnet.

License: MIT PRs Welcome Drips Wave Program

Overview
soroban-audit-cli is a pre-deployment security and quality tool for Soroban contract developers. Point it at your contract directory and it parses your Rust source files, runs a suite of modular analyzer checks, and produces a clear report of findings with severity levels, affected line numbers, and remediation guidance.

No Rust compiler required on the auditor's machine — analysis runs purely on the source AST via tree-sitter.

Technical Architecture
CLI Framework: commander.js for argument parsing — supports audit, list-rules, and init commands with flags for output format, severity threshold, and config file path
Parser: tree-sitter with the Rust grammar parses .rs contract files into an AST enabling pattern-matching rules without a full rustc toolchain
Analyzer Plugins: Three modular analyzer categories, each as an independent module exporting a RuleSet:
security/ — missing require_auth() calls, unchecked arithmetic, storage key collision risks, improper panic handling
gas/ — redundant env.storage() reads inside loops, unnecessary clone() on large types, avoidable cross-contract calls
style/ — missing contract event emissions, inconsistent naming conventions, undocumented public functions
Reporters: Pluggable output formatters — console (colored terminal output), json (machine-readable for CI pipelines), markdown (GitHub PR comment-ready audit reports)
Config: .soroban-audit.json project config for enabling/disabling rules, setting severity thresholds, and ignoring specific files or line ranges
💧 Drips Wave Program
This repository is an active participant in the Drips Wave Program — a funding mechanism that rewards open-source contributors for resolving scoped GitHub issues with on-chain streaming payments.

How to Contribute & Earn
Step 1 — Register on Drips Visit drips.network and connect your Ethereum-compatible wallet. Your wallet address is where reward streams will be sent.

Step 2 — Browse Open Issues Head to the Issues tab. Issues are labeled by complexity tier:

Label	Complexity	Typical Scope
drips:trivial	Trivial	Add a fixture contract, improve an error message, write a rule description
drips:medium	Medium	New analyzer rule, new reporter format, config schema improvements
drips:high	High	New analyzer category, CI GitHub Action, VSCode extension integration
Step 3 — Claim an Issue Comment /claim on the issue you want. The maintainer will assign it. One claim per issue at a time.

Step 4 — Submit Your Work Open a Pull Request with Closes #XX. New rules must include both a vulnerable fixture contract that triggers the rule and a safe fixture that passes cleanly.

Step 5 — Earn Rewards Your wallet begins receiving a continuous Drips stream upon PR merge.

Project Structure
soroban-audit-cli/
├── src/
│   ├── analyzers/
│   │   ├── security/         # require_auth checks, arithmetic overflow, storage risks
│   │   ├── gas/              # Storage read patterns, clone detection, call efficiency
│   │   └── style/            # Event emissions, naming conventions, doc coverage
│   ├── reporters/
│   │   ├── console/          # Colored terminal output with severity icons
│   │   ├── json/             # Machine-readable JSON output for CI
│   │   └── markdown/         # GitHub PR-ready markdown audit report
│   ├── parsers/              # tree-sitter Rust AST parser, node traversal helpers
│   ├── utils/                # File scanner, config loader, severity sorter
│   └── config/               # Default rule config, .soroban-audit.json schema
├── tests/
│   ├── unit/                 # Per-rule unit tests
│   └── fixtures/
│       ├── vulnerable/       # Sample contracts that should trigger rules
│       └── safe/             # Sample contracts that should pass cleanly
├── docs/                     # Rule reference, contributing guide, CI setup
├── scripts/                  # Release scripts
├── package.json
├── tsconfig.json
└── README.md
Quick Start
npm install -g soroban-audit-cli
# Audit a contract directory
soroban-audit audit ./contracts

# Output as markdown report
soroban-audit audit ./contracts --format markdown --output audit-report.md

# List all available rules
soroban-audit list-rules

# Only flag high severity issues
soroban-audit audit ./contracts --severity high
Example Output
soroban-audit v1.0.0 — Scanning 3 files...

✖  HIGH    [SEC-001] Missing require_auth() in transfer()       contracts/token.rs:42
✖  HIGH    [SEC-003] Unchecked arithmetic in calculate_fee()    contracts/token.rs:87
⚠  MEDIUM  [GAS-002] Redundant storage read inside loop         contracts/token.rs:103
ℹ  LOW     [STY-001] Public function missing doc comment        contracts/token.rs:15

4 issues found (2 high, 1 medium, 1 low)
License
MIT © soroban-audit-cli contributors