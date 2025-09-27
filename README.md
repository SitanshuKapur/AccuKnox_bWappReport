# AccuKnox_bWappReport
Candidate submission of report on manual penetration testing performed on bWapp as a part of application to AccuKnox for Security Officer Internship Role



# High level Summary

This report outlines the findings of a security assessment conducted on the locally deployed bWAPP web application. The assessment identified four significant vulnerabilities, including two of critical severity.

The most severe issues discovered were OS Command Injection and PHP Code Injection, both of which allow an attacker to execute arbitrary commands directly on the underlying server. Successful exploitation could lead to a full system compromise, resulting in data theft, service disruption, and unauthorized access to internal infrastructure.

Additional high-impact vulnerabilities, such as Stored HTML Injection, were also identified, posing a risk to all users of the application. The overall security posture of the application is considered **critical**. Immediate remediation of the identified findings is strongly recommended to mitigate the risk of a breach.

### Methodology
The assessment was conducted using manual penetration testing techniques. An intercepting proxy was used to analyse and manipulate HTTP requests to identify injection flaws and other common web application vulnerabilities.

## OS Command Injection

Severity - Critical

this is in the DNS lookup functionality where breaking out of the syntax is done using the semicolon 
![[Pasted image 20250927095041.png]]

#### Impact
This vulnerability allows an attacker to take full control of the underlying server, running commands with the same privileges as the web server process. This can lead to data exfiltration, installation of malware, or pivoting to other systems on the network.
#### Mitigation
Input Sanitization: Strictly validate user input to ensure it only contains expected characters (e.g., IP addresses or valid hostnames).
Avoid System Calls: Whenever possible, avoid calling system shell commands with user-supplied input. Use language-specific libraries for functionalities like DNS lookups.


## PHP Code Injection

severity : Critical

the URL behaviour reflects a PHP parameter called message
```
?message=testmessage
```
![[Pasted image 20250927101456.png]]

modifying to escape input
```
?message=test-message;system("whoami")
```
![[Pasted image 20250927101404.png]]

#### Impact
This allows an attacker to execute arbitrary code and commands on the server, leading to a complete system compromise.
#### Mitigation
Disable Dangerous Functions: Disable potentially dangerous PHP functions like `eval()`, `system()`, `passthru()`, and `exec()` if they are not essential to the application's functionality. 
Input Validation: Treat all user input as untrusted. Implement a strict allow-list of characters and sanitize all input before it is processed.


## Stored HTML Injection

Severity: High

![[Pasted image 20250927100114.png]]

#### Impact
This can be escalated to Stored Cross-Site Scripting (XSS). An attacker could inject malicious JavaScript to steal session cookies, perform actions on behalf of other users, or deface the website for every visitor.

#### Mitigation
Output Encoding: Implement context-aware output encoding on all user-supplied data before it is rendered on a page. This ensures the browser treats the data as text, not as executable code.
Content Security Policy (CSP): Implement a strong CSP to restrict the sources from which scripts can be loaded, reducing the impact of any potential XSS flaws.

## HTML Injection in post request

Severity- Medium

normal web application working
![[Pasted image 20250927094122.png]]

this is what the post request looks like
![[Pasted image 20250927094212.png]]

changing the parameters in the request we have
```xml
firstname=<h1>greetings</h1>&lastname=injection&form=submit
```

and the response is seen as
![[Pasted image 20250927094419.png]]

#### Impact
This is a form of Reflected Cross-Site Scripting (XSS). An attacker could craft a malicious link or form that, when clicked by a victim, executes scripts in the context of that user's session.

#### Mitigation
Output Encoding: Apply context-aware output encoding to any user-supplied data that is reflected back in the HTTP response.
Input Validation: Validate and sanitize all user input on the server side to prevent malicious payloads from being processed.
