- [Source Composition Analysis](#source-composition-analysis)
  - [Introduction](#introduction)
  - [How does it work?](#how-does-it-work)
  - [Importance of SCA](#importance-of-sca)
  - [Some factors to consider](#some-factors-to-consider)
  - [SBOM](#sbom)
  - [Conclusion](#conclusion)

# Source Composition Analysis

## Introduction
Software Composition Analysis is a process of analyzing an application, to detect third party libraries statically linked with the application. It can be mostly used to detect and fix security flaws in these embedded code bases/libraries, but can also be used to detect illegal and outdated code. Continous SCA testing helps developers and security teams to drive productivity without compromising on the security of the application.

## How does it work?
SCA is mostly automated by the use of tools. It works by looking at various sources of information which includes source code, manifest files, package manager metadata, container images and sometimes binary files also. Once a list of libraries/third party code is obtained, it is used to generate a Software Bill of Materials (SBOM). This is then compared with openly available security databases like NVD to generate security alerts for the scanned application in question\
\
SCA tools can also generate license information which can be a part of SBOM. 

## Importance of SCA
## Some factors to consider
## SBOM
## Conclusion


