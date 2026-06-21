# Frappe / ERPNext Manual Browser Install Skill

Use this skill when you need to set up a fresh Frappe site and complete the ERPNext setup wizard through the browser UI without getting stuck in a wizard/desk redirect loop.

## The core rule

`bench new-site` installs **only Frappe**. If you open the browser and log in before installing ERPNext, the wizard is Frappe-only. It completes and redirects to `/desk`, but ERPNext is not configured. Later, ERPNext redirects back to the wizard to finish its own setup, creating the "wizard > desk > wizard" loop.

**Always install ERPNext and any dependent custom apps via `bench install-app` BEFORE the first browser login.**

## Standard flow

```bash
# 1. Enter the Frappe bench container (adjust for your environment)
docker exec -it <frappe-container> bash
cd /path/to/bench

# 2. Create the fresh site (Frappe only)
bench new-site <site-name> \
  --admin-password <admin-password> \
  --db-root-password <db-root-password>

# 3. Install ERPNext BEFORE opening the browser
bench --site <site-name> install-app erpnext

# 4. Install any custom apps that depend on ERPNext
bench --site <site-name> install-app <custom-app-1>
bench --site <site-name> install-app <custom-app-2>

# 5. Start the dev server
bench start
```

Then open the browser:

```
http://<site-name>:<port>/login
Username: Administrator
Password: <admin-password>
```

## Expected browser wizard flow

| URL | Fields |
|-----|--------|
| `/desk/setup-wizard/0` | Language, Country, Time Zone, Currency |
| `/desk/setup-wizard/1` | Full Name, Email, Password |
| `/desk/setup-wizard/2` | Company Name, Abbreviation, Chart of Accounts, Fiscal Year Start |
| `/desk/setup-wizard/0` (progress bar) | Setup processing, then redirect to `/desk` |

## Loop-prevention checklist

Before telling the user to open the browser, verify **all** of these:

1. Required apps are installed:
   ```bash
   bench --site <site-name> list-apps
   ```
   Expected output includes `frappe`, `erpnext`, and any custom apps.

2. The wizard has not already completed:
   ```bash
   bench --site <site-name> console <<< "print(frappe.db.get_global('setup_complete'))"
   ```
   Expected output: `None`.

3. The dev server is running and the login page returns HTTP 200.

Only then open the browser.

## What the browser wizard actually does

The wizard is a UI over these API calls:

| Endpoint | Purpose |
|----------|---------|
| `frappe.desk.page.setup_wizard.setup_wizard.load_languages` | Load language list |
| `frappe.desk.page.setup_wizard.setup_wizard.load_user_details` | Load default user info |
| `frappe.desk.page.setup_wizard.setup_wizard.load_messages` | Load translations |
| `frappe.geo.country_info.get_country_timezone_info` | Country defaults |
| `erpnext.accounts.doctype.account.chart_of_accounts.chart_of_accounts.get_charts_for_country` | Available COA templates |
| `frappe.core.doctype.user.user.test_password_strength` | Live password meter |
| `frappe.desk.page.setup_wizard.setup_wizard.setup_complete` | **Final POST that writes company, COA, fiscal year, user, and `setup_complete=true`** |

## Should an agent call `setup_complete` directly?

**Generally no.** The `setup_complete` endpoint is what the browser wizard calls after collecting data. An agent should use the bench CLI flow above.

Call `setup_complete` directly only if you are:
- Already logged in as Administrator,
- ERPNext is installed,
- You have all required setup data (country, timezone, currency, language, full name, email, password, company name, company abbreviation, chart of accounts, fiscal year start/end, demo flag).

## Anti-patterns to avoid

- Do not log in to a Frappe-only site and expect ERPNext setup options.
- Do not open the browser wizard before `bench install-app erpnext`.
- Do not install a custom ERPNext-dependent app before installing ERPNext.

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Wizard shows only Language/Country/User, no Company/COA | ERPNext not installed | Drop site, recreate, install ERPNext first |
| `/desk` redirects back to `/desk/setup-wizard/` | `setup_complete` missing or ERPNext not installed | Check `bench list-apps`; reinstall from scratch if needed |
| Login page returns 500 / DB access denied | Wrong `default_site` in `common_site_config.json` | Ensure `default_site` points to your site |

## Environment-specific notes

- **Docker devcontainer (used by MoonTec):** container `frappe_docker_devcontainer-frappe-1`, bench `/workspace/development/16`, MariaDB root password defaults to `123`.
- **Non-Docker:** adjust container/paths/passwords accordingly.
