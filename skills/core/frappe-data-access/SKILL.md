---
name: frappe-data-access
description: >
  Access Frappe instance data via MCP tools, REST API, bench console, or bench mariadb.
  Use when the user asks to query, create, update, or delete Frappe documents, inspect DocType schemas,
  run reports, call whitelisted methods, debug with ORM queries, or troubleshoot site data.
  Also use when MCP auth fails and credentials need updating.
  Keywords: frappe data, query documents, DocType schema, bench console, bench mariadb, MCP, REST API,
  whitelisted method, report, ORM debug, data troubleshooting.
license: MIT
compatibility: "Claude Code, Claude.ai Projects, Claude API. Frappe v15+."
metadata:
  author: frappe-skillhq
  version: "1.0"
---

# Frappe Data Access

Four access methods in priority order:

1. **Frappe MCP** (preferred) -- structured tool calls via `user-frappe` server
2. **REST API** (fallback) -- direct HTTP calls when MCP is unavailable
3. **bench console** (debugging) -- Python shell with Frappe ORM for framework-aware inspection
4. **bench mariadb** (validation only) -- read-only SQL for troubleshooting

## 1. Frappe MCP (Primary)

Implementation: **[frappe-mcp-server](https://github.com/Sena-Services/frappe-mcp-server)** by Sena Services (`npx frappe-mcp-server`). Connects to Frappe via its REST API using API key/secret auth. Requires Frappe 15+.

MCP server identifier: **`user-frappe`**

Config file: `~/.cursor/mcp.json`

### Connectivity check

Always verify the connection first:

```
CallMcpTool  server: "user-frappe"  toolName: "ping"  arguments: {}
```

Expected response: `pong`

### Available tools

| Tool | Required params | Purpose |
|------|----------------|---------|
| `ping` | -- | Health check |
| `version` | -- | Server version |
| `get_doctype_schema` | `doctype` | Full field definitions, validations, links |
| `find_doctypes` | `search_term` | Search DocTypes by name |
| `get_module_list` | -- | All installed modules |
| `get_doctypes_in_module` | `module` | DocTypes in a module |
| `check_doctype_exists` | `doctype` | Boolean existence check |
| `get_field_options` | `doctype`, `fieldname` | Options for a Select/Link field |
| `get_required_fields` | `doctype` | Only required fields |
| `get_naming_info` | `doctype` | Naming rule info |
| `get_frappe_usage_info` | `doctype` or `workflow` | Schema + usage hints |
| `list_documents` | `doctype` | List with filters, fields, pagination |
| `get_document` | `doctype`, `name` | Single document (name is case-sensitive) |
| `create_document` | `doctype`, `values` | Create new document |
| `update_document` | `doctype`, `name`, `values` | Update existing document |
| `delete_document` | `doctype`, `name` | Delete document |
| `check_document_exists` | `doctype`, `name` | Boolean existence check |
| `get_document_count` | `doctype` | Count with optional filters |
| `call_method` | `method` | Call any whitelisted Python method |
| `list_reports` | -- | All available reports |
| `run_query_report` | `report_name` | Execute a query report |
| `run_doctype_report` | `doctype` | Report on a DocType |
| `get_report_meta` | `report_name` | Report metadata |
| `get_report_columns` | `report_name` | Column definitions |
| `export_report` | `report_name` | Export report data |
| `get_financial_statements` | -- | Financial statements |
| `get_api_instructions` | -- | API usage guidance |

### Common patterns

**Discover schema before writing:**

```
CallMcpTool  server: "user-frappe"  toolName: "get_doctype_schema"
  arguments: { "doctype": "Customer" }
```

**List with filters and field selection:**

```
CallMcpTool  server: "user-frappe"  toolName: "list_documents"
  arguments: {
    "doctype": "Customer",
    "filters": { "customer_group": "Commercial" },
    "fields": ["name", "customer_name", "territory"],
    "limit": 10,
    "order_by": "modified desc"
  }
```

Filter operators: `=`, `!=`, `<`, `>`, `<=`, `>=`, `like`, `not like`, `in`, `not in`, `is`, `is not`, `between`. Use array format for operators: `{"field": [">", "value"]}`.

**Call a whitelisted method:**

```
CallMcpTool  server: "user-frappe"  toolName: "call_method"
  arguments: { "method": "frappe.auth.get_logged_user" }
```

### Best practices (from frappe-mcp-server docs)

1. **Check schema first** -- call `get_doctype_schema` or `get_required_fields` before creating/updating documents.
2. **Specify fields** -- only request fields you need for better performance.
3. **Paginate** -- use `limit` and `limit_start` on `list_documents`.
4. **Check existence** -- use `check_document_exists` before update/delete.
5. **Use `get_frappe_usage_info`** -- combines schema metadata, static hints, and app-provided usage guidance for richer context.

### Handling auth failures

If any MCP call returns an auth error (401, 403, or credential-related message):

1. Tell the user the current API credentials are invalid.
2. Ask for a new `FRAPPE_API_KEY` and `FRAPPE_API_SECRET` pair.
   - Generated from Frappe User record > API Access > Generate Keys.
3. Update `~/.cursor/mcp.json` -- replace the values in `mcpServers.frappe.env`:

```json
{
  "mcpServers": {
    "frappe": {
      "command": "npx",
      "args": ["frappe-mcp-server"],
      "env": {
        "FRAPPE_URL": "http://localhost:8001",
        "FRAPPE_API_KEY": "<NEW_KEY>",
        "FRAPPE_API_SECRET": "<NEW_SECRET>"
      },
      "disabled": false,
      "alwaysAllow": []
    }
  }
}
```

4. After updating, inform the user they need to restart the MCP server (reload Cursor window or toggle the server) for the new credentials to take effect.
5. Re-run `ping` to verify.

### If MCP is unavailable

If the `user-frappe` MCP server is not running, not listed in available servers, or the user explicitly asks for REST/curl access, fall back to the REST API. See [references/rest-api.md](references/rest-api.md).

## 2. REST API (Fallback)

Use when MCP is unavailable or user explicitly requests HTTP/curl access.

### Authentication

Every request requires token auth:

```http
Authorization: token <API_KEY>:<API_SECRET>
Content-Type: application/json
Accept: application/json
```

API keys are generated from the Frappe User record under **API Access > Generate Keys**.

### Endpoints

```
GET    /api/resource/<DOCTYPE>           # list documents
GET    /api/resource/<DOCTYPE>/<NAME>    # get single document
POST   /api/resource/<DOCTYPE>           # create document
PUT    /api/resource/<DOCTYPE>/<NAME>    # update document
DELETE /api/resource/<DOCTYPE>/<NAME>    # delete document
GET    /api/method/<dotted.python.path>  # call whitelisted method
POST   /api/method/<dotted.python.path>  # call whitelisted method
```

List query params: `fields` (JSON array), `filters` (JSON array of arrays), `limit_page_length` (int), `limit_start` (int), `order_by` (string).

### Response format

- Resource list/CRUD endpoints return results under the **`data`** key.
- Method endpoints (`/api/method/`) return results under the **`message`** key.

### curl examples

**Verify auth:**
```bash
curl -s "http://localhost:8001/api/method/frappe.auth.get_logged_user" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

**List documents:**
```bash
curl -s "http://localhost:8001/api/resource/Customer?fields=[\"name\",\"customer_name\"]&limit_page_length=10" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

**Get one document:**
```bash
curl -s "http://localhost:8001/api/resource/Customer/CUST-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

**Create document:**
```bash
curl -s -X POST "http://localhost:8001/api/resource/Lead" \
  -H "Authorization: token KEY:SECRET" \
  -H "Content-Type: application/json" \
  -d '{"lead_name":"John Doe","email_id":"john@example.com"}'
```

**Update document:**
```bash
curl -s -X PUT "http://localhost:8001/api/resource/Lead/LEAD-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Content-Type: application/json" \
  -d '{"status":"Converted"}'
```

**Delete document:**
```bash
curl -s -X DELETE "http://localhost:8001/api/resource/Lead/LEAD-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

## 3. Bench Console (Framework-Aware Debugging)

Use when you need ORM-level inspection — checking documents via `frappe.get_doc`, running `frappe.get_all` with filters, inspecting metadata, or validating app behavior after patches/migrations. Preferred over raw SQL because queries execute through Frappe's document model, permissions system, and hooks.

**Good for:** checking document existence, inspecting fields via ORM, filtered queries, record counts, DocType metadata, testing server-side snippets, investigating issues after patches.

**Avoid for:** uncontrolled bulk production updates, intentionally bypassing business logic, or changes that belong in patches/migration scripts.

### Setup

```bash
cd /path/to/bench
cat sites/currentsite.txt        # confirm active site
bench --site <SITE_NAME> console
```

### Essential operations

```python
# Existence check
frappe.db.exists("Customer", "CUST-0001")

# Fetch document
doc = frappe.get_doc("Customer", "CUST-0001")

# Query with filters
frappe.get_all("Sales Invoice", filters={"status": "Paid"}, fields=["name", "customer"], limit=10)

# Count records
frappe.db.count("Lead")

# Inspect DocType metadata
meta = frappe.get_meta("Customer")
[f.fieldname for f in meta.fields]

# Write (use deliberately — always commit)
doc = frappe.get_doc({"doctype": "ToDo", "description": "Test"})
doc.insert()
frappe.db.commit()   # console does NOT auto-commit
```

**Always call `frappe.db.commit()` after any write** — the console does not auto-commit.

### When to use alternatives instead

- Raw SQL inspection → use `bench mariadb`
- External automation → use REST API or MCP
- Interactive UI debugging → browser System Console

## 4. Bench MariaDB (Validation Only)

Use **only for read-only SQL inspection** when troubleshooting raw database state. Never write directly to the database — use Frappe APIs, patches, or bench commands instead.

**Frappe prefixes all DocType table names with `tab`** — e.g. `Customer` → `tabCustomer`, `Sales Invoice` → `tabSales Invoice`.

### Setup

```bash
cd /path/to/bench
cat sites/currentsite.txt        # confirm active site
bench --site <SITE_NAME> mariadb
```

### Permitted queries

```sql
SHOW TABLES LIKE 'tabCustomer%';
DESC tabCustomer;
SELECT name, customer_name FROM tabCustomer LIMIT 10;
SELECT COUNT(*) FROM `tabSales Invoice` WHERE status = 'Paid';
```

### Prohibited commands

Never run: `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, `ALTER`, or `DROP`. Use Frappe REST API, MCP tools, `bench execute`, app patches, the DocType editor, or migrations for any data or schema changes.

Exit with `\q` or `exit`.

## Decision flowchart

1. Is the `user-frappe` MCP server available? → Use MCP tools.
2. MCP auth failed? → Ask user for new key/secret, update MCP config.
3. MCP unavailable entirely? → Use REST API via curl.
4. Need framework-aware debugging (ORM, metadata, hooks)? → Use `bench console`.
5. Need to inspect raw DB state? → Use `bench mariadb` (read-only).
6. User explicitly asks for curl/REST? → Use REST API.
