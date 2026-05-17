# PortSwigger - Unprotected Admin Functionality

## Vulnerability
Broken access control allows unauthorized users to access admin functionality.

---

## Attack Path
- Accessed target application
- Discovered hidden admin panel
- Access control was not enforced

---

## Impact
- Unauthorized administrative access
- Possible account takeover or system manipulation

---

## Mitigation
- Enforce server-side access control
- Restrict admin endpoints
- Validate user roles properly

---

## Key Learning
Never trust hidden URLs alone for security.
