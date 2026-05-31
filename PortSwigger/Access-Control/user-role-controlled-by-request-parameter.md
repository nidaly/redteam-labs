PortSwigger Lab Writeup - User Role Controlled by Request Parameter
===================================================================

Lab Information
---------------

*   **Lab Name:** User role controlled by request parameter
    
*   **Category:** Access Control Vulnerabilities
    
*   **Platform:** PortSwigger Web Security Academy
    

Overview
========

This lab demonstrates a common access control vulnerability where a user's role is determined entirely by a client-controlled request parameter.

The application trusts information supplied by the user rather than validating authorization on the server side. By modifying a request parameter, an attacker can elevate privileges and gain administrative access.

Objective
=========

Access the administrator panel and delete the user **carlos**.

Vulnerability Description
=========================

The application stores the user's role in a cookie that is sent with every request.

Example:

`   Cookie: Admin=false   `

The server uses this cookie to determine whether the user has administrative privileges.

Because the cookie value can be modified by the client, an attacker can simply change:

 `   Admin=false   `

to:

 `   Admin=true   `

and gain administrator access.

This is an example of:

*   Broken Access Control
    
*   Client-Side Authorization
    
*   Privilege Escalation
    

Reconnaissance
==============

After accessing the application, no administrator functionality is visible.

While browsing the site, inspect the requests using Burp Suite.

Navigate to:

 `   Proxy → HTTP History   `

Observe the requests and response headers.

A cookie similar to the following is present:

 `   Cookie: Admin=false   `

This immediately suggests that the application may be relying on a client-controlled value for authorization decisions.

Identifying the Vulnerability
=============================

The presence of the cookie:

 `   Admin=false   `

is suspicious because:

1.  The role information is exposed to the client.
    
2.  The value is not encrypted.
    
3.  The value is not signed.
    
4.  Authorization appears to depend on user-supplied data.
    

A common test is to modify the value and observe application behavior.

Exploitation
============

Step 1 - Intercept the Request
------------------------------

Using Burp Suite:

 `   Proxy → Intercept   `

Capture any request sent to the application.

Example:

 `   GET / HTTP/1.1  Host: lab-id.web-security-academy.net  Cookie: Admin=false   `

Step 2 - Modify the Cookie
--------------------------

Change:

 `   Admin=false   `

to:

 `   Admin=true   `

Modified request:

 `   GET / HTTP/1.1  Host: lab-id.web-security-academy.net  Cookie: Admin=true   `

Forward the request.

Step 3 - Verify Privilege Escalation
------------------------------------

Refresh the application.

A new link should appear:

 `   Admin panel   `

This confirms that administrative privileges are being granted based solely on the cookie value.

Step 4 - Access the Admin Panel
-------------------------------

Browse to:

 `   /admin   `

or click the newly visible administrator panel link.

The administrator interface becomes accessible.

Step 5 - Delete User Carlos
---------------------------

Locate the user management section.

Find the user:

 `   carlos   `

Select the delete function.

The request typically resembles:

 `   GET /admin/delete?username=carlos HTTP/1.1  Host: lab-id.web-security-academy.net  Cookie: Admin=true   `

Send the request.

The user is successfully deleted.

Lab Solved
==========

The lab is completed once the user **carlos** has been removed.

Root Cause Analysis
===================

The application trusts a client-controlled parameter to make authorization decisions.

Instead of validating permissions on the server side, it relies on:

 `   Admin=true   `

supplied directly by the user.

Because users fully control cookies stored in their browsers, any authorization logic based on cookie values can be bypassed.

Security Impact
===============

An attacker could:

*   Gain administrator privileges
    
*   Access sensitive functionality
    
*   Delete users
    
*   Modify data
    
*   View confidential information
    
*   Fully compromise the application
    

Depending on the application's purpose, this could lead to complete system takeover.

OWASP Mapping
=============

### OWASP Top 10 2021

**A01:2021 – Broken Access Control**

The application fails to enforce authorization checks on the server side and instead trusts client-supplied data.

CWE Mapping
===========

### CWE-602

**Client-Side Enforcement of Server-Side Security**

The server relies on user-controlled information for security decisions.

### CWE-285

**Improper Authorization**

The application does not correctly verify user permissions before granting access.

Attack Flow
===========

 ` User Accesses Website → Receives Cookie  (Admin=false) → Attacker Modifies Cookie  (Admin=true) → Server Trusts Cookie → Administrator Access Granted → Delete Carlos → Lab Solved   `

Remediation
===========

1\. Store Roles Server-Side
---------------------------

Never trust user-supplied authorization data.

Instead:

 `   Session ID → Lookup User Role → Apply Authorization   `

The server should maintain role information in a secure backend database.

2\. Implement Server-Side Authorization Checks
----------------------------------------------

Every privileged function should verify:

 `   Is User Authorized?   `

before processing the request.

3\. Use Signed Session Tokens
-----------------------------

If role information must be stored in tokens:

*   Digitally sign JWTs
    
*   Validate signatures
    
*   Reject modified tokens
    

4\. Follow Least Privilege
--------------------------

Users should only receive permissions required for their role.

Key Learning Points
===================

*   Never trust client-side authorization data.
    
*   Cookies can be modified by users.
    
*   Authorization decisions must occur on the server.
    
*   Hidden parameters are not security controls.
    
*   Broken Access Control is one of the most common web vulnerabilities.
    
*   Simple cookie manipulation can sometimes lead to complete application compromise.
    

Conclusion
==========

This lab demonstrates a classic privilege escalation vulnerability caused by trusting a client controlled request parameter. By modifying the cookie value from:

 `   Admin=false   `

to:

 `   Admin=true   `

an attacker can obtain administrative privileges and perform sensitive actions such as deleting users.

The vulnerability highlights the importance of enforcing access control exclusively on the server side and never relying on client supplied data for authorization decisions.
