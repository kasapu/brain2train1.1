# Security Guidelines for brain2train1.1

This document outlines the security principles, controls, and best practices we will follow to ensure that **brain2train1.1** is designed, implemented, and operated with a defense-in-depth mindset. Adherence to these guidelines is mandatory for all team members and will be verified during code reviews, architecture reviews, and QA testing.

---

## 1. Security by Design

- Integrate security considerations from the earliest design discussions through deployment and maintenance.
- Perform threat modeling for each major feature (e.g., user authentication, progress tracking, adaptive difficulty engine) before writing code.
- Define clear security requirements alongside functional requirements in every feature ticket or user story.

## 2. Authentication & Access Control

### 2.1 Strong Authentication

- Require email address verification and enforce complex passwords (minimum 12 characters, mixed-case, numbers, symbols).  
- Hash passwords with a slow, memory-hard algorithm (e.g., Argon2 or bcrypt) using a unique salt per user.
- Implement account lockout or progressive delay after 5–10 failed login attempts to mitigate brute-force attacks.
- Provide optional Multi-Factor Authentication (MFA) via TOTP (e.g., Google Authenticator) for sensitive roles.

### 2.2 Session Management & JWT Security

- Use stateless JWTs for API endpoints, signed with a secure algorithm (HS256 or RS256).  
- Reject tokens that omit the “alg” header or specify `none`.  
- Validate `exp`, `iat`, and a custom `nonce` or session identifier on every request.  
- Store tokens in an `HttpOnly`, `Secure`, `SameSite=Strict` cookie to prevent XSS and CSRF.
- Implement both idle timeout (e.g., 15 minutes) and absolute logout (e.g., 8 hours).

### 2.3 Role-Based Access Control (RBAC)

- Define roles (`guest`, `user`, `admin`) and map each API endpoint or UI view to a minimum required role.
- Enforce server-side authorization checks in middleware for every protected route.
- Never rely solely on front-end role checks; always re-validate on the backend.

---

## 3. Input Handling & Processing

### 3.1 Validation & Sanitization

- Treat all input (query parameters, JSON bodies, file uploads) as untrusted.  
- Apply a whitelist-based validation (e.g., Joi schemas in Node.js) for expected types, lengths, formats.
- Encode all dynamic data in HTML contexts (e.g., use `react-dom`’s default escaping) to prevent XSS.

### 3.2 Injection Prevention

- Use parameterized queries or an ORM (e.g., Sequelize/TypeORM) to access the database.  
- Do not construct SQL/NoSQL queries with string concatenation.  
- Sanitize any OS or shell commands if used (e.g., exercise export script) or avoid shell invocation altogether.

### 3.3 Secure File Uploads

- Restrict file types (e.g., only accept `.svg` or `.png` for user avatars) and enforce a maximum size (e.g., 2 MB).  
- Store uploads outside the webroot or in a managed object store (e.g., AWS S3) with ACLs that deny public write access.  
- Remove or neutralize metadata from user-supplied images.  
- Scan uploads for malware using a virus-scan integration (optional in v1.0).

---

## 4. Data Protection & Privacy

### 4.1 Encryption In Transit and At Rest

- Enforce HTTPS only (HSTS header with `max-age` ≥1 year, `includeSubDomains`, `preload`).  
- Use TLS 1.2+ with strong cipher suites and disable weak protocols (SSLv3, TLS 1.0/1.1).
- Enable Transparent Data Encryption (TDE) or file‐level encryption on the database server.

### 4.2 Sensitive Data Handling

- Avoid storing full PII beyond what is essential for the service.  
- Encrypt PII fields (e.g., email) at the application layer if regulatory requirements demand it.  
- Mask or redact sensitive data in logs and error messages.  
- Use a secrets manager (AWS Secrets Manager, HashiCorp Vault) for API keys, DB credentials, and encryption keys—never commit them to source control.

---

## 5. API & Service Security

- Version all public APIs (e.g., `/api/v1/users`, `/api/v2/exercises`) and deprecate old versions responsibly.
- Enforce rate limiting (e.g., 100 req/minute per IP) and burst controls on login, registration, and exercise-submission endpoints.
- Implement CORS policy that only allows our official UI domain (e.g., `https://app.brain2train.com`) to call APIs.
- Return minimal data in responses; do not leak internal IDs, stack traces, or debug information.
- Use appropriate HTTP verbs: GET for read, POST for create, PUT/PATCH for update, DELETE for removal.

---

## 6. Web Application Security Hygiene

- Set the following security headers:  
  - `Content-Security-Policy` (restrict sources for scripts, styles, fonts, frames).  
  - `X-Content-Type-Options: nosniff`  
  - `X-Frame-Options: DENY`  
  - `Referrer-Policy: no-referrer-when-downgrade`.
- Protect state-changing requests with CSRF tokens (Synchronizer Token Pattern) in forms and AJAX calls.
- Store session or token data in `HttpOnly` cookies, never in `localStorage` or `sessionStorage`.

---

## 7. Infrastructure & Configuration Management

- Harden servers by disabling unnecessary services and removing default credentials.  
- Expose only required ports (e.g., 443 for HTTPS, 22 for SSH restricted by IP allow-list).  
- Automate OS and package updates; apply critical patches within 48 hours of release.
- Use Infrastructure as Code (e.g., Terraform/CloudFormation) to maintain reproducible, auditable configurations.
- Ensure file system permissions are restrictive (e.g., application user cannot write to source directories).

---

## 8. Dependency Management

- Maintain a lockfile (`package-lock.json` / `yarn.lock`) to pin dependency versions.  
- Perform automated vulnerability scans (e.g., GitHub Dependabot, Snyk) on all direct and transitive dependencies.
- Upgrade or replace libraries with known Critical/High CVEs within one week of publication.
- Limit dependencies to only those needed for core functionality.

---

## 9. Logging, Monitoring & Incident Response

- Log at appropriate levels:  
  - **Info**: user logins, sign-ups, exercise completions.  
  - **Warn**: repeated failed login attempts, rate limit hits.  
  - **Error**: unhandled exceptions, DB connection failures.
- Exclude sensitive data (passwords, tokens) from logs.
- Aggregate logs in a central service (e.g., ELK, Datadog) and set up alerting for suspicious patterns.
- Define an incident response plan: roles, communication channels, and a post-mortem process.

---

## 10. Continuous Security Practices

- Include security checks in CI/CD pipelines: linting, SAST (Static Analysis), SCA (Software Composition Analysis), unit tests, integration tests.
- Perform periodic penetration tests, preferably by an external party, at least annually or after major releases.
- Conduct regular security training and tabletop exercises with the development and operations teams.

---

By following these guidelines, **brain2train1.1** will establish a strong security posture from day one. Every feature, every line of code, and every deployment must be measured against these principles to protect our users’ data, privacy, and trust.