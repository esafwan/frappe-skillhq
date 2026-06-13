---
name: project-npx
description: >
  Use when executing Node.js CLI tools via npx in this project.
  Covers running npx commands, handling arguments, and common npx workflows.
  Keywords: npx, npm exec, node cli, run script, package runner.
license: MIT
compatibility: "Claude Code, Claude.ai Projects, Claude API."
metadata:
  author: Project
  version: "1.0"
---

# npx Execution Patterns

> Deterministic patterns for running npx commands in this project.

---

## Decision Tree

```
What do you need?
├── Run a one-off CLI tool without installing
│   └── npx <package> [args]
│
├── Run a locally installed package
│   └── npx <package> [args]  (auto-resolves node_modules/.bin)
│
├── Run with a specific Node version
│   └── npx -p node@<version> <package>
│
├── Skip interactive prompts
│   └── npx --yes <package> [args]
│
└── Execute a script with npx
    └── npx tsx script.ts
```

---

## Core Rules

### ALWAYS
- ✅ Use `npx --yes <package>` to skip install confirmation in non-interactive contexts
- ✅ Prefer `npx` over global installs for one-off tools
- ✅ Check if the package is already in `package.json` before adding
- ✅ Use `npx <package>@<version>` when version pinning matters
- ✅ Quote arguments with spaces or special characters

### NEVER
- ❌ Run `npx` with `-g` or `--global` — npx is for local/temporary execution
- ❌ Install globally what can be run with npx
- ❌ Ignore exit codes from npx commands
- ❌ Run npx without `--yes` in automated scripts

---

## Common Patterns

### Run a TypeScript file directly
```bash
npx tsx path/to/script.ts
```

### Run a package temporarily
```bash
npx --yes prettier --write "src/**/*.ts"
```

### Run a specific version
```bash
npx --yes create-react-app@5.0.1 my-app
```

### Pass environment variables
```bash
NODE_ENV=production npx --yes vite build
```

### Pipe output through npx
```bash
cat data.json | npx --yes json-server --watch -
```

---

## Error Handling

| Error | Cause | Fix |
|-------|-------|-----|
| `command not found` | Package not installed or not in PATH | Use `--yes` or install the package |
| `EACCES` permission denied | File system permissions | Check directory permissions; avoid sudo |
| `ETIMEDOUT` | Network timeout | Retry, check proxy settings, or use offline cache |
| `ERR_REQUIRE_ESM` | CJS/ESM mismatch | Use `tsx` or specify `"type": "module"` |

---

## Project Context

- Always run npx from the project root or relevant subdirectory
- Respect `package.json` scripts before running npx directly
- If a `package-lock.json` or `pnpm-lock.yaml` exists, prefer `npm exec` or `pnpm exec` for consistency
