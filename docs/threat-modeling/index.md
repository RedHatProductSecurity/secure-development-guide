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

### Reduced cost by finding design flaws early and before release

### Reduced exposure to weakness, other methods may not uncover

## Goal

## When to do threat modeling

## How to do threat modeling

## What a threat model will not provide

## Defining scope

## Expected output

## Closing words

