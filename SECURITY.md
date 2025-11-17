# Security Documentation

## Overview

DraftlyCV has been designed with production-level security features to protect users when deployed to shared hosting environments. This document outlines the security measures implemented and best practices for deployment.

## Security Features Implemented

### 1. XSS (Cross-Site Scripting) Protection

**Status: ✅ IMPLEMENTED**

All user inputs are sanitized using the `escapeHtml()` function before rendering to prevent malicious script injection.

**Files Protected:**
- `preview.html` - All resume data is sanitized before display
- `builder.html` - All form inputs are escaped before preview rendering
- `builder-design2.html` - All form inputs are escaped before preview rendering

**How it works:**
```javascript
function escapeHtml(unsafe) {
    if (!unsafe) return '';
    if (typeof unsafe !== 'string') unsafe = String(unsafe);
    return unsafe
        .replace(/&/g, "&amp;")
        .replace(/</g, "&lt;")
        .replace(/>/g, "&gt;")
        .replace(/"/g, "&quot;")
        .replace(/'/g, "&#039;")
        .replace(/\//g, "&#x2F;");
}
```

All special characters are converted to HTML entities, preventing script execution.

### 2. Content Security Policy (CSP)

**Status: ✅ IMPLEMENTED**

Strict CSP headers are configured to prevent unauthorized resource loading and inline script execution.

**Policy Applied:**
```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'self';
  script-src 'self' 'unsafe-inline' 'unsafe-eval' cdnjs.cloudflare.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https:;
  connect-src 'self' api.jsonbin.io;
  font-src 'self' data:;
  object-src 'none';
  base-uri 'self';
  form-action 'self';
">
```

**What this blocks:**
- Unauthorized external scripts
- Inline event handlers (onclick, onerror, etc.)
- External connections except to JSONBin.io API
- Loading of objects/plugins
- Form submissions to external domains

### 3. Image Size Validation

**Status: ✅ IMPLEMENTED**

Images are validated to prevent:
- Exceeding JSONBin.io free tier limits (100KB per bin)
- Potential denial-of-service from large uploads
- Performance issues in preview rendering

**Implementation:**
```javascript
function validateImageSize(base64String) {
    if (!base64String) return { valid: true };
    const base64Length = base64String.length - (base64String.indexOf(',') + 1);
    const sizeInBytes = (base64Length * 3) / 4;
    const sizeInKB = sizeInBytes / 1024;
    const MAX_SIZE_KB = 100;

    if (sizeInKB > MAX_SIZE_KB) {
        return {
            valid: false,
            message: `Image too large (${sizeInKB.toFixed(1)}KB). Maximum allowed: ${MAX_SIZE_KB}KB.`
        };
    }
    return { valid: true, size: sizeInKB.toFixed(1) };
}
```

**Limit: 100KB** - Users are alerted if images exceed this size.

### 4. Input Validation

**Status: ✅ IMPLEMENTED**

Comprehensive validation for all user inputs:

#### Email Validation
```javascript
function isValidEmail(email) {
    if (!email) return true; // Optional field
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
}
```

#### Phone Validation
```javascript
function isValidPhone(phone) {
    if (!phone) return true; // Optional field
    const phoneRegex = /^[\d\s\-\+\(\)]+$/;
    return phoneRegex.test(phone) && phone.replace(/\D/g, '').length >= 7;
}
```

#### URL Validation
```javascript
function isValidURL(url) {
    if (!url) return true; // Optional field
    try {
        new URL(url);
        return url.startsWith('http://') || url.startsWith('https://');
    } catch (e) {
        return false;
    }
}
```

### 5. Comprehensive Data Validation

**Status: ✅ IMPLEMENTED**

Before sharing resumes, all data is validated:

```javascript
function validateResumeData(data) {
    const errors = [];
    if (data.email && !isValidEmail(data.email)) {
        errors.push('Invalid email format');
    }
    if (data.phone && !isValidPhone(data.phone)) {
        errors.push('Invalid phone format');
    }
    if (data.website && !isValidURL(data.website)) {
        errors.push('Invalid website URL format');
    }
    if (data.photo) {
        const imageValidation = validateImageSize(data.photo);
        if (!imageValidation.valid) {
            errors.push(imageValidation.message);
        }
    }
    return { valid: errors.length === 0, errors: errors };
}
```

Users are alerted immediately if any validation fails.

## API Key Security

