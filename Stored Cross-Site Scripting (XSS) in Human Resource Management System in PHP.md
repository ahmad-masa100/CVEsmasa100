## Stored Cross-Site Scripting (XSS) in Human Resource Management System in PHP
## Credit
Discovered by: Ahmad-masa10

## Product
Human Resource Management System in PHP

## Vendor
Code-Projects

## Vendor Homepage
Human Resource Management System in PHP with Source Code

## Affected Version
Human Resource Management System in PHP — version should be confirmed from the distributed source package.

Note: Before submitting this to VulDB or requesting a CVE, it is best to verify the exact version number from the application's source code or official package.

## Vulnerability Type
Stored Cross-Site Scripting (Stored XSS)

CWE
CWE-79 – Improper Neutralization of Input During Web Page Generation (Cross-Site Scripting)

## Severity
Medium

## Description
The Human Resource Management System in PHP is affected by a Stored Cross-Site Scripting (XSS) vulnerability in the event creation functionality.

The vulnerability occurs when the application processes user-controlled input supplied through the eventSubject parameter and stores the submitted value through the following component:

/humanresourcemanagementsystem/src/store/EventStore.php
The application accepts attacker-controlled input containing HTML markup. If the submitted value is stored and subsequently rendered within the application interface without proper context-aware output encoding, an attacker can inject malicious HTML or JavaScript-capable markup that executes in the browser of users viewing the affected event.

Testing confirmed that the following payload could be submitted through the eventSubject parameter:

<details/open/ontoggle=prompt(origin)>
Because the malicious value is stored server-side and can execute when the affected content is subsequently rendered, the vulnerability is classified as a Stored (Persistent) Cross-Site Scripting vulnerability.

An attacker with access to the event creation functionality could potentially exploit this issue to execute arbitrary JavaScript in the browser of other users who view the malicious event.

## Vulnerable File
/humanresourcemanagementsystem/src/store/EventStore.php
The event submission functionality is accessed from:

/humanresourcemanagementsystem/views/admin/sendLiveEvent.php
## Vulnerable Parameter
## eventSubject
Affected Endpoint
/humanresourcemanagementsystem/src/store/EventStore.php
## HTTP Method
POST
## Root Cause
The vulnerability is caused by improper handling of user-controlled input within the event creation functionality.

Specifically:

The application accepts user-controlled input through the eventSubject parameter.

The supplied value can contain attacker-controlled HTML markup.

The submitted value is stored by the application.

The application does not sufficiently neutralize dangerous HTML characters before the value is rendered in the affected web interface.

When the stored value is displayed without proper output encoding, the browser may interpret attacker-controlled markup as active HTML.

The vulnerable data flow can be summarized as follows:

Attacker-controlled input
        ↓
eventSubject parameter
        ↓
EventStore.php
        ↓
Stored server-side
        ↓
Rendered in application interface
        ↓
Browser interprets malicious markup
        ↓
Stored XSS
The issue should be remediated primarily through context-aware output encoding wherever eventSubject data is rendered.

For example:

echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
Payload Used During Testing
<details/open/ontoggle=prompt(origin)>
When the affected stored event content is loaded in the application interface, the injected markup may execute within the browser context of the user viewing the affected content.

Because the payload is stored server-side and can affect users who subsequently view the compromised event, this vulnerability is classified as a Stored (Persistent) Cross-Site Scripting vulnerability.

## Proof of Concept
The following request demonstrates submission of the XSS payload through the vulnerable eventSubject parameter:

```plain
POST /humanresourcemanagementsystem/src/store/EventStore.php HTTP/1.1
Host: localhost
Content-Length: 117
Cache-Control: max-age=0
sec-ch-ua: "Not;A=Brand";v="8", "Chromium";v="150"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/150.0.0.0 Safari/537.36
Origin: http://localhost
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: http://localhost/humanresourcemanagementsystem/views/admin/sendLiveEvent.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=REDACTED
Connection: keep-alive

eventSubject=%3Cdetails%2Fopen%2Fontoggle%3Dprompt%28origin%29%3E&eventDate=2026-09-10&eventTime=18%3A33&eventDesc=ss
Decoded Request Body
eventSubject=<details/open/ontoggle=prompt(origin)>
eventDate=2026-09-10
eventTime=18:33
eventDesc=ss
```
XSS Payload
<details/open/ontoggle=prompt(origin)>
PoC 1: HTTP request intercepted and modified using Burp Suite.

