# Stored Cross-Site Scripting (XSS) in Human Resource Management System

## Credit

**Discovered by:** Ahmad-masa10

---

## Product

**Human Resource Management System in PHP with Source Code**

## Vendor

**Code-Projects**

## Vendor Homepage

https://code-projects.org/human-resource-management-in-php-with-source-code/

## Affected Version

**1.0 / Tested Version**

## Vulnerability Type

**Stored Cross-Site Scripting (Stored XSS)**

## CWE

**CWE-79: Improper Neutralization of Input During Web Page Generation (Cross-site Scripting)**

## Severity

**Medium**

---

## Description

A Stored Cross-Site Scripting vulnerability was identified in the Human Resource Management System in PHP.

The vulnerability allows attacker-controlled input to be stored by the application and later rendered as HTML in the browser without sufficient output encoding.

The tested payload was:

```html
#"><img src=/ onerror=alert(2)>
```

When the stored value is rendered by the application, the browser interprets the injected HTML and triggers the `onerror` event, resulting in execution of:

```javascript
alert(2)
```

This confirms that attacker-controlled HTML/JavaScript can be executed in the context of the affected application.

---

## Vulnerable Parameter

```text
eventSubject
```

---

## Affected Endpoint

```text
http://localhost/humanresourcemanagementsystem/views/admin/noticeHistory.php
```

---

## HTTP Method

```text
POST
```

The malicious value is submitted through the application's event/notice functionality and stored server-side.

The stored content is subsequently retrieved and rendered through:

```http
GET /humanresourcemanagementsystem/views/admin/noticeHistory.php
```

### Full Affected URL

```text
http://localhost/humanresourcemanagementsystem/views/admin/noticeHistory.php
```

---

## Proof of Concept Payload

```html
#"><img src=/ onerror=alert(2)>
```

---

## Payload Explanation

The payload consists of three important parts:

```html
#">
```

Attempts to break the existing HTML attribute/context.

```html
<img src=/
```

Creates an injected image element.

```html
onerror=alert(2)
```

Uses the image error event to execute JavaScript when the image fails to load.

The final result is:

```javascript
alert(2)
```

which demonstrates successful JavaScript execution.

---

## Steps to Reproduce

1. Log in to the administrative interface of the application.
2. Navigate to the functionality used to create a notice/event.
3. Insert the following payload into the vulnerable input:

```html
#"><img src=/ onerror=alert(2)>
```

4. Submit the form.
5. Open the affected page:

```text
http://localhost/humanresourcemanagementsystem/views/admin/noticeHistory.php
```

6. Observe the stored value being rendered.
7. The browser executes:

```javascript
alert(2)
```

8. A JavaScript alert dialog appears, confirming Stored XSS.

---

## Result

### Expected Behavior

The application should display the submitted value as harmless text:

```text
#"><img src=/ onerror=alert(2)>
```

### Actual Behavior

The browser interprets the injected HTML and executes the JavaScript contained in the `onerror` event handler.

```javascript
alert(2)
```

This confirms a Stored Cross-Site Scripting vulnerability.

---

## Root Cause

The root cause is insufficient context-aware output encoding of attacker-controlled data before it is rendered in an HTML page.

User-controlled input should be encoded before being inserted into the HTML response.

For example:

```php
echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
```

Input validation can also be used as an additional security measure, but it should not replace proper output encoding.

---

## Impact

An attacker who can submit malicious content to the vulnerable field may be able to execute JavaScript in the browser of another user who views the affected notice history page.

Depending on the privileges of the victim and the application's security configuration, possible consequences may include:

* JavaScript execution in the application's origin.
* Manipulation of page content.
* Phishing or UI manipulation.
* Performing actions available to the victim.
* Access to information exposed to client-side JavaScript.
* Potential impact against privileged administrative users.

The current proof of concept only demonstrates JavaScript execution through:

```javascript
alert(2)
```

and does not by itself demonstrate account takeover or data theft.

---

## Vulnerability Classification

| Field         | Value                       |
| ------------- | --------------------------- |
| Vulnerability | Stored Cross-Site Scripting |
| CWE           | CWE-79                      |
| Parameter     | `eventSubject`              |
| Affected Page | `noticeHistory.php`         |
| HTTP Method   | POST / GET                  |
| Execution     | Client-side browser         |
| Severity      | Medium                      |
| Discovered By | Ahmad-masa10                |

---

## Suggested Remediation

### 1. Apply Output Encoding

Encode user-controlled values before rendering them in HTML:

```php
echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
```

### 2. Validate Input

Validate the expected format and length of notice/event fields.

### 3. Review Other Parameters

The application should also review other user-controlled fields for the same issue, particularly:

```text
eventSubject
eventDesc
noticeTitle
noticeDesc
```

### 4. Implement Content Security Policy

A restrictive CSP can provide an additional layer of protection against XSS:

```http
Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none';
```

CSP should be treated as defense-in-depth and should not replace proper output encoding.

---

# POC Video
https://github.com/user-attachments/assets/680e64b9-e2fd-4f8d-bd45-ec92dd2f7651
