# Component Manifest / Software Bill of Materials

## Introduction
Complex open source offerings are often made of multiple open source components. These need to be kept track of for the purpose of tracking security issues, applying fixes when they are available, etc. Depending on the complexity of the project and sometimes customer requirements there are various ways of doing this. In this section we describe a simple approach called a manifest file. If your requirements are more complex, for example if you have more things to keep track of including license information, then there is something called SBOM or Software Bill Of Materials which we will try to describe in this page also.

## What is a manifest
[A manifest file](https://en.wikipedia.org/wiki/Manifest_file) in computing is a file containing metadata for a group of accompanying files that are part of a set or coherent unit. For example, the files of a computer program may have a manifest describing the name, version number, license and the constituent files of the program.\
\
As described above, the primary use of a manifest is to enumerate files or components present in an offering. Other useful information like version numbers etc can also be included for tracking purpose.\
\
Manifest files can also be cryptographically signed when they are provided to third parties for verification or even to customers, to make sure their integrity is preserved. They can be human readable text files, or can be XML, JSON or whatever format suits the underlying generating and parsing mechanism.\

## Generating/Managing/Using a manifest
Manifest file generation is and should be an automated process. It does not really make sense to write a manifest manually. This is eliminate human errors in the process, including the fact that the person generating the manifest by hand, could leave the organization one day, and no one else would really have a clue about how the process really works.\
\
On similar lines, less a manifest file is kept to upto date, it really have very little meaning. Triggers to update a manifest file are whenever a new release of the software or offering is done, or on a timely basis when we are dealing with rolling relases, or even when a build is done sometimes via the CI/CD pipeline. This really depends on the project and the criticality of keeping the manifest updated.\
\
The most important use of manifest is trying to figure out if a particular security flaws affects one of the components shipped in your offering. This can only happen if the manifest file exists in the first place and secondly is kept upto date on a regular basis. Manifest files can parsed semi-automatically or even automatically depending on the size of the manifest and the data can used to track and fix security flaws in the offering.\

## Software Bill of Materials


