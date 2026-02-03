# Security Policy

## Security Overview

This document describes the security measures implemented in the VSCode Markdown Editor extension to protect user data and system resources.

## Security Measures

### 1. Restricted File System Access

**Issue**: Previous versions granted webview access to the entire file system (root `/` and all drive letters A-Z).

**Fix**: File system access is now restricted to:
- Extension resources (CSS/JS files)
- Workspace folders
- The markdown file's directory and immediate parent directories
- Only directories necessary for displaying images referenced in markdown files

**Impact**: Prevents malicious code from reading arbitrary files on the system while maintaining full functionality for legitimate use cases.

### 2. Custom CSS Sanitization

**Issue**: User-provided custom CSS was injected directly into the webview without validation.

**Fix**: All custom CSS is now sanitized to remove potentially dangerous patterns:
- `javascript:` and `vbscript:` protocols
- `data:text/html` URLs that could contain scripts
- `expression()` (IE-specific code execution)
- `behavior:` property (IE-specific)
- `-moz-binding:` (Firefox XBL bindings)
- Malicious `@import` statements

**Impact**: Prevents XSS attacks through CSS injection while allowing safe custom styling.

### 3. Debug Logging Protection

**Issue**: Console logging could expose sensitive data like file paths and document content.

**Fix**: Debug logging is now conditional and only enabled in development mode:
- Production builds will not log debug information
- Only errors critical for troubleshooting are logged in production

**Impact**: Reduces the risk of sensitive data exposure through browser console.

### 4. Sanitized Error Messages

**Issue**: Error messages displayed full file system paths, revealing system structure.

**Fix**: File paths in error messages are now sanitized to show only:
- The last two path components (parent directory + filename)
- A prefix `...` to indicate path truncation

**Impact**: Minimizes information disclosure while maintaining useful error messages.

## No Network Communication

✅ This extension does NOT:
- Make any network requests
- Send data to external servers
- Include telemetry or analytics
- Connect to remote services
- Exfiltrate user data

All operations are performed locally within VS Code.

## Dependency Security

All dependencies have been verified against the GitHub Advisory Database:
- No known vulnerabilities in npm dependencies
- Regular updates recommended to maintain security posture

## Security Best Practices

### For Users

1. **Custom CSS**: Only add custom CSS from trusted sources
2. **Workspace Trust**: Use VS Code's workspace trust feature for unknown projects
3. **Extensions**: Keep the extension updated to receive security fixes

### For Contributors

1. **Input Validation**: Always validate and sanitize user input
2. **Least Privilege**: Request only necessary permissions
3. **Code Review**: All changes must be reviewed for security implications
4. **Dependencies**: Check dependencies for vulnerabilities before adding/updating
5. **No Secrets**: Never commit API keys, tokens, or sensitive data

## Reporting Security Issues

If you discover a security vulnerability, please report it by:
1. **DO NOT** create a public GitHub issue
2. Contact the maintainers privately through GitHub Security Advisories
3. Provide detailed information about the vulnerability
4. Allow reasonable time for a fix before public disclosure

## Security Audit History

### 2026-02-03 - Comprehensive Security Review
- ✅ Fixed excessive file system access (HIGH severity)
- ✅ Implemented custom CSS sanitization (MEDIUM severity)
- ✅ Added debug logging protection (LOW severity)
- ✅ Sanitized error message paths (LOW severity)
- ✅ Verified no network calls or data exfiltration
- ✅ Confirmed no dependency vulnerabilities
- ✅ Verified no use of `eval()` or `Function()` constructor

## Security Features

### Content Security Policy
The webview uses VS Code's built-in CSP protections to prevent:
- Inline script execution (except explicitly allowed)
- Unauthorized resource loading
- XSS attacks through injected content

### Sandboxing
The webview runs in a sandboxed environment that:
- Isolates it from the main VS Code process
- Prevents direct access to Node.js APIs
- Requires explicit message passing for file operations

### File Upload Security
Image uploads are handled securely:
- Files are validated and encoded as base64
- Saved to user-configured directory (default: `assets/`)
- No arbitrary file execution
- Path traversal prevention through path normalization

## Compliance

This extension follows:
- VS Code Extension Security Best Practices
- OWASP Security Guidelines
- Principle of Least Privilege
- Defense in Depth

## License

This security policy is part of the project and follows the same MIT License.
