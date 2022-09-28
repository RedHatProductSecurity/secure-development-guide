# Code audit

## What do we mean by code audit
Code audit is perhaps the most interesting secure development process out there, mainly because it has different meanings for different people. Code audit solutions range from automated audit tools (perhaps [SAST](../code-scanning/sast) could also be considered as a form of automated code-audit?) to manual reading and analyzing each line of code by an expert.\
\
Irrespective of the way people interpret its meaning, its one of the oldest and perhaps most effective way of finding flaws in your code.\
\
In this section we will look at some nuances of the process, and how it can be a useful tool in your secure development arsenal.

## Where do I start?
- We need to first understand the criticality of the underlying codebase. The more critica the code, the more detailed the audit process. For example, parts of an application which does pure UI without any user input, may not be very important to audit, as compared to parts where user input is required via web or parts which access your database or even bits which run as priviledged user no your server.

- Secondly, look at the resources available to you. Code audits are most effective when done by someone who did not write the code in the first place. Do you have developers or security engineers at your disposal who can take a look at your code? Sometimes even poepe in other functions like QE folks, or in case of an open source project even contributors whose day job is developement or review may be helpful in this process. 

- Audit quality depends on the skills of the auditor. It also depends on how well the application is documented in the first place, so that the auditor can understand the various components and data flow etc, which can aid an audit. Code auditors may lose interest if they find application logic difficult to follow or a complete absence of documentation. Even the best of the auditors sometimes ignore things which they feel are a waste of time. So audit quality is a combination of auditor skills and developer contribution either via documentation or being able to talk to the auditor about the application.

## Take care of your embeds
If your application is statically compiling a third party open source library, ensure that the audit process accounts for that. Here the expectation is not to audit all the compiled third party libraries, but be aware of the various interfaces or API calls to them. There should be no assumption that the library API interfaces will sanitize the input parameters unless otherwise stated in their documentation. Therefore such differences should be carefully considered and accounted for.

## The audit report
Writing an audit report can sometimes be more of an art than a science. However your audit reports done have to have the perfect formatting or even the best fonts, as long as you have the correct and the right amount of information, they should be fine. Normally audit reports include the following information
- General overview of the application design, data flow etc, what parts were audited, what methods were used to audit the code.
- List of flaws/weaknesses found with as much detail as possible. How the flaw can be triggered, what is the result, what is the impact on the application. 
- Some bookkeeping, like was a [CVE id](https://cve.mitre.org/) or [CVSS score](https://www.first.org/cvss/) assigned to the flaw. This usually helps customers/users risk involved if they dont patch it.
- Bugs/report links which were filed as a result of the audit. Including patch details and versions containing those patches. More details in the coming section.
- In the end, some administrative information like details of the persons doing the audit, date and time stamps, where is the document going to be stored, will it be made public etc.

## What to do after the audit is done/ audit report is written 
Now that your audit is done and the audit report is written, what we do next is the most important part.
- Each of the flaws found need to be tracked in some way, so that they can analyzed and later some action taken on them, either patched or maybe ignored.
- If patched, the flaw bug or report should corrected mentioned the patchset which fixes the issue and if a new release has been made, the release details where the customers can find the patched application.
- Often security advisories are created to inform users about possible security issues found and patched.
- Think of any proactive measures you need to take to avoid these kind of issues in the future, it would be a change in the way the code is written, making peer review important before commit or could even be change in the design of the application to avoid future security issues.


