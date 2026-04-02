# GDPR Compliance — Claude Skill

A Claude Code skill that performs a structured **5-phase GDPR & Privacy audit**
on any codebase, following GDPR (EU Reg. 2016/679) and Privacy by Design /
Privacy by Default (Art. 25).

---

## How it works

The skill guides Claude through five sequential audit phases. Each phase:

1. Reads only targeted files (no blind recursive glob).
2. Writes a structured Markdown report to `gdpr_reports/`.
3. Commits and pushes the report.
4. Stops and waits for your confirmation before continuing.

| Phase | Focus |
|---|---|
| **01 — Reconnaissance** | Stack detection, personal data flows, legal files |
| **02 — Privacy by Default** | Data minimisation, default settings, retention, dark patterns |
| **03 — Security** | Transport security, auth/authorisation, XSS, secrets |
| **04 — GDPR Compliance** | Consent, data subject rights, age check, DPA/DPIA, transfers |
| **05 — Executive Summary** | Consolidated findings, scores, prioritised action plan |

---

## Stack-agnostic design

The skill auto-detects the project's tech stack and adapts its analysis
accordingly. It works with any combination of:

- **Languages:** JavaScript/TypeScript, Python, PHP, Ruby, Go, Java, …
- **Frameworks:** React, Vue, Angular, Next.js, Django, Rails, Laravel, Spring, …
- **Databases:** SQL (Postgres, MySQL, SQLite), NoSQL (Firestore, MongoDB,
  DynamoDB), ORM models
- **Auth:** Firebase Auth, Auth0, Devise, Passport, sessions/cookies, JWTs, SSO
- **Storage:** Local filesystem, S3-compatible, Firebase Storage, Cloudinary
- **Hosting / backend:** Cloud Functions, Express, Next.js API routes, WSGI/ASGI,
  serverless, traditional VPS

---

## Installation

Copy `gdpr-audit.md` into your project's `.claude/commands/` folder:

```bash
mkdir -p .claude/commands
cp gdpr-audit.md .claude/commands/gdpr-audit.md
```

The skill is then available in Claude Code as `/gdpr-audit`.

Alternatively, install it globally for all your projects:

```bash
mkdir -p ~/.claude/commands
cp gdpr-audit.md ~/.claude/commands/gdpr-audit.md
```

---

## Usage

In any project, open Claude Code and run:

```
/gdpr-audit
```

Claude will:
1. Detect the stack.
2. Start Phase 1 and write `gdpr_reports/01_PRIVACY_REPORT_<timestamp>.md`.
3. Ask for your confirmation before each subsequent phase.

Reports accumulate in `gdpr_reports/` and are committed after each phase.
The final Phase 5 report is also written to `PRIVACY_AUDIT.md` in the project
root as a canonical reference.

---

## Output structure

```
your-project/
├── gdpr_reports/
│   ├── 01_PRIVACY_REPORT_20260401_100000.md
│   ├── 02_PRIVACY_REPORT_20260401_101500.md
│   ├── 03_PRIVACY_REPORT_20260401_103000.md
│   ├── 04_PRIVACY_REPORT_20260401_105000.md
│   └── 05_PRIVACY_REPORT_20260401_110000.md
└── PRIVACY_AUDIT.md   ← canonical executive summary
```

---

## Operational rules enforced by the skill

- **Read-only analysis** — no source code is modified.
- **Evidence required** — every finding cites file path and line number.
- **Absent = absent** — if a control is not found, it is reported as ABSENT,
  never assumed compliant.
- **One phase per response** — Claude cannot skip ahead without confirmation.
- **No file overwriting** — each report uses a fresh timestamp.

---

## Severity scale used in reports

| Level | Meaning |
|---|---|
| **Critical** | Immediate data breach risk or hard legal blocker |
| **High** | Serious violation requiring urgent remediation |
| **Medium** | Significant gap; must be fixed before go-live |
| **Low** | Best-practice gap; fix in next sprint |
| **Info** | Observation; no immediate action required |

---

## License

MIT — see `LICENSE`.
