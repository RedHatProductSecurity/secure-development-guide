- [Component Manifest / Software Bill of Materials](#component-manifest-software-bill-of-materials)
  - [Introduction](#introduction)
  - [What is a manifest](#what-is-a-manifest)
  - [Generating/Managing/Using a manifest](#generatingmanagingusing-a-manifest)
  - [Software Bill of Materials](#software-bill-of-materials)
    - [Software Package Data Exchange (SPDX)](#software-package-data-exchange-spdx)
    - [CycloneDX](#cyclonedx)
    - [Software Identification Tags (SWID)](#software-identification-tags-swid)

# Component Manifest / Software Bill of Materials

## Introduction
Complex open source offerings are often made of multiple open source components. These need to be kept track of for the purpose of tracking security issues, applying fixes when they are available, etc. Depending on the complexity of the project and sometimes customer requirements there are various ways of doing this. In this section, we describe a simple approach called a manifest file. If your requirements are more complex, for example, keeping track of license information, then there is something called SBOM or Software Bill Of Materials which we will try to describe on this page also.

## What is a manifest
[A manifest file](https://en.wikipedia.org/wiki/Manifest_file) in computing is a file containing metadata for a group of accompanying files that are part of a set or coherent unit. For example, the files of a computer program may have a manifest describing the name, version number, license and constituent files of the program.\
\
As described above, the primary use of a manifest is to enumerate files or components present in an offering. Other useful information like version numbers etc can also be included for tracking purposes.\
\
Manifest files can also be cryptographically signed when they are provided to third parties for verification or even to customers, to make sure their integrity is preserved. They can be human readable text files or can be XML, JSON or whatever format suits the underlying generating and parsing mechanism.

## Generating/Managing/Using a manifest
Manifest file generation is and should be an automated process. It does not make sense to write a manifest manually. This eliminates human errors in the process, including the fact that the person generating the manifest by hand, could leave the organization one day without documenting the correct process.\
\
On similar lines, unless a manifest file is kept up to date, it has very little meaning. Updates to a manifest file should be triggered whenever a new release of the software is complete or on a timely basis when we are dealing with rolling releases, or even when a build is done sometimes via the CI/CD pipeline. This depends on the project and the criticality of keeping the manifest updated.\
\
The most important use of manifest is trying to figure out if a particular security flaw affects one of the components shipped in your offering. This can only happen if the manifest file exists in the first place and secondly is kept up to date on a regular basis. Manifest files can be parsed semi-automatically or even automatically depending on the size of the manifest and the data can be used to track and fix security flaws in the offering.

## Software Bill of Materials
SBOMs were briefly discussed in the section on [Software Composition Analysis](../code-scanning/sca.md#SBOM). Essentially an SBOM is a manifest with a lot of bells and whistles! SBOMs list all the components used in an offering along with additional information which can be related to licenses, source from where those components are taken etc.\
\
Various supply chain attacks prompted President Biden to issue a [cybersecurity executive order (EO)](https://www.whitehouse.gov/briefing-room/presidential-actions/2021/05/12/executive-order-on-improving-the-nations-cybersecurity/)  detailing guidelines for how federal departments, agencies, and contractors doing business with the government must secure their software. Among the recommendations was a requirement for SBOMs, to ensure the safety and integrity of software applications used by the federal government.\
\
Let us briefly discuss the various formats used for SBOM

### Software Package Data Exchange (SPDX)
[SPDX, a Linux Foundation project,](https://spdx.dev/) was formed with the intent of creating a common data exchange format for information related to software packages for sharing and collection. SPDX supports the largest collection of file formats among the leading SBOM formats. These include RDFa, .xlsx, .spdx, .xml, .json, and .yaml. SPDX also aims to be a dynamic specification by being able to describe a set of software packages, files, or snippets. SPDX is the only SBOM format that has achieved International Organization for Standardization (ISO) [certification status.](https://spdx.github.io/spdx-spec/)\
\
SPDX documents can be composed of fields and data such as document creation information, package information, file information, snippet information, licensing information, relationships, and annotations.

### CycloneDX
[CycloneDX](https://cyclonedx.org/) is being led by longtime security community leader Open Web Application Security Project (OWASP). CycloneDX is a self-defined lightweight SBOM standard designed for use in application security contexts and supply chain component analysis.
\
CycloneDX supports referencing components, services and vulnerabilities in other systems and BOMs as well, in a nesting and hierarchical approach that aligns with the complexity of the modern software ecosystem when it comes to hardware, cloud and SaaS. 
\
 It allows for the identification of vulnerabilities through three fields, which are Common Platform Enumeration (CPE), SWID, and Package-URL (PURL). The CPE specification can be used for vulnerabilities in operating systems, applications and hardware devices. SWID is used for installed software and PURL can be used for software package metadata.

### Software Identification Tags (SWID)
According to NIST, “the SWID standard defines a lifecycle where a SWID tag is added to an endpoint as part of the software product’s installation process and deleted by the product’s uninstall process.” Standard indicators of a software product’s presence on a device through a consistent label with details on the product name and version.\
\
SWID tags aim to help enterprises create accurate software inventories by making it easier to discover, identify and contextualize software throughout the software lifecycle. The standard is part of the broader ISO IT asset management standard and is supported by NIST.

