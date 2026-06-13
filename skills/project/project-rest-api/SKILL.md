---
name: project-rest-api
description: >
  Use when making REST API calls programmatically (JS/TS, Python, shell) using the project's API credentials.
  Covers fetch, axios, Python requests, and Frappe-specific REST patterns.
  Keywords: REST API, HTTP client, fetch, axios, requests, API integration, authenticated API call.
license: MIT
compatibility: "Claude Code, Claude.ai Projects, Claude API."
metadata:
  author: Project
  version: "1.0"
---

# REST API Patterns with Project Credentials

> Deterministic patterns for making REST API calls using credentials from `Cred.md`.

---

## Decision Tree

```
What environment?
├── Browser / Frontend
│   ├── Modern browser → fetch() with credentials
│   └── Need interceptors → axios
│
├── Node.js / Server
│   ├── Native → fetch() (Node 18+) or node-fetch
│   └── Need ease → axios
│
├── Python / Scripting
│   └── requests library
│
└── Shell / One-off
    └── curl skill (see project-curl)
```

---

## Core Rules

### ALWAYS
- ✅ Read `Cred.md` first to get `BASE_URL`, `API_KEY`, `API_SECRET`
- ✅ Set `Authorization: token API_KEY:API_SECRET` header on every request
- ✅ Set `Content-Type: application/json` for JSON payloads
- ✅ Handle HTTP errors explicitly (4xx, 5xx)
- ✅ Parse and validate responses before using data
- ✅ Use `encodeURIComponent()` for document names in URLs

### NEVER
- ❌ Hardcode credentials in source code
- ❌ Send API_SECRET in URL query parameters
- ❌ Ignore network or timeout errors
- ❌ Trust response data without validation

---

## Credential Loading Pattern

### Python
```python
import os
import re

def load_credentials(cred_path="Cred.md"):
    """Load API credentials from Cred.md file."""
    with open(cred_path, "r") as f:
        content = f.read()
    
    creds = {}
    for line in content.strip().split("\n"):
        if ":" in line and not line.startswith("#"):
            key, value = line.split(":", 1)
            creds[key.strip()] = value.strip()
    
    return creds["BASE_URL"], creds["API_KEY"], creds["API_SECRET"]

BASE_URL, API_KEY, API_SECRET = load_credentials()
HEADERS = {
    "Authorization": f"token {API_KEY}:{API_SECRET}",
    "Content-Type": "application/json",
}
```

### JavaScript / TypeScript
```typescript
import fs from "fs";
import path from "path";

function loadCredentials(credPath = "Cred.md"): { baseUrl: string; apiKey: string; apiSecret: string } {
  const content = fs.readFileSync(path.resolve(credPath), "utf-8");
  const lines = content.split("\n");
  const creds: Record<string, string> = {};
  
  for (const line of lines) {
    const idx = line.indexOf(":");
    if (idx > 0 && !line.startsWith("#")) {
      const key = line.slice(0, idx).trim();
      const value = line.slice(idx + 1).trim();
      creds[key] = value;
    }
  }
  
  return {
    baseUrl: creds.BASE_URL,
    apiKey: creds.API_KEY,
    apiSecret: creds.API_SECRET,
  };
}

const { baseUrl, apiKey, apiSecret } = loadCredentials();
const HEADERS = {
  Authorization: `token ${apiKey}:${apiSecret}`,
  "Content-Type": "application/json",
};
```

---

## Method Patterns

### JavaScript / TypeScript (fetch)

#### GET
```typescript
async function getList(doctype: string, filters?: any[]) {
  const url = new URL(`/api/resource/${encodeURIComponent(doctype)}`, baseUrl);
  if (filters) {
    url.searchParams.append("filters", JSON.stringify(filters));
  }
  
  const res = await fetch(url.toString(), { headers: HEADERS });
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  return res.json();
}
```

#### POST — Create
```typescript
async function createDoc(doctype: string, data: Record<string, any>) {
  const res = await fetch(`${baseUrl}/api/resource/${encodeURIComponent(doctype)}`, {
    method: "POST",
    headers: HEADERS,
    body: JSON.stringify(data),
  });
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  return res.json();
}
```

#### PUT — Update
```typescript
async function updateDoc(doctype: string, name: string, data: Record<string, any>) {
  const res = await fetch(
    `${baseUrl}/api/resource/${encodeURIComponent(doctype)}/${encodeURIComponent(name)}`,
    {
      method: "PUT",
      headers: HEADERS,
      body: JSON.stringify(data),
    }
  );
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  return res.json();
}
```

