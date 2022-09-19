# Secure Development Guide

## Welcome to the Secure Development Guide!

The purpose of this guide is to discuss industry accepted secure development practices specially applied to the open source context. While there are many references and documents freely available on the internet, this guide specifically talks about secure application design and secure development processes and practices. Currently secure coding is out of scope and is [covered by another similar document.](https://docs.fedoraproject.org/en-US/defensive-coding/)


- [What are secure development practices](#what-are-secure-developement-practices)
- [How does all this apply to open source?](#how-does-all-this-apply-to-open-source)
- [Threat Modeling](threat-modeling/#threat-modeling)
- [Code scanning](code-scanning)
  - [Static Analysis or SAST](code-scanning/sast.md)
  - [Dynamic Analysis or DAST](code-scanning/dast.md)
  - [Source Composition Analysis](code-scanning/sca.md)
  - [Scanning for Malware/Virus](code-scanning/malware.md)
- [Fuzzing](fuzzing)
- [Code Audit](code-audit)
- [Manifesting/SBOM](manifest)
- [Vulnerability Response](vuln)
- [Education](education)
- [References](references)

## What are secure development practices
There are many aspects of secure development. Secure development is a mindset and not just technical know-how of how to code and build securely. It is something which affects all parts of software development lifecyle. Starting from secure design, to code development, QE, release pipeline, patching, support, security advisories and even documentation. \
\
It is often said that the strength of a chain is strength of its weakest link, this is very rightly applicable to application security as well. Therefore just developer education is not enough, it requires a change in every part of team which deals with the development of said application.\
\
This document aims to look at some of these practices in brief. The aim is not to provide exhaustive documentation, but to provide enough information, to get everyone excited about the process. It is expected that application project members will seek more information to re-enforce this basic knowledge and think about applying them in their open source projects.

## How does all this apply to open source?
Most people think that Secure Development LifeCyle is something which can only be applied to software created from scratch in a controlled corporate environment. This is not true!!. Secure development practices can very well be applied to open source software and are even applicable for packagers or application developers who use open source libraries or code.\
\
This guide contains processes which can applied to code independent of who developed it or on what stages of development the codebase is. In fact we think they can be more easily applied to open source software because a lot of these techniques and tools are more suitable to the fact that the code is publicly available.\
\
So buckle up!! And get ready to learn secure software development for open source software!!
\
## [License](license)

## Contributing
All types of contribution to this document are welcome. PRs are preferable. For larger changes it would perhaps make more sense in discussing them before hand before filing change requests.

## Contributors
- Huzaifa Sidhpurwala <huzaifas@redhat.com>
- Judy Kelly <jkelly@redhat.com>
- Jeremy Choi <jechoi@redhat.com>
