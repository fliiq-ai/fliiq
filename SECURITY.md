# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability in Fliiq, please report it responsibly.

**Do NOT open a public GitHub issue for security vulnerabilities.**

Instead, email: **andy@fliiq.ai**

Include:
- Description of the vulnerability
- Steps to reproduce
- Impact assessment
- Your Fliiq version (`fliiq --version`)

## Response Timeline

- **Acknowledgment**: Within 48 hours
- **Initial assessment**: Within 1 week
- **Fix or mitigation**: Depends on severity, but we prioritize security issues above all else

## Scope

In scope:
- Prompt injection leading to credential exfiltration
- Unauthorized access to files outside the working directory
- Daemon API authentication bypass
- Telegram/email/SMS channel security issues
- Command injection via skills

Out of scope:
- System prompt extraction (soft defense only — known limitation)
- Denial of service against the local daemon
- Issues requiring physical access to the machine
- Vulnerabilities in third-party dependencies (report upstream)

## Security Features

See the Security section in the [README](README.md) for details on Fliiq's built-in protections.
