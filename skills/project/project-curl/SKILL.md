---
name: project-curl
description: >
  Use when making HTTP requests with curl using the project's API credentials.
  Covers authenticated requests, common HTTP methods, headers, and response handling.
  Keywords: curl, http, api request, authenticated curl, Bearer token, API key secret.
license: MIT
compatibility: "Claude Code, Claude.ai Projects, Claude API."
metadata:
  author: Project
  version: "1.0"
---

# curl Patterns with Project Credentials

> Deterministic patterns for making authenticated curl requests using credentials from `Cred.md`.

---

## Decision Tree

```
What do you need?
├── Read project credentials
│   └── Read Cred.md → BASE_URL, API_KEY, API_SECRET
│
├── Simple GET request
│   └── curl -s "$BASE_URL/api/resource/Dashboard" -H "Authorization: token $API_KEY:$API_SECRET"
│
├── POST/PUT/DELETE with JSON body
│   └── curl -s -X POST "$BASE_URL/api/method/frappe.client.insert" \\
│       -H "Content-Type: application/json" \\
│       -H "Authorization: token $API_KEY:$API_SECRET" \\
│       -d '{"doc": {"doctype": "Note", "title": "Test"}}'
│
├── Upload a file
│   └── curl -s -X POST "$BASE_URL/api/method/upload_file" \\
│       -H "Authorization: token $API_KEY:$API_SECRET" \\
│       -F "file=@/path/to/file.csv"
│
└── Save response to file
    └── curl -s -o output.json ...
```

---

## Core Rules

### ALWAYS
- ✅ Read `Cred.md` first to get `BASE_URL`, `API_KEY`, and `API_SECRET`
- ✅ Use `-s` (silent) flag in scripts to suppress progress meters
- ✅ Set `-H "Authorization: token $API_KEY:$API_SECRET"` for every request
- ✅ Set `-H "Content-Type: application/json"` for JSON payloads
- ✅ Use `-w "\\nHTTP_CODE: %{http_code}"` to capture response status
- ✅ Quote URLs and header values containing variables
- ✅ Use `-L` to follow redirects when appropriate

### NEVER
- ❌ Hardcode credentials in commands — always source from `Cred.md`
- ❌ Expose API_SECRET in logs or output
- ❌ Forget `-s` in non-interactive contexts
- ❌ Send sensitive data in URL query parameters

---

## Authentication Pattern

The project uses **Token Authentication** with `API_KEY:API_SECRET`.

```bash
# Source credentials (if running in shell)
BASE_URL=$(grep BASE_URL Cred.md | cut -d: -f2- | xargs)
API_KEY=$(grep API_KEY Cred.md | cut -d: -f2- | xargs)
API_SECRET=$(grep API_SECRET Cred.md | cut -d: -f2- | xargs)

# Use in curl
-H "Authorization: token ${API_KEY}:${API_SECRET}"
```

---

## Method Patterns

### GET — Fetch data
```bash
curl -s "$BASE_URL/api/resource/DocType" \
  -H "Authorization: token $API_KEY:$API_SECRET"
```

### GET with filters
```bash
curl -s "$BASE_URL/api/resource/ToDo?fields=[\"name\",\"status\"]&filters=[[\"status\",\"=\",\"Open\"]]" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  | python3 -m json.tool
```

### POST — Create document
```bash
curl -s -X POST "$BASE_URL/api/resource/Note" \
  -H "Content-Type: application/json" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  -d '{
    "title": "My Note",
    "content": "Note content here"
  }' | python3 -m json.tool
```

### PUT — Update document
```bash
curl -s -X PUT "$BASE_URL/api/resource/Note/My%20Note" \
  -H "Content-Type: application/json" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  -d '{"content": "Updated content"}' \
  | python3 -m json.tool
```

### DELETE — Remove document
```bash
curl -s -X DELETE "$BASE_URL/api/resource/Note/My%20Note" \
  -H "Authorization: token $API_KEY:$API_SECRET"
```

### POST — Call RPC method
```bash
curl -s -X POST "$BASE_URL/api/method/frappe.client.get_list" \
  -H "Content-Type: application/json" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  -d '{
    "doctype": "User",
    "fields": ["name", "email"],
    "limit_page_length": 10
  }' | python3 -m json.tool
```

### File Upload
```bash
curl -s -X POST "$BASE_URL/api/method/upload_file" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  -F "file=@/path/to/file.pdf" \
  -F "is_private=1" \
  -F "folder=Home" \
  | python3 -m json.tool
```

---

## Response Handling

### Pretty-print JSON
```bash
... | python3 -m json.tool
# Or with jq (if installed)
... | jq .
```

### Check HTTP status
```bash
HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" \
  "$BASE_URL/api/resource/User" \
  -H "Authorization: token $API_KEY:$API_SECRET")
```

### Save response + status
```bash
curl -s -w "\nHTTP_CODE: %{http_code}" \
  "$BASE_URL/api/resource/User" \
  -H "Authorization: token $API_KEY:$API_SECRET" \
  -o response.json
```

---

## Error Handling

| HTTP Code | Meaning | Action |
|-----------|---------|--------|
| 200 | Success | Parse response |
| 401 | Unauthorized | Check API_KEY and API_SECRET in Cred.md |
| 403 | Forbidden | Check permissions for the user/token |
| 404 | Not Found | Verify doctype name or document exists |
| 417 | Expectation Failed | Validation error; check request payload |
| 500 | Server Error | Check Frappe error logs |
| 429 | Too Many Requests | Implement backoff and retry |

---

## Security Notes

- `Cred.md` contains secrets — never commit it or log its contents
- Use `xargs` to trim whitespace when parsing Cred.md values
- In shell scripts, clear credential variables after use: `unset API_SECRET`
- Prefer `POST` with body over `GET` with query params for sensitive data
