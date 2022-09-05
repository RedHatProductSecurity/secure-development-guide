- [Threat Modeling](#threat-modeling)
  - [Why threat modeling? ](#why-threat-modeling-)
  - [Benefits](#benefits)
    - [More secure code](#more-secure-code)
    - [Reduced cost by finding design flaws early and before release](#reduced-cost-by-finding-design-flaws-early-and-before-release)
    - [Reduced exposure to weakness, other methods may not uncover](#reduced-exposure-to-weakness-other-methods-may-not-uncover)
  - [Goal](#goal)
  - [When to do threat modeling](#when-to-do-threat-modeling)
  - [How to do threat modeling](#how-to-do-threat-modeling)
  - [What a threat model will not provide](#what-a-threat-model-will-not-provide)
  - [Defining scope](#defining-scope)
  - [Expected output](#expected-output)
  - [Closing words](#closing-words)

# Threat Modeling


Formally, threat modeling, outlined by OWASP, is a process by which potential threats are identified and rated for severity and possible mitigations are discussed. Less formally, threat modeling happens when you think about each decision that you make in the system you're creating and extrapolate how these may affect its security either immediately or in the future. 

It is important to stress that threat modeling is a process, not a tool. While tools can help the process be more efficient (e.g., by providing visualization, tracking changes over time, or identifying changes to software that would more likely affect its threat model), tools by themselves cannot currently take the place of humans reasoning about how other humans would attack a system. 

Threat modeling is most effective when multiple stakeholders come together to look at a system from different angles: developers, architects, Offering engineers, designers, and end-users, along with security specialists. The discussion can be as simple as walking through how the system is used, how it is supposed to work, and how it actually works. Security specialists ask questions to get a better understanding of the security controls in place, and everyone leaves with a better understanding of the risks that affect the system.

While several approaches can be used to perform the assessment, We would recommend a design-centric threat modeling approach that focuses on areas where data passes over trust boundaries or virtual boundaries that separate groups of components with higher, lower, or different trust. 

## Why threat modeling? 

The express aim of threat modeling is to identify design issues, potential security weaknesses or arrive at a set of security needs that must be built. These are sometimes referred to as "non-functional requirements." Once identified, the security requirements when implemented will bring a system or set of systems to the intended security posture. Identifying likely threats and the probable consequences of successful attacks is the method of investigation to identify an appropriate set of defenses. It is an industry best practice to validate the defenses that were derived from the threat model and this validation can be achieved with the likes of Penetration Testing etc. 

While some threat modeling methods focus on identifying threats and security issues, other methods also perform an assessment of the resulting risks by rating the consequences (impacts) and the likelihood of threats. Such methods are also called Threat and Risk Analysis or Assessment (see, for example, ISO 27005, NIST SP 800-30). Such a rating can be used to prioritize defenses.

## Benefits

### More secure code

Threat modeling done properly and early establishes a foundation for building a secure code base on a secure design. Designing an project with security in mind throughout the process reduces the chances of finding vulnerabilities and gives teams more confidence to concentrate on the features being delivered.

### Reduced cost by finding design flaws early and before release

Threat modeling normally occurs during the project design phase and allows for early identification of security risks. If vulnerabilities are found, early detection saves valuable time and monetary resources when mitigations are required. The cost of closing a security risk increases dramatically as a project nears release, and exponentially once it has been released to the public.

### Reduced exposure to weakness, other methods may not uncover

Design and development teams use many different methods to develop projects. Code and test plan reviews help engineers evaluate an Offerings’s effectiveness, but may not divulge such security concerns as the need for input validation or cryptography. Threat modeling examines the relationships that exist between a component’s atomic elements, analyzes how data flows between them, and exposes potentially exploitable weak points.


## Goal

- Detect problems early in the software development lifecycle — even before coding begins.
- Propose realistic mitigations to the identified weaknesses before a release, preventing costly post-deployment re-coding.
- Spot design flaws that traditional testing methods and code reviews may overlook.
- Think about threats and security issues unique to the Offering.
- Brainstorm as a group to evaluate new forms of attack.
- Help target testing and code review.
- Identify gaps in Red Hat’s Product Security and Information Security requirements.
- Highlight assets, threat agents, and controls to deduce components that attackers will target.


## When to do threat modeling

Ideally, threat modeling is performed as soon as possible. There is a timing element to threat modeling that we highly recommend understanding. No matter how late in the development process threat modeling is performed, it is always critical to understand weaknesses in a design's defences. The cost of addressing issues will generally increase when we uncover design misses and missed security requirements later, or worse, at the end of the development process. It is much more useful to begin the process of identifying potential attacks and their treatments while identifying other system requirements.


## How to do threat modeling


## What a threat model will not provide
- Business Risk Assessment
- A compliance check will all Security Standards
- External Audit
- Penetration Testing
- Architecture Security Analysis with respect to code reviews
- The threat model report does not constitute a guarantee that assets are secure from all threats

## Defining scope
Scope: One Threat Model or Many?

Depending on the size of a particular project, one threat model can be completed or a number of threat models per project, whereby the project is broken down by features, components or Engineering teams. Threat modeling by individual feature or component instead of creating a single threat model for the entire workload has a number of benefits:

- Smaller chunks of work allow for more granular tracking of progress, which aligns well with Engineering teams that are following agile-style delivery and gives leadership a constant view of progress.
- The resulting threat models tend to be more detailed, which results in more findings being identified.
- The threat model potentially can be reused as a dependency for other workload features that use the same components.
- Considering threat mitigations at the component level means that a single threat may have multiple mitigations at the overall workload level, resulting in an improved resilience against those threats.
- Issues with a single threat model (e.g. a critical threat which is not yet mitigated) do not become launch blockers for the entire workload, but rather just for the individual feature.

## Expected output

## Closing words

