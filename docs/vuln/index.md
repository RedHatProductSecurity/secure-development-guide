# Vulnerability Response

## Introduction
Vulnerability Response response in a very important part of secure development. Rather than pro-active security it rather lays down the rules and procedures about how to handle an incident, when a security flaw has been found by someone, how to report such incident, track then, fix them, make them public if required and how to document them so that they can be easily consumed by users.

## Clearly documenting where probable security flaws are to be reported
This should be present on the front page of the project site or whereever its web presence is. 
- If you are the only developer of this project clearly mention your name and email address or any other way you would like to recieve security reports of your project. If there are mulitple developers, then a designated person who will recieve these emails or reports. It is often preferable to create a <security@project-name.domainname.com> instead of personal email addresses, reporters often feel more comfortable reporting to this address rather than anywhere else.
- How do you want your reports to be sent is also another useful thing here. Some researchers dont like plain text reports, they prefer GPG encrypted. Can you support that? otherwise you can mention that we prefer plain text reports only. 
- Public or not? This is a very important question for researchers, developers and users. If your reported issue is serious and may cause harm to your users for example, you have a web application and a easy SQL injection issue was found, if you make it public without fixing it, it may lead to your users getting compromised. Under such conditions you want want to keep it embargoed till you are able to fix the issue first. It all really depends on what the issue is and how important your project is.
- Work with the reporter, on how he found the flaw, how to reproduce it, maybe share the patch with him, he may be able to find further flaws if the patch was not sufficent. 
- Once you have a patch, test it again, its a security flaw, you dont want your patch to lead to other future flaws, that would really mean bad reputation for your project.
- Think of metadata, do you want to assign CVE etc to the flaw, some projects prefer some other ID mechanism. Some way of intendifying "this" flaw from "that" would be useful to everyone.
- Lastly think of how you want to release it. Do you just want to do a tuesday release by making patch public and spinning a new tarball or are there some other things which need to fall in place, before you can do the release.
- Also think of how you want to write security advisories. We will talk about them in the sections below.