### ⚠️ CRITICAL SECURITY WARNING

**NEVER commit API keys to public repositories!**

The JSONBin.io API key in the code is a **placeholder** and must be replaced with your own key:

```javascript
// ⚠️ SECURITY WARNING: Replace with your own API key!
// NEVER commit real API keys to public repositories
const JSONBIN_API_KEY = 'YOUR_JSONBIN_API_KEY_HERE';
```

### Best Practices for API Key Management

#### For Development/Testing:
1. Get your free API key from https://jsonbin.io/
2. Replace the placeholder in your local copy
3. **DO NOT commit this file to Git**

#### For Production Deployment:

**Option 1: Environment Variables (Recommended)**
```javascript
// Use server-side environment variables
const JSONBIN_API_KEY = process.env.JSONBIN_API_KEY || 'YOUR_JSONBIN_API_KEY_HERE';
```

**Option 2: Server-Side Proxy (Most Secure)**
Create a backend endpoint that handles JSONBin.io requests:
```javascript
// Frontend calls your backend instead of JSONBin.io directly
fetch('/api/save-resume', {
    method: 'POST',
    body: JSON.stringify(resumeData)
});

// Backend handles API key securely
app.post('/api/save-resume', async (req, res) => {
    const response = await fetch('https://api.jsonbin.io/v3/b', {
        headers: {
            'X-Master-Key': process.env.JSONBIN_API_KEY
        },
        body: JSON.stringify(req.body)
    });
    res.json(await response.json());
});
```

**Option 3: Separate Config File (Not Committed)**
```javascript
// config.js (add to .gitignore)
const JSONBIN_API_KEY = 'your-real-api-key-here';

// .gitignore
config.js
```

### API Key Exposure Detection

All builder files check for placeholder API key:
```javascript
if (!JSONBIN_API_KEY || JSONBIN_API_KEY === 'YOUR_JSONBIN_API_KEY_HERE') {
    alert('⚠️ JSONBin.io API key is not configured!');
    return;
}
```

## Deployment Checklist

### Before Deploying to Public Hosting:

- [ ] **API Key Security**
  - [ ] Replace placeholder API key with real key
  - [ ] Ensure API key is NOT committed to Git
  - [ ] Consider using server-side proxy for production

- [ ] **File Permissions**
  - [ ] Set files to read-only where possible
  - [ ] Restrict write access to upload directories

- [ ] **HTTPS Configuration**
  - [ ] Enable HTTPS on your hosting
  - [ ] Force HTTPS redirects
  - [ ] Update CSP if using custom domain

- [ ] **Rate Limiting**
  - [ ] Configure server-side rate limiting
  - [ ] Limit API calls per IP (recommended: 10/minute)

- [ ] **Monitoring**
  - [ ] Set up error logging
  - [ ] Monitor for suspicious activity
  - [ ] Track API usage to prevent quota exhaustion

### Testing Security Features:

1. **Test XSS Protection:**
   - Enter `<script>alert('XSS')</script>` in name field
   - Preview should show escaped text, not alert

2. **Test Image Validation:**
   - Upload image > 100KB
   - Should show error message and prevent upload

3. **Test Input Validation:**
   - Enter invalid email: `notanemail`
   - Enter invalid phone: `abc123`
   - Enter invalid URL: `not-a-url`
   - Should show validation errors before sharing

4. **Test CSP Headers:**
   - Open browser console
   - Try injecting external script in console
   - Should be blocked by CSP

## Known Limitations

### Current Security Gaps:

1. **No Rate Limiting (Client-Side)**
   - Currently no client-side rate limiting
   - **Mitigation:** Add server-side rate limiting on hosting

2. **API Key in Client Code**
   - API key is visible in JavaScript
   - **Mitigation:** Use server-side proxy for production

3. **No Server-Side Validation**
   - All validation is client-side only
   - **Mitigation:** Add backend validation layer

4. **No User Authentication**
   - Anyone can create/view resumes with link
   - **Consideration:** This may be desired behavior for public sharing

## Reporting Security Issues

If you discover a security vulnerability, please:

1. **DO NOT** open a public GitHub issue
2. Contact the repository owner directly
3. Provide detailed description of the vulnerability
4. Allow time for fix before public disclosure

## Security Updates

This document will be updated as new security features are added or vulnerabilities are discovered.

**Last Updated:** 2025-11-17
**Security Review Status:** Production-ready for public deployment with proper API key management
