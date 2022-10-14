- [Vulnerability Response](#vulnerability-response)
  - [Introduction](#introduction)
  - [Clearly documenting where probable security flaws are to be reported](#clearly-documenting-where-probable-security-flaws-are-to-be-reported)
  - [What should a security report contain:](#what-should-a-security-report-contain)
  - [Security advisory](#security-advisory)
  - [Set proper lifetime of your software](#set-proper-lifetime-of-your-software)
  - [Conclusion](#conclusion)

# Vulnerability Response

## Introduction
Vulnerability Response response is a very important part of secure development. Rather than pro-active security it lays down the rules and procedures about how to handle an incident, when a security flaw has been found by someone, how to report such an incident, track them, fix them, make them public if required and how to document them so that they can be easily consumed by users.

## Documenting where probable security flaws are to be reported
This should be present on the front page of the project site or wherever its web presence is. 
- If you are the only developer of this project clearly mention your name and email address or any other way you would like to receive security reports of your project. If there are multiple developers, then a designated person/email should be appointed to receive these emails or reports. It is often preferable to create a <security@project-name.domainname.com> instead of personal email addres as reporters often feel more comfortable reporting to this type of address rather than anywhere else.
- How you want your reports to be sent is also another useful thing here. Some researchers don't like plain text reports, they prefer GPG encrypted. Can you support that? otherwise, you can mention that you prefer plain text reports only. 
- Public or not? This is a very important question for researchers, developers and users. If your reported issue is serious and may cause harm to your users, for example, you have a web application and an easy SQL injection issue was found, if you make it public without fixing it, it may lead to your users getting compromised. Under such conditions, you would want to keep it embargoed till you can fix the issue first. It depends on what the issue is and how important your project is.
- Work with the reporter, on how they found the flaw, and how to reproduce it, and maybe share the patch with them, they may be able to find further flaws if the patch was not sufficient. 
- Once you have a patch, test it again, it's a security flaw, and you dont want your patch to lead to other future flaws, which would result in a bad reputation for your project.
- Think of metadata, do you want to assign CVE etc to the flaw, some projects prefer some other ID mechanism. Some way of identifying "this" flaw from "that" would be useful to everyone.
- Lastly, think of how you want to release it. Do you just want to do a Tuesday release by making the patch public and spinning a new tarball or are there some other things which need to fall in place, before you can do the release?
- Also, think of how you want to write security advisories. We will talk about them in the sections below.

## What should a security report contain:
At the very minimum, what the issue is, what version it affects, how it can be reproduced, what environment the researcher used to reproduce the issue and if they has any Proof of Concept. \
\
For example, refer to [Red Hat's security page](https://access.redhat.com/security/team/contact) on how to contact the security team and what information Red Hat would need. You don't have to have an efficient and large security team, but the basic information remains the same.
<kbd>
![Red Hat Security Advisory](../images/rh-security.png)</kbd>

## Security advisory
Now that you have resolved the security issue with your software and released a fixed version, how do you let the users know that a new version which fixes security issues is available? One way of doing this is to write a security advisory page for each issue found. The advisory should contain at the minimum, some basic information about what the security flaw was, what is the possible risk to the users, which version fixes it, any other mitigating factors and some metadata like CVE id if required.\
\
The diagram below shows a typical security advisory, this one is from the LibreOffice project and has all the essential information users would require:
![Libreoffice advisory](../images/libre.png)
Most people think you need some security background to write a good advisory, but that is not the case. An advisory is supposed to be consumed by the users of the application and not some security engineer, so keep it simple and provide all the important information which is required.

## Set a proper lifetime for your software
If you are working on various versions of your software, you will eventually reach a point where you may not continue maintaining all of them. Therefore it may be necessary to sunset older versions at some point in time. Ensure you have a plan for this, as this is important from a security perspective, for example, end users may still be using older versions not aware of the fact, that you don't backport security issues to the older versions anymore.

## Conclusion
A basic Vulnerability Response program is very important. It can be a web page informing readers how you handle security and does not need to be fancy, just a simple process which is operational.
