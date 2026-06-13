# Bench MariaDB -- Validation Only

Use `bench mariadb` **only for read-only inspection and troubleshooting**. Never write to the database directly for normal operations -- use Frappe APIs, patches, or bench commands instead so permissions, hooks, and validations are respected.

Docs: https://docs.frappe.io/framework/user/en/bench/resources/bench-commands-cheatsheet

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

### Open MariaDB console (explicit site is safer)

```bash
bench --site <SITE_NAME> mariadb
```

---

## Allowed operations (read-only)

| Operation | Example |
|-----------|---------|
| List tables | `SHOW TABLES;` |
| Describe table | `` DESC `tabCustomer`; `` |
| Count rows | `` SELECT COUNT(*) FROM `tabCustomer`; `` |
| View recent records | `` SELECT name, modified FROM `tabCustomer` ORDER BY modified DESC LIMIT 10; `` |
| Validate one record | `` SELECT name, customer_name FROM `tabCustomer` WHERE name = 'CUST-0001'; `` |
| Check child table | `` SELECT parent, item_code, qty FROM `tabSales Order Item` WHERE parent = 'SAL-ORD-0001'; `` |
| Check nulls | `` SELECT name FROM `tabLead` WHERE email_id IS NULL LIMIT 20; `` |
| Group counts | `` SELECT status, COUNT(*) AS count FROM `tabIssue` GROUP BY status ORDER BY count DESC; `` |

Frappe prefixes all DocType tables with `tab` (e.g., `Customer` -> `tabCustomer`).

---

## Forbidden operations

Do NOT run `INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`, `ALTER`, or any DDL/DML. Use these instead:

| Need | Use |
|------|-----|
| Create/update/delete docs | Frappe REST API or MCP tools |
| Patch data | `bench execute` or app patches |
| Schema changes | DocType editor or migrations |
| Workflow state changes | Frappe API |

---

## Workflow

1. `cd` into bench directory.
2. Confirm site with `cat sites/currentsite.txt` or use `--site`.
3. Open console with `bench mariadb` or `bench --site <site> mariadb`.
4. Run **read-only** queries only.
5. Exit with `\q` or `exit`.
