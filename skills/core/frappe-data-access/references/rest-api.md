# Frappe REST API Reference

## Authentication

Every request requires token auth:

```http
Authorization: token <API_KEY>:<API_SECRET>
Content-Type: application/json
Accept: application/json
```

API keys are generated from the User record under API Access in Frappe.

Docs: https://docs.frappe.io/framework/user/en/guides/integration/rest_api/token_based_authentication

---

## Endpoints

### List documents

```http
GET /api/resource/<DOCTYPE>
```

Returns paginated results. Default page size is 20, default fields is `name` only.

Query params:

| Param | Format | Example |
|-------|--------|---------|
| `fields` | JSON array | `["name","customer_name"]` |
| `filters` | JSON array of arrays | `[["Customer","customer_group","=","Commercial"]]` |
| `limit_page_length` | integer | `50` |
| `limit_start` | integer | `0` |
| `order_by` | string | `modified desc` |

### Get single document

```http
GET /api/resource/<DOCTYPE>/<DOCNAME>
```

### Create document

```http
POST /api/resource/<DOCTYPE>
Content-Type: application/json

{"field": "value"}
```

### Update document

```http
PUT /api/resource/<DOCTYPE>/<DOCNAME>
Content-Type: application/json

{"field": "new_value"}
```

### Delete document

```http
DELETE /api/resource/<DOCTYPE>/<DOCNAME>
```

### Call whitelisted method

```http
GET  /api/method/<dotted.python.path>
POST /api/method/<dotted.python.path>
```

Custom app methods and server scripts are also available under `/api/method/`.

Docs: https://docs.frappe.io/framework/user/en/api/rest

---

## Curl examples

### Verify auth

```bash
curl -s "http://localhost:8001/api/method/frappe.auth.get_logged_user" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

### List documents

```bash
curl -s "http://localhost:8001/api/resource/Customer?fields=[\"name\",\"customer_name\"]&limit_page_length=10" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

### Get one document

```bash
curl -s "http://localhost:8001/api/resource/Customer/CUST-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

### Create document

```bash
curl -s -X POST "http://localhost:8001/api/resource/Lead" \
  -H "Authorization: token KEY:SECRET" \
  -H "Content-Type: application/json" \
  -d '{"lead_name":"John Doe","email_id":"john@example.com"}'
```

### Update document

```bash
curl -s -X PUT "http://localhost:8001/api/resource/Lead/LEAD-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Content-Type: application/json" \
  -d '{"status":"Converted"}'
```

### Delete document

```bash
curl -s -X DELETE "http://localhost:8001/api/resource/Lead/LEAD-0001" \
  -H "Authorization: token KEY:SECRET" \
  -H "Accept: application/json"
```

---

## Response format

- Resource list endpoints return JSON under `data` key.
- Method endpoints return JSON under `message` key.
- Permissions of the API-key user apply to all requests.

Docs: https://docs.frappe.io/framework/user/en/guides/integration/rest_api
