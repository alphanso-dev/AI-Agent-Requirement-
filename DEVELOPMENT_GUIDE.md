# Development Guide

The purpose of this guide is to define a clean, secure, easy-to-manage, and easy-to-deploy standard for new projects based on the structure used in this current project. This repository already follows a practical pattern with `app.py` as the entrypoint, a module-based backend inside `src/`, a frontend layer with `templates/` and `static/`, and separate areas for `auth`, `admin`, `api`, and `storage`. New projects should follow the same base pattern by default.

## 1. Main Development Principles

- Keep `app.py` thin. It should contain app bootstrap, middleware, router include, static mount, and error handlers.
- Split business logic inside `src/` module by module.
- Keep configuration fully `.env` driven and centralize it inside `src/config.py`.
- Route files should handle request/response flow only; heavy logic should move to a service or helper layer.
- Keep Auth, Admin, Public Web, API, Storage, and Background Processing logically separate.
- Even if Template UI and API live in the same project, routing should remain clear and organized.
- Do not add security later as an afterthought; enforce the baseline from day one.

## 2. Standard Project Structure

Recommended structure based on the current project:

```text
project-root/
|-- app.py
|-- logger.py
|-- requirements.txt
|-- .env
|-- DEVELOPMENT_GUIDE.md
|-- src/
|   |-- config.py
|   |-- router.py
|   |-- auth/
|   |   |-- routes.py
|   |   |-- dependencies.py
|   |   |-- database.py
|   |   |-- utils.py
|   |   `-- email.py
|   |-- admin/
|   |   |-- routes.py
|   |   `-- auth.py
|   |-- api/
|   |   |-- router.py
|   |   |-- auth.py
|   |   |-- file_process.py
|   |   `-- <feature>.py
|   |-- <feature_module>/
|   |   |-- routes.py
|   |   |-- schema.py
|   |   |-- renderer.py / service.py
|   |   `-- utils.py
|   |-- storage/
|   |   |-- db.py
|   |   |-- file_io.py
|   |   |-- s3.py
|   |   `-- index.py
|   |-- scripts/
|   `-- utils.py
|-- templates/
|   |-- base.html
|   |-- auth/
|   |-- admin/
|   |-- errors/
|   `-- <feature>/
|-- static/
|   |-- style.css
|   |-- js/
|   `-- favicon.ico
|`-- storage/
```

## 3. Folder Responsibility

- `app.py`: app init, CORS, middleware, exception handlers, static mount, router mount.
- `src/router.py`: central router that includes all route modules.
- `src/config.py`: env variables, constants, paths, storage/backend settings.
- `src/auth/`: registration, login, session/cookie, email verification, role checks.
- `src/admin/`: admin panel routes and admin-only access.
- `src/api/`: JSON APIs for third-party, Laravel, frontend, or mobile integration.
- `src/<feature_module>/`: domain-specific logic such as `pdf`, `division`, or `project`.
- `src/storage/`: DB CRUD, file storage abstraction, local/S3 switching.
- `templates/`: server-rendered pages.
- `static/`: CSS, JS, favicon, images.
- `storage/`: processing temp data, OCR cache, uploads, persistent file/object storage abstraction.

## 4. New Project Start Strategy

Follow this order for every new project:

1. Define the project type: web app, admin portal, API-only, or hybrid app.
2. Define the `.env` variable list first.
3. Centralize all settings in `src/config.py`.
4. Set the route aggregation pattern in `src/router.py`.
5. Build the auth module early.
6. Define the DB connection and schema initialization strategy.
7. Start with local storage, then add S3 support through abstraction.
8. Add error pages, logging, health route, and deployment files early in the project.

## 5. API Management Strategy

### 5.1 API Structure

- Keep all API routes under `src/api/`.
- Versioning is mandatory: `/api/v1`, `/api/v2`.
- `src/api/router.py` should only include sub-routes.
- Use separate API files for major features:
  - `src/api/auth.py`
  - `src/api/file_process.py`
  - `src/api/reports.py`
  - `src/api/users.py`

### 5.2 API Rules

- Use Pydantic schemas for request validation.
- Keep response shapes consistent:

```json
{
  "status": true,
  "message": "Processed successfully",
  "data": {},
  "error": null
}
```

- Do not write direct DB queries inside API route files; call helper, service, or repository functions.
- For auth-required routes, use a central dependency or token validation function.
- Keep public APIs and internal admin APIs separate.
- Swagger can remain available for development, but do not expose sensitive endpoints carelessly in production.

### 5.3 API Security

- Choose one clear standard based on the project: API key, bearer token, or signed session.
- Add rate limiting in production.
- Enforce request size limits.
- Validate extension, MIME type, and file size on upload endpoints.
- Never expose internal IDs, stack traces, or raw DB errors in sensitive responses.

## 6. Frontend and Backend Management

### 6.1 Hybrid App Pattern

For a hybrid app like the current project:

- Keep HTML pages in `templates/`.
- Keep CSS/JS in `static/`.
- Keep page routes inside feature modules, for example `src/division/routes.py`.
- Keep async actions, uploads, polling, and JS interactions inside `static/js/`.
- Use `templates/base.html` for shared layout.
- Keep feature-specific template folders:
  - `templates/auth/`
  - `templates/admin/`
  - `templates/project/`

### 6.2 When to Separate Frontend

Use a separate frontend project only when:

- an SPA is needed
- mobile and web will consume the same API
- the team has dedicated frontend developers
- UI complexity is much higher

Otherwise, a server-rendered plus JS-enhanced approach like the current project is usually the best fit for small to medium internal applications.

### 6.3 Frontend Rules

- Minimize inline JS and CSS.
- Keep common styles in `static/style.css` or a feature-specific CSS file.
- Validate forms in both frontend and backend.
- Define UI for unauthorized states, empty states, loading states, and error states.
- Keep admin UI and public UI separate both visually and in access control.

## 7. Backend Module Design

Follow this internal pattern for each new module:

- `routes.py`: HTTP endpoints
- `schema.py`: request/response models
- `service.py` or `renderer.py`: business logic
- `repository.py` or `db.py`: DB access
- `utils.py`: helper functions

If the module is small, it can stay within 2-3 files, but split it as it grows.

## 8. Auth System Standard

The current project already provides a strong base pattern for future auth systems.

### 8.1 Minimum Auth Flow

1. Register
2. Email verification
3. Login
4. Session issue
5. Role-based access
6. Logout

### 8.2 Recommended Auth Rules

- Never use weak or ad hoc password hashing; always use a proper secure utility.
- Always normalize emails to lowercase.
- Use a generic login error such as `Invalid email or password`.
- Do not allow unverified users to access authenticated areas.
- Use `HttpOnly` cookies, `SameSite=Lax` or stricter, and `Secure=True` in production.
- Keep session expiry fixed and explicit.
- Revoke tokens or invalidate sessions on logout.
- Keep admin and normal user roles separate.

### 8.3 Recommended Future Additions

- Password reset flow
- Login attempt throttling
- MFA for admin accounts
- Audit log for auth actions
- Session list / force logout

## 9. Security Baseline

Every project should start with this security checklist:

- Do not commit `.env` to git.
- Keep `.env.example`.
- Use a strong secret key.
- Make HTTPS mandatory in production.
- Set `SECURE_COOKIE=true` in production.
- Avoid wildcard CORS.
- Validate file uploads.
- Maintain input sanitization and output escaping.
- Do not show raw tracebacks in the browser.
- Do not log passwords, tokens, or API keys.
- Give the DB user only the minimum required permissions.
- Protect admin routes with stronger auth or IP restriction where possible.
- Update dependencies regularly.
- Test backup and restore processes.

## 10. Config and Secret Management

Use a central config style like `src/config.py` in every project.

### Rules

- Do not spread direct `os.getenv()` calls across the whole codebase; keep them mainly inside `src/config.py`.
- Derive all paths from `BASE_DIR`.
- Control local vs production behavior using env settings.
- Secrets include:
  - app secret key
  - DB credentials
  - SMTP credentials
  - AWS keys
  - external API keys

### Recommended Files

- `.env`
- `.env.example`
- `src/config.py`
- `logger.py`

## 11. Database Management

This project uses MySQL, and the same pattern can work well for future projects.

### Rules

- Keep the DB connection helper centralized.
- Schema init functions can run at startup, but migrations are better for production-grade systems.
- Split CRUD functions feature-wise.
- Separate public IDs and internal IDs where useful.
- Almost every table should include `created_at` and `updated_at`.
- Keep foreign key rules explicit.
- Use JSON fields only where they are truly helpful.

### Recommended Next Step

As the project grows, introduce Alembic or an equivalent migration tool.

## 12. Storage Strategy

Follow the current abstraction-based storage pattern:

- Local storage for development
- S3/object storage for production or scalable deployments
- Do not hardcode file paths directly across the codebase
- Handle backend switching through `src/storage/` abstraction

### File Upload Rules

- Allowed extension whitelist
- Max file size limit
- Virus/malware scanning if uploads are public-facing
- Sanitize original filenames
- Use unique generated file IDs

## 13. Logging and Error Handling

- Keep a central logger in `logger.py`.
- Use request logging middleware.
- Keep error pages in `templates/errors/`.
- Add a request ID in production logs.
- Keep structured logs for background tasks, upload processing, and external API calls.
- Keep user-facing errors polite and generic; detailed information should stay in logs only.

## 14. Web Server and Linux Deployment Strategy

Recommended Ubuntu server stack:

- App: FastAPI
- App server: `uvicorn` or `gunicorn` with uvicorn worker
- Reverse proxy: Nginx
- Process manager: `systemd`
- SSL: Certbot

Apache can also be used, but for FastAPI/Jinja apps, Nginx plus systemd is usually simpler and cleaner.

### 14.1 Recommended Server Layout

```text
/var/www/project-name/
|-- app.py
|-- src/
|-- templates/
|-- static/
|-- requirements.txt
|-- .env
|-- .venv/
|-- data/
`-- storage/
```

