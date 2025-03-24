This repo will contain our tool protocols, including a protocol for ESLint, as well as our general principals and practices. All protocols will have sections that is meant to help developers run tools, resolve easy issues and raise exceptions; these sections will be labeled For Developers.




# Microsoft's Security Development Lifecycle (SDL)

Visual presentation of our SDL:  https://www.figma.com/design/xPoUpar3TCKNEqlQ3FK2cE/Untitled?node-id=0-1&t=jpdsxuLxv5iafDrL-1

20 years ago, Microsoft introduced the [Security Development Lifecycle (SDL)](https://www.microsoft.com/en-us/securityengineering/sdl/practices) practices as a set of 10 principals that can be easily digest by all developers working at Microsoft. 

To understand the SDL, we must understand the steps involved in the development lifecycle and how each step can result in a compromised system.

This next excerpt is take directly from Microsoft's website:
"
Security risks (and the need to mitigate them) can occur at any point in the development lifecycle:

**1. Design** – ensure that the design doesn’t naturally allow attackers to easily gain unauthorized access to the workload, its data, or other business assets in the organization.

**2. Code** – ensure that writing (and re-use) of code doesn’t allow attackers to easily take control of the application to perform unauthorized actions that harm customers, employees, systems, data, or other business assets. Developers should also work in a secure environment that doesn’t allow attackers to do this without their knowledge.

**3. Build and Deploy** – ensure that the continuous integration and continuous deployment (CI/CD) processes don’t allow unauthorized users to alter the code and allow attackers to compromise it.

**4. Run** – ensure that environment running the code (cloud, servers, mobile devices, others) follows security best practices across people, process, and technology to avoid attackers compromising and abusing the workload. This includes the adoption of well-established best practices, security baseline configurations, and more.

**5. Zero Trust architecture and governance** – All of these stages should follow Zero Trust principles to assume breach (assume compromise), explicitly verify trust, and grant the least privilege required for each user account, machine/service identity, and application component.

"

## The 10 SDL Practices:

1. Establish security standards, metrics, and governance
2. Require use of proven security features, languages, and frameworks
3. Perform security design review and threat modeling
4. Define and use cryptography standards
5. Secure the software supply chain
6. Secure the engineering environment
7. Perform security testing
8. Ensure operational platform security
9. Implement security monitoring and response
10. Provide security training

## 1. Establish security standards, metrics, and governance

**1.1 Identify Required Standards**
- Technical standards:
	- [NIST Secure Development Framework](https://csrc.nist.gov/Projects/ssdf)
- Regulatory standards:
	- Laws 
	- Industry specific standards
**1.2 Define Security Requirements**
Use known threats (Such as [OWASP Top 10](https://owasp.org/www-project-top-ten/) or [SANS Top 25](https://www.sans.org/top25-software-errors/) or [Mitre's Top 25](https://cwe.mitre.org/top25/) ) to establish a minimum-security baseline. This baseline can be in the form of security compliance controls, which include processes as well as automated checks that must be adhered to at different steps in the development lifecycle. Compliance controls may integrate automated scanning tools into the development lifecycle.

Here are some modern SAST tools:
- [GitHub's Advanced Security (GHAS)](https://github.com/advanced-security), 
- [GitHub's Dependabot](https://docs.github.com/en/code-security/dependabot/dependabot-alerts/about-dependabot-alerts)
- `npm audit`, [ESLint](https://eslint.org/)/[TSLint](https://typescript-eslint.io/) for NodeJS
- [OWASP's Dependency Check](https://github.com/jeremylong/DependencyCheck)

**1.3 Define metrics and compliance reporting**
The *type* of security issue and its *severity* must be reported to the right individuals, at the right time during the development cycle. Depending, on their severity, these issues must be addressed and resolved within a timeframe, or before proceeding to the next step of development. These issues must be reported, tracked and resolved at the right time.

**1.4 Create a Security Exception Process**
Developers cannot be expected to achieve 100% compliance with the security policies, all the time. As such, security teams should allow for developers to formatlly request exceptions. 

The security team must define how to manage security policy exceptions. The security teams must review, approve/deny and track all exceptions to security policies, to their resolution or expiration. 

Depending on the severity of the exception, the right level of management should be involved in reviewing and deciding how to tackle the exception. The security team can advise on possible temporary solutions, while exploring permanent solutions that can be applied in the future. If no permanent solution exists, an exception can be integrated into the policy, as a form of resolution for hard-to-resolve exceptions. Deviations from policy must be justified and only exist temporarily.


## 2. Require use of proven security features, languages, & frameworks

Some fundamental aspects of authentication and authorization are too important to leave undefined.

**2.1 Identity**
Design zero-trust environments to inventory, monitor, maintain, and update all stored secrets. Encrypt all secrets and ensure no secrets are stored or transmitted in plaintext form. Secrets should only be decrypted for use under secure circumstances, by authenticated and authorized entities.

For the workload, each account type and component of the system should have only the *minimum necessary privileges* to perform the required operations. Provide control and management of sensitive accounts and grant access only as needed. Restrict and minimize the number of people in privileged roles who have access to secured information or resources. 

**2.2 AI safety and security**
There are established guidelines for integrating AI into the workspace, development lifecycle and software products.


**2.3 Data protection: Securing content used in apps**
Secure implementation and connection to databases and files can be defined using secure database & file access standards and compliance checks. To make data protection easier, data can be grouped by state, and addressed accordingly:
- **At Rest**: This includes all information storage objects, containers, and types that exist statically on physical media, whether magnetic or optical disk.
- **In Transit**: When data is being transferred between components, locations, or programs, it's in transit. Examples are transfer over the network, across a service bus (from on-premises to cloud and vice-versa, including hybrid connections such as ExpressRoute), or during an input/output process.
- **In Use**: When data is being processed, the specialized AMD & Intel chipset based Confidential compute VMs keep the data encrypted in memory using hardware managed keys.

A key management solution, such as Azure Key Vault, can be selected, otherwise somekind of tested framework must be used. Here are some best practices for key management:
- Grant access to users, groups, and applications at a specific scope. 
- Control what users have access to. The management plane, which can configure access controls, should not have access to the data plane, which is where the application can interpret data. These two planes of access control should work independently.
- Store certificates in your key vault. Your certificates are of high value. In the wrong hands, your application's security or the security of your data can be compromised.
- Key Recovery: Ensure that you can recover a deletion of key vaults or key vault objects. Enable the soft delete and purge protection features.

Here are some general data protection best practices:
- Secure Workstation: Use a secure management workstation to protect sensitive accounts, tasks, and data.
- Use encryption to help mitigate risks related to unauthorized data access.
			- Apply disk encryption to help safeguard your data at rest. 
			- All transactions occur via HTTPS.
- Lessen the need for trust Running workloads on the cloud requires trust. You give this trust to various providers enabling different components of your application.
			- *App software vendors:* Trust software by deploying on-premises, using open-source, or by building in-house application software.
			- *Hardware vendors:* Trust hardware by using on-premises hardware or in-house hardware.
			- *Infrastructure providers:* Trust cloud providers or manage your own on-premises data centers.
- You want to control and secure email, documents, and sensitive data that you share outside your company and to third parties.

**2.4 Logging and telemetry**
Environment level and application level telemetry can be used to monitor system behavior, performance and security. Logs must track potentially malicious events, and not leak any secrets.

**2.5 Use approved technology**
Define and publish a list of approved technologies and their associated security checks.

Developers must strive to use the *latest versions* of approved technologies. Security teams must perform compliance checks on approved technologies, using approved security analysis tools that are well maintained. For each technology, the security teams must define a SecDevOps policy that must be adhered to by the developers using that technology throughout the development lifecycle.

## 3. Perform secure design review and threat modeling 
*Threat modeling* and *security design reviews* of threat models identify potential security threats so that designers can mitigate them early in the development process. One must shift the focus from **how products should work** to **how products might be abused** from a security and privacy-harm standpoint. Threat modeling tools such as OWASP's Thread Dragon or Microsoft's Threat Modeling Tool can be used.

**3.1 Identify use cases, scenarios, and assets** 
Each system has intended business functions, user roles and external systems that connect to it. These can be described textually to begin with. An inventory of assets and data stored/processed are needed. 

Examples of tangible assets:

			- Personal photos and contacts stored on a smartphone
			- Compute resources in a cloud environment
			- Software supply chain integrity in a development environment
			- Medical imaging and diagnostic data
			- Financial accounting data
			- Biometric authentication data
			- Proprietary formulae and manufacturing processes
			- Military and government secrets
			- Machine learning models and training data
			- Credit card data
			
Examples of intangible assets:

			- Customer trust
			- User privacy
			- System confidentiality, integrity & availability
			
**3.2 Create an architecture overview**
Flowcharts of intended scenarios, Data Flow Diagrams (DFDs), UML sequence diagrams and network architecture diagrams must be created and analyzed from a security perspective. It is a recommended practice to label the following in your DFD arrows:

				- Data types (business function)
				- Data transfer protocols
				
A trust boundary is a logical construct used to demarcate areas of the system with different levels of trust such as:

				- Public Internet vs. private network
				- Service APIs
				- Separate operating system processes
				- Virtual machines
				- User vs. kernel memory space
				- Containers
Calls & data that crosses a trust boundary often need to be authenticated and authorized. These can be illustrated with a dashed red line.

![2bef3cbc3667bca7403cb7e1dff81931.png](:/ed2384f1f7ec43dba6dde9917ae4d3b8)

**3.3  Identify the threats**
If possible, hold 2 hour meetings and invite people of varied backgrounds, to go through the prepared materials, such as DFDs, inventory assets, and identify threats and mitigations. Decompose the system into smaller chunks and threat model them separately. 

STRIDE is a common methodology for enumerating potential security threats that fall into these categories:
			- *Spoofing*: Making false identity claims.
			- *Tampering*: Unauthorized data modification.
			- *Repudiation*: Performing actions and then denying that you did.
			- *Information Disclosure*: Leaking sensitive data to unauthorized parties.
			- *Denial of Service*: Crashing or overloading a system to impact its availability.
			- *Elevation of Privilege*: Manipulating a system to gain unauthorized privileges.
During threat modeling excercises, STRIDE can be used to systematically think about potential threats that may applly to each element in a diagram or inventory list. Other threat lists can also be used, including Mitre's or OWASP's or SANS' lists to top vulnerabilities for different technologies, including mobile apps, web apps, IoT, etc...

[LINDDUN](https://linddun.org/) is a privacy-centric threat-modeling methodology that provides both threats and potential mitigations.

Threats should be recorded, alongside their mitigations. Well-written threats clearly describe:

			- The threat actor who exploits the vulnerability
			- Any preconditions required for exploitation
			- What the threat actor does
			- The consequences for affected assets and users
			
			
**3.4 Identify and track mitigations**

Think in terms of layered defenses. Assume that other layers’ security controls will be bypassed or disabled (Assume Breach policy). If one layer is compromised, it should not result in the entire system being compromised and unrecoverable. Recommended Secure Design Practices that can help manage threats:

			- Design and Threat Model as a Team
			- Prefer Platform Security to Custom Code
			- Secure Configuration is the Default
			- Never Trust Data from the Client
			- Assume Breach
			- Enforce Least Privilege
			- Minimize Blast Radius
			- Minimize Attack Surface
			- Consider Abuse Cases
			- Monitor and Alert on Security Events

**3.5 Communicate threat models to key stakeholders**
Threat modeling is not complete until you create work items to track your threat findings and the related development and testing tasks to audit your attempts at mitigating them. A threat model provides the seeds for a good security test plan.



## 4. Define and use cryptography standards
**4.1 Encrypt data in transit and at rest**
Ensure data is encrypted at rest and in transit, including security-sensitive information and management and control data. For encryption in transit, use only strong versions of TLS. For data at rest, it is important to consider both the actual encryption solution of data on disk & in RAM, but also the logical intregrity to avoid compromise through application logic.

**4.2 Post-Quantum Cryptography (PQC)**
We recommend prioritizing symmetric encryption where applicable and subsequently adopting post-quantum cryptography (PQC) for asymmetric encryption once standardized and approved by relevant setting bodies and governments, as recommended by cybersecurity agencies globally. Microsoft has a [list](https://www.microsoft.com/en-us/research/project/post-quantum-cryptography/) of Post Quantum Cryptography resources.


**4.3 Cryptographic agility**
Cryptographic libraries, algorithms and mechanisms need to be designed into a system with consideration for potential updates or replacements of those cryptographic standards, in case they become outdated or shown to be vulnerable.

**4.4 Encryption key and certificate management and rotation**
Keys and certificates have a limited lifespan, and it is necessary to define mechanisms to manage the lifecycle of keys and certificates, including mechanisms to both create new keys and certificates once the previous ones are near expiration and to rapidly rotate in the event of a security incident, such as unintended access. Anyone who can access an encryption key or private key can access the encrypted data, and therefore it's necessary to control who (whether a person or a service) has access and provide a clear audit log of that access.

## Secure the software supply chain

Software projects are often built using both comercial and open source software (OSS) dependencies and components. 

**5.1 Establish a secure open source software (OSS) ingestion process**
[Github](https://docs.github.com/code-security/supply-chain-security/end-to-end-supply-chain/end-to-end-supply-chain-overview) and [Microsoft](https://www.microsoft.com/securityengineering/opensource) offer security assurance and risk reduction processes that are focused on securing how developers consume OSS.

**5.2 Understand dependencies in your environment**
 [Dependency reviews](https://docs.github.com/code-security/supply-chain-security/understanding-your-software-supply-chain/about-dependency-review) and [dependency graphs](https://docs.github.com/code-security/supply-chain-security/understanding-your-software-supply-chain/about-the-dependency-graph) can be mapped out to aid the security team inventory all open source dependencies. Insecure dependencies can be automatically detected using SAST tools (see a short list of SAST tools in 1.2). Insecure dependencies should be rated, tracked and updated as soon as possible.
 
**5.3 Produce software bill of materials (SBOM)** 
An SBOM is a machine-readable document that lists all of the components, including open source components used to create a product and helps better inventory software and therefore helps organizations to understand license and vulnerability risk. SBOMs also contain package and file checksums to help validate hashes, which is useful when signatures aren’t provided by other means. A SBOM can be automatically created using a tool such as [Microsoft's SBOM Tool](https://github.com/microsoft/sbom-tool).

**5.4 Signing and attesting artifacts**
Software integrity requires signing and validating the software's components. These components include tools that were integrated into your lifecycle. A dependency's certificate can be validated and provenance proven, before the project's build. Siging artifacts with SBOMs, and attesting them before a build, is crucial to avoiding man-in-the-middle attacks that rely on build-time attack, such as CCleaner/SolarWinds.

### Microsoft's Secure Software Supply Chain Framework Practices (S2C2F)

**Ingest**

*Level 1, Level 2* - Saving a local copy of the OSS (Open Source Software) artifact helps mitigate against availability risks of the upstream package managers, and also protects against left-pad incidents – enabling developers to continue to build even if upstream resources are unavailable. This can be done by adopting an integrated package caching solution into your CI/CD infrastructure.  
*Level 3* - Mirroring external source code repositories to an internal location is useful for many reasons, including Business Continuity and Disaster Recovery (BCDR) purposes, proactive security scans to look for backdoors and zero-day vulnerabilities, and contributing fixes back upstream.

**Scan for vulnerabilities and malware**

*Level 1* - It is recommended to scan for both known vulnerabilities (i.e. CVEs, GitHub Advisories, etc.) and licenses. For known vulnerabilities, choosing a tool that gets vulnerabilities from more places than just CVEs is important to ensure that you are being informed from across multiple vulnerability sources. 

*Level 2* - For security purposes, no organization should take a dependency on software that is no longer receiving updates, so it is important to scan OSS to determine if it is end of life.

*Level 3* - There has been a rise in malicious OSS packages over the years. It is critical that OSS be scanned for malware prior to consumption. In addition, without doing proactive security analysis to look for zero-day vulnerabilities, there would be entire threat categories that would go unmitigated, such as back-doors.

**Inventory**

*Level 1, Level 2* - Establishing an inventory of all developer OSS dependencies is critical when responding to an incident. If a malicious component ever gets ingested, it needs to be deleted from the developer’s desktop, the package caching solution, and the software/service that in production that consumed the package. Knowing which projects are using which OSS components and their versions across your enterprise is vital toward supporting rapid Incident Response.

**Update**

*Level 1, Level 2* - Using tools that improve your mean time to remediate (MTTR) OSS vulnerabilities is required.

**Audit provenance and integrity**

*Level 3, Level 4* - Verify the provenance of all OSS components to ensure they come through the official supply chain, and hash validate each component to ensure that no man-in-the-middle attack altered their integrity since they had been consumed from the public package manager.

**Enforce OSS consumption meets security policy**

*Level 2*- Securing the configuration of how build pipelines consume OSS components is important to ensure there is determinism to protect against race conditions and dependency confusion threats.

*Level 3* - Enforcing teams to only consume packages from a curated feed enhances the trust of your OSS. Curated feeds enable you to scan for malware, validate metadata about the component, and enforce an allow/deny list; these enforce secure consumption of OSS packages within your organization overall. 

**Fix it + Upstream**

*Level 4* - In extreme cases, when a newly discovered zero-day vulnerability is so severe, you should be prepared to deploy a private fix as a temporary risk reduction activity while you confidentially coordinate with the upstream maintainer to issue a public fix.
