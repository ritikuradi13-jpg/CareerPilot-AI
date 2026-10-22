# CareerPilot AI

CareerPilot AI is a full-stack starter web app for tailoring CVs and cover letters to a job description and destination country. It includes user accounts, saved applications, editable previews, and PDF/DOCX exports.

## Features

- Email/password accounts; passwords stored as Werkzeug password hashes.
- Upload PDF, DOCX, or TXT CVs (8 MB default limit).
- Country selector covering common destinations worldwide.
- AI-generated tailored CV, cover letter, and match/country notes.
- Evidence-preserving prompt: the AI must not invent dates, skills, employers, credentials, achievements, or work authorization.
- Editable outputs and saved drafts.
- Download each document as PDF or DOCX.
- Uploaded files are processed in memory and not saved as files.
- Per-user ownership checks on viewing, editing, deleting, and exporting saved documents.
- Basic security headers and secure cookie option.

## Run locally

Requires Python 3.10+.

```bash
python -m venv .venv
# macOS/Linux:
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1

pip install -r requirements.txt
cp .env.example .env
```

Edit `.env` and set:
- `OPENAI_API_KEY` to your API key.
- `FLASK_SECRET_KEY` to a long random secret (generate one with `python -c "import secrets; print(secrets.token_hex(32))"`).

Load environment variables before running. One simple option is to use `python-dotenv` (add it to requirements and call `load_dotenv()` at the top of `app.py`), or set variables in your shell. For example in PowerShell:

```powershell
$env:OPENAI_API_KEY="your-key"
$env:FLASK_SECRET_KEY="your-long-random-secret"
python app.py
```

macOS/Linux:

```bash
export OPENAI_API_KEY="your-key"
export FLASK_SECRET_KEY="$(python -c 'import secrets; print(secrets.token_hex(32))')"
python app.py
```

Open http://127.0.0.1:5000.

**Important:** `.env` is intentionally not auto-loaded by the app. Configure environment variables in your shell or hosting provider, or install `python-dotenv` and enable it explicitly.

## Production checklist — required before public launch

This is an implementation starter, not a deployed or security-audited production service.

1. Set `FLASK_DEBUG=0`, a strong `FLASK_SECRET_KEY`, `COOKIE_SECURE=1`, and serve only behind HTTPS.
2. Store `CAREERPILOT_DATA_DIR` on a persistent private volume with restrictive filesystem permissions. Back up and protect the database.
3. Add CSRF protection to all state-changing forms (for example Flask-WTF), rate limiting on sign-up/login/generation, account email verification, password reset, and login throttling.
4. Consider encrypting sensitive data at rest, define retention/deletion policies, and provide account deletion/export controls.
5. Review your AI provider's data-processing terms and configure the appropriate privacy settings. CV text and job descriptions are sent to the configured AI API for generation.
6. Validate country-specific employment, privacy, and anti-discrimination requirements. Never automatically add sensitive personal information just because a country convention may permit it.
7. Add automated tests, dependency scanning, monitoring, and error reporting that avoids logging CV contents.
8. Configure domain, deployment, persistent database, environment secrets, and any provider billing/API limits.

## Data notes

- Uploaded CVs are not saved as uploaded files. Extracted CV content is sent to the AI provider and is not stored as a source CV in the database by this starter.
- Generated CV and cover-letter text, country, job title, match notes, account email, and password hash are stored in SQLite.
- Deleting an application removes its generated document record from SQLite. Database backups must be handled separately.
- There is no job-application submission automation. Users review and submit documents themselves.

## Stack

- Flask, SQLite, OpenAI API
- `pypdf` and `python-docx` for reading uploads
- `reportlab` and `python-docx` for PDF/DOCX exports
- Responsive HTML/CSS templates
