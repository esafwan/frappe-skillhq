# frappe-skillhq

> The definitive skill pack for AI-assisted Frappe & ERPNext development.

Building on [Frappe](https://frappeframework.com) is powerful but has a steep
learning curve — the right hook, the correct DocType pattern, the exact bench
command, the permission model that won't bite you in production. **frappe-skillhq**
encodes that expertise as structured skills that AI coding agents can load and
act on directly.

The result: an AI pair-programmer that already knows Frappe — not one you have
to teach from scratch every session.

---

## Who is this for?

- **Frappe / ERPNext developers** who use AI coding agents (Claude Code, Cursor,
  Kimi, OpenCode, or similar) and want the agent to produce correct Frappe code
  without hand-holding.
- **Teams building custom Frappe apps** who want consistent, best-practice code
  across all contributors and AI-generated output.
- **Consultants and integrators** working across multiple ERPNext instances who
  need fast, reliable reference for v14, v15, and v16 patterns.

---

## What it does

Each skill is a focused, agent-readable document covering one Frappe topic:
what to use, how to write it correctly, what mistakes to avoid, and how to
debug when things go wrong. When an AI agent loads a skill, it gains:

- **Correct patterns** — the right API, the right hook, the right field type
- **Pitfall prevention** — common mistakes called out explicitly before they
  happen
- **Version awareness** — differences between v14, v15, and v16 noted inline
- **Actionable examples** — copy-paste snippets that follow Frappe conventions

Together the 69 skills cover the full Frappe development lifecycle: from
scaffolding a new app to deploying it to production.

---

## Install

```bash
git clone https://github.com/esafwan/frappe-skillhq.git ~/.ai-skills/frappe-skillhq
```

Then symlink into your agent's skills directory:

```bash
# Claude Code
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.claude/skills/frappe-skillhq

# Cursor
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.cursor/skills/frappe-skillhq

# Kimi Code
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.kimi-code/skills/frappe-skillhq

# OpenCode
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.opencode/skills/frappe-skillhq
```

Once linked, invoke any skill by name in your agent session:

```
/frappe-impl-workspace
/frappe-errors-api
/frappe-core-permissions
```

---

## Companion Skills

The skills below are maintained as separate repos because they are complex,
standalone tools with their own scripts and release cadence. They are
recommended companions to this pack:

| Skill | Repo | What it adds |
| :--- | :--- | :--- |
| `doctype-skills` | [`esafwan/doctype_skills`](https://github.com/esafwan/doctype_skills) | Generate production-correct Frappe DocType JSON from business requirements via an IR → builder → validator → repair pipeline. Use this when you need to scaffold many DocTypes automatically; use `frappe-syntax-doctypes` inside this pack for quick syntax reference. |

Install it the same way — clone once and symlink into your agent's skills
directory:

```bash
git clone git@github.com:esafwan/doctype_skills.git ~/.ai-skills/doctype-skills
ln -sfn ~/.ai-skills/doctype-skills ~/.claude/skills/doctype_skills
ln -sfn ~/.ai-skills/doctype-skills ~/.kimi-code/skills/doctype_skills
ln -sfn ~/.ai-skills/doctype-skills ~/.opencode/skills/doctype_skills
ln -sfn ~/.ai-skills/doctype-skills ~/.codex/skills/doctype_skills
ln -sfn ~/.ai-skills/doctype-skills ~/.pi/agent/skills/doctype_skills
```

---

## Skill Index

### 🤖 Agents — 5 skills
Meta-agents that orchestrate the rest of the catalog. Start here for complex,
multi-step tasks.

| Skill | What it does |
| :--- | :--- |
| `frappe-agent-architect` | Designs multi-app Frappe architectures — when to split apps, cross-app hooks, dependency management |
| `frappe-agent-debugger` | Systematic debugging via bench console, traceback analysis, log files, pdb, VS Code DAP, profiling |
| `frappe-agent-interpreter` | Converts vague requirements ("add approval flow") into concrete technical specs mapped to the right skills |
| `frappe-agent-migrator` | Guides version upgrades v14→v15→v16 — breaking change detection, deprecated API mapping, migration checklist |
| `frappe-agent-validator` | Reviews generated code against all 69 skills — catches anti-patterns and v16 regressions before deployment |

---

### 🧱 Core — 12 skills
Deep reference for Frappe internals. Use these when you need to understand
*how* something works, not just *how to write it*.

| Skill | What it covers |
| :--- | :--- |
| `frappe-core-api` | REST API, RPC API, authentication, webhooks, rate limiting (v14/v15/v16) |
| `frappe-core-cache` | Redis caching, `@redis_cache`, cache invalidation, distributed locking, TTL strategies |
| `frappe-core-database` | `frappe.db`, ORM patterns, `frappe.get_doc/get_list`, raw SQL, transactions, performance |
| `frappe-core-files` | File uploads, attachments, private/public file access, S3 storage, File DocType |
| `frappe-core-logging` | `frappe.logger()`, `frappe.log_error()`, Sentry integration, production logging patterns |
| `frappe-core-notifications` | `frappe.sendmail`, Notification DocType, Assignment Rules, Auto Repeat, ToDo API |
| `frappe-core-permissions` | Roles, User Permissions, perm levels, data masking, `has_permission` hook |
| `frappe-core-search` | Link field search, global search, SQLite FTS5 (v15+), Awesomebar customization, website search |
| `frappe-core-translation` | `_()` / `__()`, CSV translation files, PO/MO (v15+), RTL support, bench extraction commands |
| `frappe-core-utils` | `frappe.utils.*` — date/time, number formatting, money, string helpers, validation |
| `frappe-core-workflow` | Workflow DocType, states, transitions, conditions (Python expressions), Workflow Action |
| `frappe-data-access` | Query/create/update/delete documents via MCP, REST API, bench console, or bench mariadb |

---

### ❌ Errors — 7 skills
Diagnosis guides per surface. Each skill maps error messages to root causes and
fixes — use when something breaks.

| Skill | What it diagnoses |
| :--- | :--- |
| `frappe-errors-api` | 401/403/404/417/500 errors — wrong token format, missing `@whitelist`, validation failures |
| `frappe-errors-clientscripts` | TypeError, `frappe.call` failures, async/await mistakes, CSRF token errors, child table access |
| `frappe-errors-controllers` | Autoname failures, validate loops, wrong lifecycle hook, NestedSet errors, recursion without flags |
| `frappe-errors-database` | DuplicateEntry, LinkValidation, deadlocks, SQL parameter format (`%` vs `%s`), query timeouts |
| `frappe-errors-hooks` | Hook not firing (typo, wrong dict key), circular imports, scheduler not running, fixture issues |
| `frappe-errors-permissions` | PermissionError (403), `has_permission` failures, User Permission over/under-restriction |
| `frappe-errors-serverscripts` | ImportError (the #1 Server Script error), sandbox violations, `doc_events` not firing |

---

### 🔨 Implementation — 14 skills
Step-by-step build guides. Each skill walks through the full workflow for one
Frappe feature surface.

| Skill | What it builds |
| :--- | :--- |
| `frappe-impl-clientscripts` | Field visibility, cascading filters, calculated fields, custom buttons, child table logic |
| `frappe-impl-controllers` | Document Controllers — lifecycle hooks, validation, autoname, submittable docs, flags system |
| `frappe-impl-customapp` | Custom Frappe app from scratch — `bench new-app`, structure, fixtures, packaging, installation |
| `frappe-impl-hooks` | `hooks.py` — `doc_events`, `scheduler_events`, override/extend, permission hooks, asset injection |
| `frappe-impl-integrations` | OAuth2, Connected Apps, Webhooks, Payment Gateways, Data Import/Export |
| `frappe-impl-jinja` | Print Formats, Email Templates, Notification templates, Portal Pages, custom Jinja methods |
| `frappe-impl-reports` | Script Reports, Query Reports, dashboard charts, Number Cards |
| `frappe-impl-scheduler` | Scheduled tasks, background jobs, `enqueue`, retry config (v14/v15/v16) |
| `frappe-impl-serverscripts` | Server Scripts — document validation, API scripts, scheduled scripts, sandbox patterns |
| `frappe-impl-ui-components` | Custom dialogs, List View extensions, Page controllers, Dashboard widgets |
| `frappe-impl-website` | Portal pages, Web Forms, website routes, themes, SEO configuration |
| `frappe-impl-whitelisted` | `@frappe.whitelist()` — endpoint design, auth, guest access, file upload APIs |
| `frappe-impl-workflow` | Approval chains, state-based transitions, Workflow Action DocType, conditions |
| `frappe-impl-workspace` | Workspace pages (Frappe Desk) — shortcuts, number cards, charts, custom HTML, role-based access, fixtures |

---

### ⚙️ Ops — 9 skills
Running, deploying, and maintaining Frappe in production.

| Skill | What it handles |
| :--- | :--- |
| `frappe-ops-app-lifecycle` | App scaffolding, settings, asset builds, `bench migrate`, multi-site management |
| `frappe-ops-backup` | Backup config, restore, encryption, scheduling, S3 offsite backup |
| `frappe-ops-bench` | bench commands reference, multi-tenancy, DNS-based multitenancy, common flags |
| `frappe-ops-cloud` | Frappe Cloud / Press API, site provisioning, bench management on cloud |
| `frappe-ops-deployment` | Production deploy — Nginx, Supervisor, SSL, environment config, zero-downtime |
| `frappe-ops-frontend-build` | Asset bundling — `build.json` (v14) → esbuild (v15+), watch mode, custom bundles |
| `frappe-ops-performance` | MariaDB tuning, Redis sizing, Gunicorn workers, CDN, slow query analysis |
| `frappe-ops-upgrades` | Major version upgrades v14→v15→v16 — checklist, common post-upgrade failures |
| `frappe-ops-website-deploy` | Deploy HTML/CSS as Frappe Web Pages via REST API |

---

### 🔌 Project — 3 skills
Utilities for making live calls against a running Frappe instance.

| Skill | What it enables |
| :--- | :--- |
| `project-curl` | HTTP requests with curl using the project's API credentials |
| `project-npx` | Node.js CLI tools via npx in this project |
| `project-rest-api` | REST API calls from JS/TS, Python, or shell using project credentials |

---

### 📐 Syntax — 13 skills
Correct syntax patterns for every Frappe surface. Reference these when
writing code to get the API and structure right the first time.

| Skill | What it covers |
| :--- | :--- |
| `frappe-syntax-clientscripts` | JS for form events, field manipulation, `frappe.call`, child table access |
| `frappe-syntax-controllers` | Python Document Controllers — lifecycle methods, autoname, v16 `extend_doctype_class` |
| `frappe-syntax-customapp` | App structure, `pyproject.toml`, `hooks.py` skeleton, fixtures, `__init__.py` |
| `frappe-syntax-doctypes` | DocType JSON — fieldtypes, options, permissions, `in_list_view`, autoname strategies |
| `frappe-syntax-hooks` | `hooks.py` — all hook keys, correct dict structure, multi-app patterns |
| `frappe-syntax-hooks-events` | `doc_events` lifecycle — which hook fires when, order of execution across apps |
| `frappe-syntax-jinja` | Jinja for Print Formats, Email Templates, Portal Pages, custom methods |
| `frappe-syntax-print` | Print formats, PDF generation, print styles, letterheads (v14/v15/v16) |
| `frappe-syntax-query-builder` | `frappe.qb` (PyPika-based) — joins, filters, aggregates, subqueries |
| `frappe-syntax-reports` | Query Reports, Script Reports, Report Builder — column/filter definitions |
| `frappe-syntax-scheduler` | `scheduler_events` syntax, cron expressions, `enqueue`, retry configuration |
| `frappe-syntax-serverscripts` | Server Script Python — restricted sandbox, available globals, safe patterns |
| `frappe-syntax-whitelisted` | `@frappe.whitelist()` — method signatures, auth context, response format |

---

### 🧪 Testing — 2 skills

| Skill | What it covers |
| :--- | :--- |
| `frappe-testing-cicd` | GitHub Actions for Frappe apps — test matrix, MariaDB service container, bench setup |
| `frappe-testing-unit` | Unit & integration tests, test fixtures, `frappe.tests.utils`, `setUp`/`tearDown` patterns |

---

### 🛠️ Tools — 4 skills
Document generation from Frappe.

| Skill | What it generates |
| :--- | :--- |
| `docx` | DOCX / Word documents |
| `pdf` | PDFs — generation, rendering, wkhtmltopdf patterns |
| `pptx` | PowerPoint presentations |
| `xlsx` | Excel spreadsheets |

---

## License

MIT