### 14.2 Systemd Service Example

```ini
[Unit]
Description=Project Name FastAPI App
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/var/www/project-name
EnvironmentFile=/var/www/project-name/.env
ExecStart=/var/www/project-name/.venv/bin/uvicorn app:app --host 127.0.0.1 --port 8000
Restart=always

[Install]
WantedBy=multi-user.target
```

### 14.3 Nginx Example

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    client_max_body_size 100M;

    location /static/ {
        alias /var/www/project-name/static/;
        expires 7d;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 14.4 Apache Option

- If company infrastructure is already Apache-based, Apache can be used as the reverse proxy.
- Still, Nginx should remain the default recommendation.

## 15. Easy Deployment Strategy

Deployment should be repeatable and not depend on manual guesswork.

### Minimum Deployment Flow

1. Pull code
2. Update virtualenv
3. Install dependencies
4. Verify `.env`
5. Restart service
6. Run health check
7. Check error logs

### Recommended Commands

```bash
cd /var/www/project-name
git pull origin main
source .venv/bin/activate
pip install -r requirements.txt
sudo systemctl restart project-name
sudo systemctl status project-name
```

### Better Deployment Practice

- Keep a `deploy.sh` script
- Add a health endpoint
- Keep a rollback plan
- Take a pre-deploy backup
- Make DB migration steps explicit when schema changes are involved

## 16. Environment Strategy

At minimum, define these environments:

- local
- staging
- production

### Environment Rules

- Local: developer machine
- Staging: production-like testing
- Production: live users

Never:

- use the production DB directly for local testing
- reuse the same secrets across all environments

## 17. Recommended New Project Checklist

Use this checklist when starting a new project:

- Root structure created
- `app.py` bootstrap ready
- `src/config.py` complete
- `src/router.py` ready
- auth module ready
- admin module if needed
- versioned API router ready
- templates/static base ready
- logger ready
- DB init ready
- `.env.example` ready
- error pages ready
- health endpoint ready
- deployment service file ready
- Nginx config draft ready
- backup and restore plan ready

## 18. Team Rules for Developers

- Do not add new features into random files; place them inside the relevant module.
- Do not overload route files with business logic.
- Do not hardcode secrets in source code.
- Think about auth and security impact whenever a new API endpoint is added.
- Review uploads, admin features, and auth changes with extra care.
- Validate production changes in staging first.

## 19. Best Default Architecture for Future Projects

If a developer asks, "What standard should we use for a new project?" the short answer should be:

- FastAPI app with `app.py` bootstrap
- `src/` module-based architecture
- `src/config.py` for env/config
- `src/router.py` for route aggregation
- `templates/` + `static/` for server-rendered UI
- `src/api/` for versioned APIs
- `src/auth/` for full auth lifecycle
- `src/storage/` for DB and file abstraction
- MySQL for core relational data
- Nginx + systemd on Ubuntu
- HTTPS, secure cookies, no wildcard CORS, no secret leakage

## 20. Final Recommendation

This project structure is very practical for small to mid-size business applications. It already supports clarity, maintainability, auth separation, template-based UI, API integration, storage abstraction, and deployment simplicity. Reuse the same base structure for future projects, and as the project grows, add module-level `service.py`, migrations, rate limiting, MFA, CI/CD, and monitoring in a gradual and controlled way.
