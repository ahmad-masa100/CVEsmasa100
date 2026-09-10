# Stored Cross-Site Scripting (XSS) in Human Resource Management System in PHP – `eventSubject` Parameter

---

## Credit

**Discovered by:** Ahmad-masa10

---

## Product

**Human Resource Management System in PHP**

## Vendor

**Code-Projects**

## Vendor Homepage

Human Resource Management System in PHP

## Affected Version

**Human Resource Management System in PHP**

> The exact version should be confirmed from the downloaded source package before submitting the report to a vulnerability database or requesting a CVE.

The Code-Projects project page identifies the application as a PHP-based Human Resource Management system with administrative functionality for managing employees, live events, and notices.

---

## Vulnerability Type

**Stored Cross-Site Scripting (Stored XSS)**

### CWE

**CWE-79 – Improper Neutralization of Input During Web Page Generation (Cross-Site Scripting)**

CWE-79 covers cases where user-controlled input is not properly neutralized before being incorporated into web page output.

---

## Severity

**Medium**

---

# Description

The Human Resource Management System in PHP is affected by a **Stored Cross-Site Scripting (XSS)** vulnerability in the live event functionality.

The vulnerability occurs when attacker-controlled input is submitted through the `eventSubject` parameter and stored by the application through the event storage functionality.

The malicious value is subsequently rendered in the live event history page:

```text
/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

Testing confirmed that the following XSS payload could be stored and subsequently executed when the affected live event history page was viewed:

```html
--><script>alert(123)</script>
```

The vulnerable rendering endpoint is:

```text
http://localhost/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

Because the malicious payload is stored server-side and later rendered in the application interface, this vulnerability is classified as a **Stored (Persistent) Cross-Site Scripting vulnerability**.

An attacker with access to the functionality that allows modification or creation of live events may inject malicious JavaScript that executes in the browser of users viewing the affected event history page.

---

# Vulnerable Files

## Event Storage Component

```text
/humanresourcemanagementsystem/src/store/EventStore.php
```

## Vulnerable Rendering Page

```text
/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

---

# Affected Endpoint

## Vulnerable Rendering Endpoint

```text
http://localhost/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

## HTTP Method

```text
GET
```

## Vulnerable Parameter

```text
eventSubject
```

---

# Proof of Concept

The following stored XSS payload was used during testing:

```html
--><script>alert(123)</script>
```

The payload was stored through the application's live event functionality and subsequently triggered when accessing the following page:

```text
http://localhost/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

The following HTTP request demonstrates access to the affected page:

```http
GET /humanresourcemanagementsystem/views/admin/liveEventHistory.php HTTP/1.1
Host: localhost
sec-ch-ua: "Not;A=Brand";v="8", "Chromium";v="150"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=REDACTED
Connection: keep-alive
```

### PoC 1

**Stored XSS payload rendered through the Live Event History page.**

```html
--><script>alert(123)</script>
```

---

# Steps to Reproduce

### Step 1 – Install the Application

Install the **Human Resource Management System in PHP** application locally.

### Step 2 – Authenticate

Log in to the application with an account that has access to the live event management functionality.

### Step 3 – Navigate to Live Event Management

Navigate to the live event creation functionality.

### Step 4 – Create or Modify a Live Event

Create or modify a live event.

### Step 5 – Inject the Payload

Insert the following payload into the `eventSubject` field:

```html
--><script>alert(123)</script>
```

### Step 6 – Submit the Event

Submit the event and allow the application to store the supplied value.

### Step 7 – Open the Affected Page

Navigate to:

```text
http://localhost/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

### Step 8 – Observe the Result

Observe that the stored payload is rendered by the application.

The JavaScript payload executes in the browser when the affected event history entry is displayed.

---

# Result

The malicious payload is stored by the application and subsequently rendered in:

```text
/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

When the affected live event entry is displayed, the browser interprets the attacker-controlled input as HTML and JavaScript.

The following payload executes:

```html
--><script>alert(123)</script>
```

This confirms that attacker-controlled data stored through the live event functionality is rendered without sufficient output encoding.

Because the payload remains stored server-side and executes when the affected content is viewed, the vulnerability is classified as a:

**Stored Cross-Site Scripting (Persistent XSS)** vulnerability.

---

# Root Cause

The vulnerability is caused by improper handling of attacker-controlled input submitted through the live event functionality.

Specifically:

1. The application accepts user-controlled input through the `eventSubject` parameter.

2. The attacker-controlled value is stored by the application.

3. The stored value is subsequently displayed in the live event history functionality.

4. The application fails to properly encode or neutralize dangerous HTML characters before rendering the stored value.

5. The browser interprets attacker-controlled HTML and JavaScript as active content.

The vulnerable data flow can be summarized as follows:

```text
Attacker-controlled input
          │
          ▼