#### DELETE
```typescript
async function deleteDoc(doctype: string, name: string) {
  const res = await fetch(
    `${baseUrl}/api/resource/${encodeURIComponent(doctype)}/${encodeURIComponent(name)}`,
    {
      method: "DELETE",
      headers: { Authorization: HEADERS.Authorization },
    }
  );
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  return res.json();
}
```

#### RPC Method Call
```typescript
async function callMethod(method: string, args?: Record<string, any>) {
  const res = await fetch(`${baseUrl}/api/method/${method}`, {
    method: "POST",
    headers: HEADERS,
    body: args ? JSON.stringify(args) : undefined,
  });
  if (!res.ok) throw new Error(`HTTP ${res.status}: ${await res.text()}`);
  const data = await res.json();
  return data.message;  // Frappe wraps RPC responses in { message: ... }
}
```

---

### Python (requests)

#### GET
```python
import requests

def get_list(doctype, filters=None, fields=None):
    params = {}
    if filters:
        params["filters"] = json.dumps(filters)
    if fields:
        params["fields"] = json.dumps(fields)
    
    resp = requests.get(
        f"{BASE_URL}/api/resource/{doctype}",
        headers=HEADERS,
        params=params,
    )
    resp.raise_for_status()
    return resp.json()["data"]
```

#### POST — Create
```python
def create_doc(doctype, data):
    resp = requests.post(
        f"{BASE_URL}/api/resource/{doctype}",
        headers=HEADERS,
        json=data,
    )
    resp.raise_for_status()
    return resp.json()["data"]
```

#### PUT — Update
```python
def update_doc(doctype, name, data):
    resp = requests.put(
        f"{BASE_URL}/api/resource/{requests.utils.quote(doctype)}/{requests.utils.quote(name)}",
        headers=HEADERS,
        json=data,
    )
    resp.raise_for_status()
    return resp.json()["data"]
```

#### DELETE
```python
def delete_doc(doctype, name):
    resp = requests.delete(
        f"{BASE_URL}/api/resource/{doctype}/{name}",
        headers={"Authorization": HEADERS["Authorization"]},
    )
    resp.raise_for_status()
    return resp.json()
```

#### RPC Method Call
```python
def call_method(method, **kwargs):
    resp = requests.post(
        f"{BASE_URL}/api/method/{method}",
        headers=HEADERS,
        json=kwargs,
    )
    resp.raise_for_status()
    return resp.json()["message"]  # Frappe wraps in { message: ... }
```

---

### File Upload (Python)

```python
import requests

def upload_file(file_path, is_private=1, folder="Home"):
    with open(file_path, "rb") as f:
        resp = requests.post(
            f"{BASE_URL}/api/method/upload_file",
            headers={"Authorization": HEADERS["Authorization"]},
            files={"file": f},
            data={"is_private": is_private, "folder": folder},
        )
    resp.raise_for_status()
    return resp.json()["message"]
```

---

## Error Handling

### Python
```python
try:
    data = get_list("User")
except requests.exceptions.HTTPError as e:
    if e.response.status_code == 401:
        print("Authentication failed — check Cred.md")
    elif e.response.status_code == 403:
        print("Permission denied")
    elif e.response.status_code == 404:
        print("Resource not found")
    else:
        print(f"API error: {e}")
except requests.exceptions.RequestException as e:
    print(f"Network error: {e}")
```

### JavaScript
```typescript
try {
  const data = await getList("User");
} catch (err: any) {
  if (err.message.includes("401")) {
    console.error("Authentication failed — check Cred.md");
  } else if (err.message.includes("403")) {
    console.error("Permission denied");
  } else if (err.message.includes("404")) {
    console.error("Resource not found");
  } else {
    console.error("API error:", err);
  }
}
```

---

## Response Shape

Frappe REST responses follow this structure:

```json
{
  "data": { ... },           // For CRUD on single docs
  "data": [ { ... }, ... ],  // For list endpoints
  "message": { ... },        // For RPC method calls
  "docs": [ ... ]            // For some bulk operations
}
```

Always extract the inner payload:
- `response.data` for document CRUD
- `response.data` (array) for lists
- `response.message` for RPC calls

---

## Security Notes

- Never commit `Cred.md` — add it to `.gitignore`
- Never log `API_SECRET` in error messages or console output
- Use environment variables in production: `process.env.API_SECRET` or `os.environ["API_SECRET"]`
- Validate and sanitize all user inputs before including in API calls
- Use HTTPS for all production API calls (verify `BASE_URL` starts with `https://`)
