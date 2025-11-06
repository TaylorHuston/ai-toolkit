---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Optional"
target_audience: ["AI Assistants", "Security Auditors", "Development Team"]
description: "Security approach, authentication patterns, and compliance requirements"

# === Security Configuration (Machine-readable for AI agents) ===
authentication_method: "TBD"   # JWT, session, OAuth2, etc.
authorization_model: "TBD"     # RBAC, ABAC, simple roles
sensitive_data_handling: "TBD" # encryption at rest, in transit
security_headers: "TBD"        # CORS, CSP, HSTS, etc.
input_validation: "TBD"        # library, custom, none
secrets_management: "TBD"      # env vars, vault, etc.
---

# Security Guidelines

**Referenced by Commands:** `/security-audit`

## Quick Reference

This guideline defines our security approach, authentication patterns, and compliance requirements. Update as you make security decisions.

## Security Philosophy

**Approach**: Security by default, defense in depth

- **Principle of Least Privilege**: Grant minimum necessary access
- **Secure by Default**: Fail secure, not fail open
- **Defense in Depth**: Multiple layers of security
- **Zero Trust**: Verify every request

## Authentication

### Strategy

**Method**: TBD → Run `/adr "authentication strategy"` to decide

- JWT (stateless, scalable)
- Session-based (server-side state)
- OAuth 2.0 (third-party authentication)
- Custom (define your approach)

### Implementation

- **Library**: TBD
- **Token Storage**: TBD (httpOnly cookies, localStorage, etc.)
- **Token Expiry**: TBD
- **Refresh Strategy**: TBD

## Authorization

### Model

**Approach**: TBD → Run `/adr "authorization model"` to decide

- **RBAC** (Role-Based Access Control): Users have roles with permissions
- **ABAC** (Attribute-Based Access Control): Policy-based decisions
- **Simple Roles**: Basic admin/user distinction

### Implementation

- **Permission Check Location**: TBD (middleware, route guards, service layer)
- **Role Definition**: TBD
- **Permission Granularity**: TBD

## Data Protection

### Sensitive Data

**What qualifies as sensitive**:
- TBD - Define for your domain (PII, financial, health, etc.)

**Handling**:
- **Encryption at Rest**: TBD
- **Encryption in Transit**: TBD (HTTPS, TLS version)
- **Data Masking**: TBD (logs, responses)
- **Retention Policy**: TBD

### Secrets Management

- **Method**: TBD (environment variables, vault, KMS)
- **Never Commit**: Passwords, API keys, tokens, certificates
- **Rotation Policy**: TBD

## Input Validation

### Strategy

- **Validation Library**: TBD (Zod, Joi, class-validator, etc.)
- **Where to Validate**: TBD (API boundary, service layer, both)
- **Sanitization**: TBD (XSS prevention, SQL injection, etc.)

### Rules

- **Allow-list over Deny-list**: Define what's allowed, reject everything else
- **Validate Type & Format**: Don't trust client input
- **Limit Size**: Prevent DoS via large payloads

## Security Headers

```
TBD - Define security headers for your application

Recommended:
- Strict-Transport-Security (HSTS)
- Content-Security-Policy (CSP)
- X-Frame-Options
- X-Content-Type-Options
- CORS configuration
```

## Common Vulnerabilities

### OWASP Top 10 Awareness

- **Injection**: TBD - How we prevent SQL/NoSQL injection
- **Broken Authentication**: TBD - Our auth security measures
- **Sensitive Data Exposure**: TBD - Data protection strategy
- **XSS**: TBD - Output encoding, CSP
- **CSRF**: TBD - Token-based protection
- **And others...**: Reference OWASP for full list

## Compliance

**Requirements**: TBD

- GDPR (if handling EU data)
- HIPAA (if handling health data)
- PCI-DSS (if handling payment data)
- SOC 2 (if providing SaaS)
- Custom compliance needs

## Security Checklist

### Development

- [ ] Input validation on all user inputs
- [ ] Output encoding to prevent XSS
- [ ] Parameterized queries to prevent injection
- [ ] Authentication on protected resources
- [ ] Authorization checks enforced
- [ ] Sensitive data encrypted
- [ ] Security headers configured

### Deployment

- [ ] HTTPS enforced
- [ ] Secrets not in code/config files
- [ ] Security updates applied
- [ ] Error messages don't leak info
- [ ] Logging doesn't expose sensitive data

## Examples

### Authentication Implementation
- TBD - Add link to auth code when implemented

### Authorization Check
- TBD - Add link to authorization middleware

### Input Validation
- TBD - Add link to validation schema

## General Security Knowledge

For security best practices, Claude has extensive knowledge of:
- OWASP Top 10 vulnerabilities and mitigations
- Authentication patterns (JWT, OAuth, SAML)
- Authorization models (RBAC, ABAC, PBAC)
- Encryption standards and algorithms
- Secure coding practices
- Security testing and threat modeling

Ask questions like "How should I secure [X]?" and Claude will provide guidance based on security standards and your application context.
