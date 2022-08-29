# What is threat modeling?


Formally, threat modeling, outlined by OWASP, is a process by which potential threats are identified and rated for severity and possible mitigations are discussed. Less formally, threat modeling happens when you think about each decision that you make in the system you're creating and extrapolate how these may affect its security either immediately or in the future. 

It is important to stress that threat modeling is a process, not a tool. While tools can help the process be more efficient (e.g., by providing visualization, tracking changes over time, or identifying changes to software that would more likely affect its threat model), tools by themselves cannot currently take the place of humans reasoning about how other humans would attack a system. 

Threat modeling is most effective when multiple stakeholders come together to look at a system from different angles: developers, architects, Offering engineers, designers, and end-users, along with security specialists. The discussion can be as simple as walking through how the system is used, how it is supposed to work, and how it actually works. Security specialists ask questions to get a better understanding of the security controls in place, and everyone leaves with a better understanding of the risks that affect the system.

While several approaches can be used to perform the assessment, We would recommend a design-centric threat modeling approach that focuses on areas where data passes over trust boundaries or virtual boundaries that separate groups of components with higher, lower, or different trust. 

