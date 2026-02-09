# Security Policy

## Code Examples Disclaimer

**All code examples in this skill are for educational purposes only.**

### ⚠️ Before Production Use

1. **Security Review Required**
   - Have code reviewed by security experts
   - Perform penetration testing
   - Run static analysis tools (Bandit, SonarQube)

2. **Use Proven Solutions**
   - Prefer established libraries over custom implementations
   - Use dedicated tools (e.g., pg_basebackup instead of custom backup scripts)
   - Follow framework security guidelines (Django, Flask security best practices)

3. **Input Validation**
   - Validate ALL user inputs
   - Sanitize data before database queries
   - Use parameterized queries (NEVER string concatenation)

4. **Least Privilege Principle**
   - Run services with minimal required permissions
   - Use separate database users for different operations
   - Implement RBAC (Role-Based Access Control)

## Security Best Practices Applied

### ✅ What We Do

- **No `os.system()`** - Use `subprocess.run()` with argument lists
- **No `eval()` or `exec()`** - Never execute dynamic code
- **No `shell=True`** - Prevents shell injection
- **Path Validation** - Prevent path traversal attacks
- **Timeout Settings** - Prevent hanging processes
- **Error Handling** - Fail securely, no sensitive info in errors

### ❌ What We Avoid

- ❌ Command injection vulnerabilities (`os.system`, `shell=True`)
- ❌ SQL injection (always use ORM or parameterized queries)
- ❌ Path traversal (validate all file paths)
- ❌ Hardcoded credentials (use environment variables or vaults)
- ❌ Unvalidated redirects
- ❌ XXE (XML External Entity) attacks

## Reporting Security Issues

If you find a security vulnerability in the code examples, please report it to:
- GitHub Issues: https://github.com/CIRFMF/ksef-issues
- Or contact maintainers privately

**Do NOT** publicly disclose security vulnerabilities before they're addressed.

## Compliance

This skill documentation supports:
- OWASP Top 10 awareness
- Secure coding practices
- Polish VAT Act compliance
- GDPR/RODO data protection requirements

## Updates

This security policy is reviewed and updated regularly. Last update: 2026-02-09

---

**Remember:** Security is not a feature, it's a requirement. Always prioritize security in production systems.
