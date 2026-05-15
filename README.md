# GDPR Compliance — Claude Skill

A Claude Code **slash command** (`/gdpr-audit`) that performs a structured
**5-phase GDPR & Privacy Audit** on any codebase, following GDPR
(EU Reg. 2016/679) and Privacy by Design / Privacy by Default (Art. 25).

> **Disclaimer:** This is an automated technical aid based on static code
> analysis. It is **not legal advice** and is **not a substitute for review by a
> qualified Data Protection Officer or lawyer**. Treat its output as a starting
> point, not a compliance certificate.

---

## How it works

The command guides Claude through five sequential audit phases. Each phase:

1. Reads only targeted files (recursive grep is used for discovery, but only the
   surfaced files are read — no bulk directory dumps).
2. Writes a structured Markdown report to `gdpr_reports/`.
3. Commits the report **locally**; pushing to a remote requires your explicit
   confirmation (security findings do not leave your machine without consent).
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

The command auto-detects the project's tech stack and adapts its analysis
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

It is then available in Claude Code as the `/gdpr-audit` slash command.

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

To resume an interrupted audit, run `/gdpr-audit` again: it detects existing
reports in `gdpr_reports/` and offers to continue from the next phase rather
than restarting. You can also jump straight to a phase with `/gdpr-audit 3`.

Reports accumulate in `gdpr_reports/` and are committed **locally** after each
phase; Claude asks before pushing anything to a remote. The final Phase 5 report
is also written to `PRIVACY_AUDIT.md` in the project root, a copy of the
executive summary kept at the repo root for easy discovery.

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
└── PRIVACY_AUDIT.md   ← copy of the executive summary (repo root)
```

---

## Operational rules enforced by the command

- **Read-only analysis** — no source code is modified.
- **Evidence required** — every finding cites file path and line number.
- **Absent = absent** — if a control is not found, it is reported as ABSENT,
  never assumed compliant.
- **One phase per response** — Claude cannot skip ahead without confirmation.
- **No file overwriting** — each report uses a fresh `date`-derived timestamp.
- **Local commits, opt-in push** — reports are committed locally; `git push`
  runs only after you explicitly confirm.

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

## Limitations

- **Static analysis only** — no runtime/DAST testing, no live traffic
  inspection, no penetration testing.
- **Dependency CVE checks are best-effort** — the command prefers the stack's
  own audit tool (`npm audit`, `pip-audit`, `composer audit`, …) when available
  and offline, but does not install or upgrade anything.
- **False positives and negatives are expected** — every finding must be
  manually verified before action.
- Not a substitute for a Data Protection Officer, legal counsel, or a formal
  DPIA.

---

## A note on report storage

`gdpr_reports/` and `PRIVACY_AUDIT.md` contain detailed security findings.
Decide deliberately whether they belong in version control: for sensitive or
public repositories, consider adding `gdpr_reports/` (and `PRIVACY_AUDIT.md`) to
`.gitignore` so findings are not committed at all.

---

## License

MIT — see `LICENSE`.