## Steps to Reproduce
Install and configure the Human Resource Management System in PHP application.

Log in to an account with access to the administrator event management functionality.

Navigate to the following page:

/humanresourcemanagementsystem/views/admin/sendLiveEvent.php
Create a new event.

Intercept the event submission request using Burp Suite or another HTTP proxy.

Insert the following payload into the eventSubject parameter:

<details/open/ontoggle=prompt(origin)>
Submit the modified request to:

/humanresourcemanagementsystem/src/store/EventStore.php
Allow the application to store the submitted event.

Navigate to the application page where the stored event subject is displayed.

Observe the behavior of the injected payload when the affected event is rendered.

## Result
The malicious payload is submitted through the eventSubject parameter and stored by the application.

When the affected event is subsequently displayed in an application context that renders the stored value without proper HTML output encoding, the browser interprets the attacker-controlled markup.

This confirms a Stored Cross-Site Scripting vulnerability when the stored payload is rendered in the vulnerable output context.

## Impact
Successful exploitation of this vulnerability may allow an attacker to:

Execute arbitrary JavaScript in victim browsers.

Modify the content displayed to users.

Perform actions within the application in the context of a victim user.

Conduct phishing attacks using injected application content.

Target administrative users who view malicious events.

Access sensitive information available to JavaScript in the affected application context.

Potentially compromise user sessions depending on cookie attributes and other application security controls.

Because the payload is stored server-side, the attack may affect multiple users who subsequently view the compromised event.

The final impact depends on the application's authentication controls, user privileges, cookie configuration, Content Security Policy, and the specific location where the stored event data is rendered.

## Vulnerability Classification
CWE
CWE-79 – Improper Neutralization of Input During Web Page Generation (Cross-Site Scripting)

OWASP
OWASP Top 10: A03:2021 – Injection

Note: In newer OWASP guidance, XSS is generally treated under broader web application security categories rather than as a standalone Top 10 category. For a vulnerability database report, CWE-79 is the most precise classification.

Suggested Remediation
Output Encoding
All user-controlled data should be properly encoded before being inserted into HTML responses.

For example:

echo htmlspecialchars($eventSubject, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
The correct encoding method should match the output context, such as:

HTML body

HTML attribute

JavaScript

URL

CSS

Input Validation
The application should validate event subject input according to the expected format.

If HTML is not required, markup should not be accepted as event subject content.

Example approach:

$eventSubject = trim($_POST['eventSubject'] ?? '');
Input validation should be considered a secondary defense and should not replace output encoding.

Use a Secure HTML Sanitizer
If the application intentionally allows users to submit HTML, a well-maintained allowlist-based HTML sanitizer should be used.

Custom blacklist filtering is not recommended as the primary XSS defense.

Content Security Policy
Implement a restrictive Content Security Policy to reduce the impact of successful XSS exploitation.

Example:

Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none'
The policy should be tested to ensure compatibility with legitimate application functionality.

Review Other Event Parameters
Additional parameters related to event functionality should also be reviewed for similar vulnerabilities, including:

eventSubject
eventDesc
eventDate
eventTime
Any user-controlled data rendered in the application should be output-encoded according to its rendering context.

CVSS
A provisional CVSS v3.1 vector may be:

CVSS:3.1/AV:N/AC:L/PR:L/UI:R/S:C/C:L/I:L/A:N
The final CVSS score should be assigned after confirming:

Authentication requirements.

Required attacker privileges.

Whether another user must view the stored payload.

The affected user roles.

The exact impact on confidentiality and integrity.

Whether the application uses mitigating browser or cookie security controls.

References
Code-Projects — Human Resource Management System in PHP

CWE-79 — Cross-Site Scripting

OWASP Cross Site Scripting Prevention Cheat Sheet

## POC video 
https://github.com/user-attachments/assets/d76103c5-6bc6-461c-bfed-c539e2d9ec8f
