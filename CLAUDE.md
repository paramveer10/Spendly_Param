# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Run the app
python app.py          # runs on http://localhost:5001

# Run all tests
pytest

# Run a single test file
pytest tests/test_auth.py

# Run a single test by name
pytest -k "test_login"
```

## Architecture

**Spendly** is a Flask + SQLite expense tracker built as a step-by-step teaching scaffold. The UI and route stubs exist, but most backend logic is unimplemented — that is by design.

### Request flow

1. [app.py](app.py) — all Flask routes live here. Only `GET /`, `GET /register`, and `GET /login` are wired up; they render templates but have no POST handling yet. All other routes return placeholder strings (`"coming in Step X"`).
2. [database/db.py](database/db.py) — **currently empty**. Three functions must be implemented here:
   - `get_db()` — returns a SQLite connection with `row_factory = sqlite3.Row` and `PRAGMA foreign_keys = ON`
   - `init_db()` — creates tables using `CREATE TABLE IF NOT EXISTS`
   - `seed_db()` — inserts sample rows for development
3. Templates use Jinja2 inheritance: [templates/base.html](templates/base.html) defines navbar/footer/script blocks; all pages `{% extends "base.html" %}`.
4. Auth forms (`register.html`, `login.html`) POST to `/register` and `/login` and display `{{ error }}` when that template variable is set.

### Expected database schema

- `users` — id, name, email, password_hash
- `expenses` — id, user_id (FK → users.id), amount, category, date, description

### Currencies and locale

All monetary values are in Indian Rupees (₹). Use `₹` symbol, not `$`.

### Implementing stub routes

When a stub route is ready to be implemented:
1. Add `methods=['GET', 'POST']` to the `@app.route` decorator.
2. Add form-handling logic in the POST branch; keep the GET branch rendering the template.
3. Use `get_db()` from `database/db.py` for all database access.
4. Sessions require `app.config['SECRET_KEY']` to be set before `flask.session` works.
5. Use `werkzeug.security.generate_password_hash` / `check_password_hash` for passwords (werkzeug is already a dependency).
