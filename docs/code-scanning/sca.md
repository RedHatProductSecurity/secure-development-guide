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
SCA processes are often used by customers/consumers because they are not sure what open source code is contained in the products they buy from application developers. However using SCA in software development process can add a lot of value to the process. Consider for example a team of developers working on a project. Due to the open source nature of work, it is often the case, where developers will take code or even complete codebases from other open source projects (in compliance with their licenses ofcourse). This is how open source software is development!, but sometimes can cause security risks for customers, when a serious security flaw in found in the same codebase and no one knows if your application uses it in a vulnerable way. This is where SCA can help and add value to your final product.

## Some factors to consider
- SCA is most useful when you have big complex projects, with multiple developers working on it. Most open source projects prefer to dynamically link libraries to the main application, rather than statically linking them. This is the most optimal option from a security point of view. Running SCA on such applications may not yield a lot. 
- In some cases there is cost to benefit to consider as well. Commercial SCA products may be more expensive v/s the benefit you get out of them. Consider automation benefits v/s doing it manually also. Lastly your customers may really want a machine generated SBOM if they buy your product, which is another thing to consider.

## SBOM
[A “software bill of materials” (SBOM)](https://www.cisa.gov/sbom) has emerged as a key building block in software security and software supply chain risk management. A SBOM is a nested inventory, a list of ingredients that make up software components. As mentioned above it may contain list of components, their licenses, and version information.\
\
This term actually comes from manufacturing industry, where a Bill of Materials is an inventory detailing all the items included in a product. For each product, the manufacturer knows, what the product is made up of, where each part was sourced from and other details of the part. So when a fault is found in one of the parts, the manufacturer knows exactly what all products are affected and need to be replaced.\
\
There are several formats which are currently being used and include, SPDX, SWID and CycloneDX\

## Conclusion


