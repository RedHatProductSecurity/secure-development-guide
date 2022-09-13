- [Dynamic application security testing (DAST)](#dynamic-application-security-testing-dast)
  - [What is DAST](#what-is-dast)
  - [Why is DAST important for your code?](#why-is-dast-important-for-your-code)
  - [Phases of DAST](#phases-of-dast)
  - [How does DAST differ from SAST](#how-does-dast-differ-from-sast)
  - [RapidDAST](#rapiddast)
  - [Conclusion](#conclusion)

# Dynamic application security testing (DAST)
DAST stands for dynamic application (or analysis) security testing, and is a blackbox testing methodology used to uncover potential security flaws by performing automated security scanning against a running target. DAST works by simulating automated attacks on an application, mimicking a malicious attacker. The goal is to find outcomes or results that were not expected and could therefore be used by attackers to compromise an application.

## What is DAST
DAST is usually a term broadly used for testing a running target for security flaws, it usually refers to web based applications. This has become quite prevalant in the last decade due to large number of web and
API based applications.


## Why is DAST important for your code?

## Phases of DAST
###  Identify endpoints
In order to improve the security of something, the first step is to identify what actually needs to be secured. API scanning starts with identifying endpoints, called attack surfaces, where attackers input malicious data — such as URLs and parameters — in an attempt to compromise the web application. It should be noted that any data in the web requests can be freely modified by attackers.

To automatically identify web endpoints, most DAST tools employ crawling or a spider feature. API endpoints can be difficult to identify with crawling, however API scanning can be done effectively by taking advantage of [OpenAPI.](https://www.redhat.com/en/blog/automated-dynamic-application-security-testing-enhancing-development-security-rapidast#openapi-based-scanning)

### Send requests

Once the endpoints have been identified, DAST tools start sending lots of requests that simulate real attacks. To reach the protected contents, the tools can use established sessions that are normally implemented with cookies or tokens, or even guide users to login to the target.

APIs generally rely on tokens for authentication, whereas most traditional web applications typically use cookies. In some cases, both authentication techniques are used for the same website. It is not uncommon to see the frontend application use a cookie and the backend API use a token at the same time.

### Analyze results

The previous phase results in a lot of responses and results. Analyzing them, with configured scanning rules, can detect security vulnerabilities such as SQL injection and cross-site scripting (XSS), in addition to many other weaknesses such as misconfiguration issues.


## How does DAST differ from SAST

## RapidDAST

## Conclusion

