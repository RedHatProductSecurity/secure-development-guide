# Static application security testing (SAST)

## Introduction
Also known as static analysis, this method works by running specialized software called static analyzers on the application source code. Static Analyzers use an internal model of the execution process of the application and based on in-built rules they are able to detect anomalies with the source code. Some of these issues can result in security flaws. A lot of common vulnerability classes can be detected by using static analyzers. 

## Why do SAST?
- SAST tools can give developers early feedback about the quality of their code and help detect security flaws even before the code is built and testing by QE. This initial detected of flaws can result in quick resolution of issues very early in the development cycle.
- Most SAST tools are able pin point the exact location of code, which caused the security flaw as compared with other efforts like fuzzing etc. This helps developers eliminate risky parts of code, even if the developer has very limited security experience. 
- Another key advantage of the SAST process, is that 100% of the codebase is analyzed, even difficult to reach branches or dead code. 
- Its much faster than manual reviews or code-audits

## 