eventSubject parameter
          │
          ▼
EventStore.php
          │
          ▼
Stored in application/database
          │
          ▼
liveEventHistory.php
          │
          ▼
Unescaped HTML output
          │
          ▼
Stored XSS execution
```

The vulnerability results from the combination of persistent storage of attacker-controlled input and improper output encoding when the data is rendered.

---

# Impact

Successful exploitation of this vulnerability may allow an attacker to:

* Execute arbitrary JavaScript in the browser of affected users.
* Modify application content displayed to victims.
* Perform actions within the application in the context of an authenticated victim.
* Target privileged users who access the affected live event history page.
* Conduct phishing attacks using malicious injected content.
* Perform unauthorized actions depending on the victim's privileges.
* Access information available to JavaScript within the application's origin.
* Potentially compromise authenticated sessions, depending on cookie security settings and application architecture.

Because the malicious payload is stored persistently, multiple users may be affected when they access the compromised live event history entry.

The affected functionality is part of the application's administrative features for managing live events.

> The demonstrated proof of concept only confirms JavaScript execution using `alert(123)`. Higher-impact consequences such as session compromise or unauthorized actions depend on the application's authentication, authorization, cookie settings, CSP, and victim privileges.

---

# Vulnerability Classification

## CWE

**CWE-79 – Improper Neutralization of Input During Web Page Generation (Cross-Site Scripting)**

## Vulnerability Class

**Stored Cross-Site Scripting (Persistent XSS)**

## OWASP Classification

**A03:2021 – Injection**

---

# CVSS Assessment

A possible **CVSS v3.1** vector is:

```text
CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:C/C:L/I:L/A:N
```

## Severity

**Medium**

---

# CVSS Rationale

### AV:N – Network

The vulnerability is exploitable through the web application.

### AC:L – Low

Exploitation requires submission of a crafted payload through the affected functionality and does not require complex conditions.

### PR:L – Low

Access to the live event management functionality may be required to submit the malicious content.

### UI:R – Required

Another user must access the affected live event history page for the stored payload to execute in their browser.

### S:C – Changed

The vulnerability can cause JavaScript execution in the browser context of another user.

### C:L – Low

Sensitive information accessible to JavaScript may potentially be exposed, depending on the application's architecture and security controls.

### I:L – Low

An attacker may potentially perform actions in the victim's application context, depending on the victim's privileges.

### A:N – None

No direct availability impact was demonstrated during testing.

> The final CVSS vector should be adjusted according to the exact privileges required and the confirmed impact in the deployed application.

---

# Suggested Remediation

## 1. Apply Context-Aware Output Encoding

All user-controlled values should be encoded before being rendered in HTML.

For example:

```php
echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
```

This ensures that characters such as:

```text
<
>
"
'
```

are treated as text instead of executable HTML markup.

---

## 2. Review the `eventSubject` Output

The code responsible for rendering event subjects in:

```text
/humanresourcemanagementsystem/views/admin/liveEventHistory.php
```

should be reviewed.

If the application currently performs unsafe output such as:

```php
echo $eventSubject;
```

it should instead use context-appropriate output encoding:

```php
echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
```

---

## 3. Validate User Input

The `eventSubject` parameter should be validated according to the expected application requirements.

If event subjects are intended to contain plain text, HTML markup should not be accepted.

For example:

```php
$eventSubject = trim($_POST['eventSubject'] ?? '');
```

Input validation should be considered an additional security layer and should not replace proper output encoding.

---

## 4. Review Other Event Parameters

Other user-controlled event fields should also be reviewed for similar vulnerabilities, including:

```text
eventSubject
eventDesc
eventDate
eventTime
```

Any user-controlled value that is stored and later rendered should use context-appropriate output encoding.

---

## 5. Implement a Content Security Policy

A restrictive Content Security Policy may help reduce the impact of successful XSS vulnerabilities.

Example:

```http
Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none'
```

The policy should be tested against the application's legitimate functionality.

CSP should be considered a defense-in-depth measure and should not replace proper output encoding.

---

# Additional Payload Tested

An additional payload was also tested:

```html
#"><img src=/ onerror=alert(2)>
```

This payload uses a different HTML injection technique from the `<script>` payload.

The payload attempts to break the existing HTML context and inject an `<img>` element with an `onerror` event handler.

The relevant JavaScript execution is:

```javascript
alert(2)
```

If both payloads execute through the same parameter and rendering location, they should generally be documented as different proof-of-concept payloads for the **same underlying Stored XSS vulnerability**, rather than as separate vulnerabilities.

---

# POC Video

https://github.com/user-attachments/assets/f4902bfb-fc8e-4e4c-8ef8-01fde7c51038
