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

This guideline defines our security approach, authentication patterns, and compliance requirements. Update the YAML frontmatter as you make security decisions.

## Security Philosophy

**Approach**: Security by default, defense in depth

- **Principle of Least Privilege**: Grant minimum necessary access
- **Secure by Default**: Fail secure, not fail open
- **Defense in Depth**: Multiple layers of security
- **Zero Trust**: Verify every request

## Configuration Process

**When making security decisions:**

1. Run `/adr "authentication strategy"` to document auth/authz decisions
2. Update YAML frontmatter with decisions (`authentication_method`, `authorization_model`, `sensitive_data_handling`, etc.)
3. Add examples section below with links to security implementations

**Agents read the frontmatter** to understand your security posture and enforce patterns during implementation.

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

## OWASP Top 10 Awareness

Agents automatically check for OWASP Top 10 vulnerabilities during security audits:
- Injection, Broken Authentication, Sensitive Data Exposure, XSS, CSRF, and others
- See `/security-audit` command for proactive vulnerability detection

## General Security Knowledge

Claude has extensive knowledge of OWASP Top 10, authentication patterns (JWT, OAuth, SAML), authorization models (RBAC, ABAC, PBAC), encryption standards, secure coding practices, and threat modeling. Ask questions like "How should I secure [X]?" for guidance based on security standards.
