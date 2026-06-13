# frappe-skillhq

> Central command for Frappe / ERPNext development skills.

**frappe-skillhq** is a structured skill pack for AI coding agents working on
[Frappe](https://frappeframework.com) and [ERPNext](https://erpnext.com)
applications. It covers v14, v15, and v16 — from DocType controllers and hooks
to deployment, permissions, and the full workspace/desk UI.

69 skills across 9 categories, each designed to be invoked as a slash command
(`/frappe-impl-workspace`, `/frappe-errors-api`, etc.) from any compatible
coding agent.

---

## Install

```bash
git clone git@github.com:esafwan/frappe-skillhq.git ~/.ai-skills/frappe-skillhq

# Symlink into whichever agents you use
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.claude/skills/frappe-skillhq
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.cursor/skills/frappe-skillhq
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.kimi-code/skills/frappe-skillhq
ln -sfn ~/.ai-skills/frappe-skillhq/skills ~/.opencode/skills/frappe-skillhq
```

Or use [swarm++](https://github.com/esafwan/swarm-plus-plus) which wires this
automatically via `install.sh`.

---

## Skill Index

### 🤖 Agents — 5 skills
Meta-agents that orchestrate the rest of the skill catalog.

| Skill | Description |
| :--- | :--- |
| `frappe-agent-architect` | Multi-app architecture decisions — when to split, cross-app hooks, dependency management |
| `frappe-agent-debugger` | Debugging via bench console, traceback analysis, log files, pdb, VS Code DAP, profiling |
| `frappe-agent-interpreter` | Converts vague requirements into concrete technical specs mapped to the right skills |
| `frappe-agent-migrator` | Version upgrades v14→v15→v16 — breaking change detection, deprecated API mapping |
| `frappe-agent-validator` | Code review against all 69 skills — catches anti-patterns before deployment |

---

### 🧱 Core — 12 skills
Deep-dives into Frappe internals.

| Skill | Description |
| :--- | :--- |
| `frappe-core-api` | REST API, RPC API, authentication, webhooks, rate limiting (v14/v15/v16) |
| `frappe-core-cache` | Redis caching, `@redis_cache`, cache invalidation, distributed locking, TTL |
| `frappe-core-database` | `frappe.db`, ORM patterns, `frappe.get_doc`, raw SQL, transactions, performance |
| `frappe-core-files` | File uploads, attachments, private/public files, S3 storage, File DocType |
| `frappe-core-logging` | `frappe.logger()`, `frappe.log_error()`, Sentry integration, production patterns |
| `frappe-core-notifications` | `frappe.sendmail`, Notification DocType, Assignment Rules, Auto Repeat, ToDo |
| `frappe-core-permissions` | Roles, User Permissions, perm levels, data masking, `has_permission` hook |
| `frappe-core-search` | Link field search, global search, FTS5 (v15+), Awesomebar, website search |
| `frappe-core-translation` | `_()` / `__()`, CSV translation files, PO/MO files (v15+), RTL, bench commands |
| `frappe-core-utils` | `frappe.utils.*` — date/time, number/money, string, validation, file paths |
| `frappe-core-workflow` | Workflow DocType, states, transitions, conditions, permissions, Workflow Action |
| `frappe-data-access` | Query/create/update docs via MCP, REST API, bench console, bench mariadb |

---

### ❌ Errors — 7 skills
Diagnosis guides per surface — what went wrong and how to fix it.

| Skill | Description |
| :--- | :--- |
| `frappe-errors-api` | 401/403/404/417/500 — wrong token, missing `@whitelist`, validation errors |
| `frappe-errors-clientscripts` | TypeError, `frappe.call` failures, async/await mistakes, CSRF, child table errors |
| `frappe-errors-controllers` | Autoname failures, validate loops, wrong lifecycle hook, NestedSet, recursion |
| `frappe-errors-database` | DuplicateEntry, LinkValidation, deadlocks, SQL format (`%` vs `%s`), timeouts |
| `frappe-errors-hooks` | Hook not firing, circular imports, `app_include_js` paths, scheduler not running |
| `frappe-errors-permissions` | PermissionError (403), `has_permission` failures, User Permission over-restriction |
| `frappe-errors-serverscripts` | ImportError, NameError, sandbox violations, `doc_events` not firing, SQL injection |

---

### 🔨 Implementation — 14 skills
Step-by-step build guides.

| Skill | Description |
| :--- | :--- |
| `frappe-impl-clientscripts` | Field visibility, cascading filters, calculated fields, custom buttons, child tables |
| `frappe-impl-controllers` | Document Controllers — lifecycle hooks, validation, autoname, submittable, flags |
| `frappe-impl-customapp` | Build a Frappe app from scratch — `bench new-app`, structure, fixtures, packaging |
| `frappe-impl-hooks` | `hooks.py` — `doc_events`, `scheduler_events`, override/extend, permission hooks |
| `frappe-impl-integrations` | OAuth2, Connected Apps, Webhooks, Payment Gateways, Data Import/Export |
| `frappe-impl-jinja` | Print Formats, Email Templates, Notification templates, Portal Pages |
| `frappe-impl-reports` | Script Reports, Query Reports, dashboard charts, Number Cards |
| `frappe-impl-scheduler` | Scheduled tasks, background jobs, `enqueue`, cron syntax (v14/v15/v16) |
| `frappe-impl-serverscripts` | Server Scripts — document validation, API scripts, scheduled scripts |
| `frappe-impl-ui-components` | Custom dialogs, List View extensions, Page controllers, Dashboard widgets |
| `frappe-impl-website` | Portal pages, Web Forms, website routes, themes, SEO |
| `frappe-impl-whitelisted` | `@frappe.whitelist()` — endpoint design, auth, guest access, file upload APIs |
| `frappe-impl-workflow` | Approval chains, state-based transitions, Workflow Action, conditions |
| `frappe-impl-workspace` | Workspace pages, shortcuts, number cards, charts, custom HTML, role-based access |

---

### ⚙️ Ops — 9 skills
Running, deploying, and maintaining Frappe in production.

| Skill | Description |
| :--- | :--- |
| `frappe-ops-app-lifecycle` | App scaffolding, settings, asset builds, migrations, `bench migrate` |
| `frappe-ops-backup` | Backups, restore, encryption, scheduling, automated backup to S3 |
| `frappe-ops-bench` | bench commands, multi-site, multi-tenancy, DNS-based multitenancy |
| `frappe-ops-cloud` | Frappe Cloud / Press API, site provisioning, bench management |
| `frappe-ops-deployment` | Production deploy, Nginx, Supervisor, SSL, environment config |
| `frappe-ops-frontend-build` | Asset bundling — `build.json` (v14) → esbuild (v15+), Vite, watch mode |
| `frappe-ops-performance` | MariaDB tuning, Redis sizing, Gunicorn workers, CDN, query optimization |
| `frappe-ops-upgrades` | Major version upgrades v14→v15→v16, troubleshooting post-upgrade errors |
| `frappe-ops-website-deploy` | Deploy HTML/CSS sites as Frappe Web Pages via REST API |

---

### 🔌 Project — 3 skills
Project-level utilities for working against a live Frappe instance.

| Skill | Description |
| :--- | :--- |
| `project-curl` | HTTP requests with curl using project API credentials |
| `project-npx` | Node.js CLI tools via npx in this project |
| `project-rest-api` | REST API calls (JS/TS, Python, shell) using project credentials |

---

### 📐 Syntax — 13 skills
Correct syntax patterns for every Frappe surface, with common pitfalls.

| Skill | Description |
| :--- | :--- |
| `frappe-syntax-clientscripts` | JS for form events, field manipulation, `frappe.call`, child tables |
| `frappe-syntax-controllers` | Python Document Controllers — lifecycle hooks, autoname, v16 patterns |
| `frappe-syntax-customapp` | App structure, `pyproject.toml`, `hooks.py`, fixtures, `__init__.py` |
| `frappe-syntax-doctypes` | DocType JSON — fieldtypes, options, permissions, `in_list_view`, autoname |
| `frappe-syntax-hooks` | `hooks.py` — all hook keys, correct structure, multi-app patterns |
| `frappe-syntax-hooks-events` | `doc_events` lifecycle — which hook fires when, order of execution |
| `frappe-syntax-jinja` | Jinja for Print Formats, Email Templates, Portal Pages, custom methods |
| `frappe-syntax-print` | Print formats, PDF generation, print style, letterheads (v14/v15/v16) |
| `frappe-syntax-query-builder` | `frappe.qb` (PyPika-based) — joins, filters, aggregates, subqueries |
| `frappe-syntax-reports` | Query Reports, Script Reports, Report Builder config, columns/filters |
| `frappe-syntax-scheduler` | `scheduler_events` syntax, cron expressions, `enqueue`, retry config |
| `frappe-syntax-serverscripts` | Server Script Python — restricted sandbox, available globals, safe patterns |
| `frappe-syntax-whitelisted` | `@frappe.whitelist()` — method signatures, auth, response format |

---

### 🧪 Testing — 2 skills

| Skill | Description |
| :--- | :--- |
| `frappe-testing-cicd` | GitHub Actions for Frappe apps — test matrix, MariaDB service, bench setup |
| `frappe-testing-unit` | Unit & integration tests, test fixtures, `frappe.tests.utils`, `setUp` patterns |

---

### 🛠️ Tools — 4 skills
Document generation libraries.

| Skill | Description |
| :--- | :--- |
| `docx` | DOCX generation from Frappe |
| `pdf` | PDF generation and rendering |
| `pptx` | PowerPoint generation |
| `xlsx` | Excel/XLSX generation |

---

## License

MIT
