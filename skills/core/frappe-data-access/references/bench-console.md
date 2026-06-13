# Bench Console -- Framework-Aware Debugging

`bench console` opens an interactive Python shell with Frappe loaded for a specific site. Use it for ORM queries, metadata inspection, document validation, and controlled troubleshooting. Preferred over raw SQL because it goes through Frappe's document model, permissions, and hooks.

Docs: https://docs.frappe.io/framework/user/en/bench/frappe-commands

---

## When to use

- Verify whether a document exists
- Inspect documents and their fields via ORM
- Run `frappe.get_all` with filters
- Validate counts and list results
- Check metadata (fields, DocType structure)
- Test small server-side snippets
- Debug behavior after patches, migrations, or API calls
- Check installed apps

## When NOT to use

- Uncontrolled bulk updates on production
- Bypassing business logic intentionally
- Anything that should be a patch, migration, or `bench execute` script

---

## Setup

### Ensure correct bench directory

```bash
cd /workspace/development/edge16
```

### Check active site

```bash
cat sites/currentsite.txt
```

### Set active site (if needed)

```bash
bench use <SITE_NAME>
```

### Open console (explicit site is safer)

```bash
bench --site <SITE_NAME> console
```

---

## Common operations

### Check if a document exists

```python
frappe.db.exists("Customer", "CUST-0001")
```

### Get a single document

```python
doc = frappe.get_doc("Customer", "CUST-0001")
doc.customer_name
```

### Get document as dict

```python
frappe.get_doc("Sales Order", "SAL-ORD-0001").as_dict()
```

### List documents with filters

```python
frappe.get_all(
    "Sales Invoice",
    filters={"status": "Paid"},
    fields=["name", "customer", "grand_total"],
    limit=10
)
```

### Count records

```python
frappe.db.count("Lead")
```

### Get a single field value

```python
frappe.db.get_value("Customer", "CUST-0001", "customer_name")
```

### Get multiple field values

```python
frappe.db.get_value(
    "Customer", "CUST-0001",
    ["customer_name", "customer_group", "territory"],
    as_dict=True
)
```

### Inspect child table rows

```python
doc = frappe.get_doc("Sales Order", "SAL-ORD-0001")
for item in doc.items:
    print(item.item_code, item.qty, item.rate)
```

### Check DocType metadata

```python
meta = frappe.get_meta("Customer")
[f.fieldname for f in meta.fields]
```

### List installed apps

```python
frappe.get_installed_apps()
```

### Check recently modified documents

```python
frappe.get_all(
    "ToDo",
    fields=["name", "description", "modified"],
    order_by="modified desc",
    limit=5
)
```

### Verify a custom field is populated

```python
frappe.get_all(
    "Quotation",
    filters={"custom_field_name": ["is", "set"]},
    fields=["name", "custom_field_name"],
    limit=10
)
```

---

## Controlled writes (use carefully)

Writes through `bench console` are better than raw SQL because they go through Frappe's document methods, but should still be deliberate.

### Create a document

```python
doc = frappe.get_doc({
    "doctype": "ToDo",
    "description": "Test item from bench console"
})
doc.insert()
frappe.db.commit()
```

### Update a document

```python
doc = frappe.get_doc("ToDo", "TDO-0001")
doc.description = "Updated description"
doc.save()
frappe.db.commit()
```

Always call `frappe.db.commit()` after writes -- the console does not auto-commit.

---

## Comparison with other tools

| Tool | Best for |
|------|----------|
| `bench console` | ORM queries, metadata, app-aware debugging |
| `bench mariadb` | Raw SQL read-only inspection |
| Frappe MCP / REST API | Programmatic CRUD from outside the server |
| System Console (Desk UI) | Browser-based Python debugging |

---

## Workflow

1. `cd` into bench directory.
2. Confirm site with `cat sites/currentsite.txt` or use `--site`.
3. Open with `bench --site <SITE_NAME> console`.
4. Run read-focused ORM checks.
5. Avoid unnecessary writes; move repeatable changes to app code or patches.
6. Exit with `exit()` or Ctrl-D.
