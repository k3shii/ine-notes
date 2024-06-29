---
description: '-- credit by syselement'
---

# Auditing Fundamentals

## Cybersecurity Basics <a href="#cybersecurity-basics" id="cybersecurity-basics"></a>

🗒️ **Cybersecurity**, or **I**nformation **T**echnology Security, _is the process of protecting systems, networks, programs, devices and data by preventing, detecting and responding to digital attacks_. It aims to lower the risk of cyberattack and protect against the unauthorized and improper use of the involved technology.

**Protected information can be:**

* **PII** (**P**ersonally **I**dentifiable **I**nformation)
* Healthcare Information
* Financial Data
* Intellectual Property
* Business Secrets
* Business Operations

**Data must be secured from:**

* Criminals
* Competitors
* Insider Threats
* Malicious Actors

***

### CIA Triad <a href="#cia-triad" id="cia-triad"></a>

🗒️ The **CIA** Triad (**C**onfidentiality, **I**ntegrity and **A**vailability) _is a guiding model used to describe information security_, also useful for managing the products and data of research.

Policies and security controls that reduce threats to these three vital components are a part of a comprehensive _information security plan_ and are balanced to meet business needs.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-13a24a0974ee6a83fa7506e283c4a631c8e33f5c%252FCIA-TRIAD.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=92c169b3&#x26;sv=1" alt=""><figcaption><p>InfoSec CIA Triad</p></figcaption></figure>

<table><thead><tr><th width="186">CIA Triad</th><th>Description</th></tr></thead><tbody><tr><td><strong>Confidentiality</strong></td><td><em>Protecting information from unauthorized access</em>, allowing only authorized users to access certain data</td></tr><tr><td><strong>Integrity</strong></td><td><em>Data is trustworthy, complete, accurate and have not been accidentally altered or modified by an unauthorized user</em>. Data can be retrieved in the future without changes</td></tr><tr><td><strong>Availability</strong></td><td><em>Data is accessible when needed</em></td></tr></tbody></table>

### Defense in Depth <a href="#defense-in-depth" id="defense-in-depth"></a>

🗒️ The **Defense-in-Depth** is _a strategy that uses multiple layers of security protection_ and helps reducing vulnerabilities, containing threats and mitigating risks.

Depending on _what_ and _how_ an organization need to secure, the strategy have to be shaped, considering a risk management plan.

🗒️ **Risk Management** is _the process of identifying, assessing and controlling financial, legal, strategic and security risks to an organization’s **capital** and earnings_. It plays the ultimate factor in an organization.

***

## Compliance <a href="#compliance" id="compliance"></a>

🗒️ **Cybersecurity Compliance** _involves meeting various controls to protect the confidentiality, integrity, and availability of data_.

Compliance requirements (usually established by a regulatory authority, law or industry group) typically involve using an array of specific organizational processes and technologies to safeguard data. Controls come from a variety of Cybersecurity frameworks and regulations, such as:

* [PCI DSS](https://www.pcisecuritystandards.org/) (Payment Card Industry Data Security Standard )
  * mandated by card brands, created to increase controls around cardholder data
  * reduce card fraud
* [GDPR](https://gdpr.eu/) (General Data Protection Regulation)
  * Data protection and privacy law in the EU (European Union) and EEA (European Economic Area)
* [HIPAA](https://www.hhs.gov/hipaa/index.html) (Health Insurance Portability and Accountability Act)
  * United States regulations for the use and disclosure of **PHI** (**P**rotected **H**ealth **I**nformation)
  * Administrative, physical, technical safeguards
* [CPPA](https://leginfo.legislature.ca.gov/faces/codes\_displayText.xhtml?lawCode=CIV\&division=3.\&title=1.81.5.\&part=4.\&chapter=\&article=) (California Consumer Privacy Act.)
  * enhance privacy rights and consumer protection for California (USA) residents

> 📖 [Cybersecurity Compliance: A Comprehensive Guide - Celerium](https://www.celerium.com/cyber-security-compliance-a-comprehensive-guide)

***

## Frameworks and Maturity <a href="#frameworks-and-maturity" id="frameworks-and-maturity"></a>

🗒️ [Cybersecurity **Frameworks**](https://www.celerium.com/cybersecurity-frameworks-a-comprehensive-guide) are _sets of controls that represents a fully functional cybersecurity program when met_.

* [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework) (National Institute of Standards and Technology)
  * Identify, Protect, Detect, Respond, Recover - best practices
  * Applies to any organization
  * [NIST 800-53](https://csrc.nist.gov/Projects/risk-management/sp800-53-controls/release-search) disegned to apply to U.S. Federal Government agencies.
* [ISO/IEC 27001](https://www.iso.org/isoiec-27001-information-security.html) (International Organization for Standardization and the International Electrotechnical Commission)
  * Information security management systems - Requirements
  * [ISO/IEC 27002](https://www.iso.org/standard/75652.html) - Code of practice for information security controls
* [COBIT by ISACA](https://www.isaca.org/resources/cobit)
  * business and generic IT management focused
* [CIS](https://www.cisecurity.org/) (Center for Information Security)
  * 18 CIS Controls for mitigate the most known cyber attacks
* [CMCC](https://dodcio.defense.gov/CMMC/) (Cybersecurity Maturity Model Certification)
  * Simplifies compliance by allowing self-assessment for some requirements
  * Applies priorities for protecting Department of Defense (DoD) information
  * Reinforces cooperation between the DoD and industry in addressing evolving cyber threats
* [ASD Essential 8](https://www.upguard.com/blog/essential-eight) (Australian Signals Directorate)

***

## Auditing <a href="#auditing" id="auditing"></a>

🗒️ A **security Audit** is _an extensive evaluation of an organization’s information system_, an assessment that measures the information system’s security against an audit checklist of industry best practices, governmental legislation or externally defined standards.

An audit is necessary for a compliance plan.

* _Interviews_ on security awareness, training and practices - CISO, system administrators, employees
* _Review Paperworks_ - how records, logs are kept
* _Assessments_ - using automated tools to find compliance software and devices, vulnerability assessments
* As an auditor or pentester, _good notes_ are useful for the information gathering and the final report
* _Mind maps_ - useful and understandable by the customer
* _Reports_ - an important part that has value to a company as a result of a penetration test

***

## SCAP <a href="#scap" id="scap"></a>

🗒️ The [**SCAP**](https://public.cyber.mil/stigs/scap/) (**S**ecurity **C**ontent **A**utomation **P**rotocol) is _a specification for expressing and manipulating security data in standardized ways. It uses specific standards to help organizations automate continuous monitoring, vulnerability management and security policy compliance evaluation reporting._

DISA [**STIG Viewer**](https://public.cyber.mil/stigs/srg-stig-tools/) is a GUI java based program used to open content and generate checklists for managing the security settings on a system or network.

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-044835bef8324abca7e4c204293a424f31405273%252Fimage-20230227114909530.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=aaa25f6e&#x26;sv=1" alt=""><figcaption><p>STIG Viewer</p></figcaption></figure>

***

## Asset Management <a href="#asset-management" id="asset-management"></a>

### [nmap](https://nmap.org/) <a href="#nmap" id="nmap"></a>

* General audit internal devices with an internal network scan using **`nmap`** tool to find active services and open ports

```
nmap 192.168.50.0/24

nmap -A 192.168.50.1,5,10
# Aggressive scan (Syn, Service, O.S, Default Scripts Scans) on specific IPs
```

### [Nessus](https://www.tenable.com/products/nessus/nessus-essentials) <a href="#nessus" id="nessus"></a>

**`nessus`** is a network vulnerability scanning tool, hosted locally and used for Vulnerability Assessment.

* `Nessus Essentials` is the free version of the V.A. tool by [Tenable](https://www.tenable.com/blog/nessus-home-is-now-nessus-essentials).
  * Limited to 16 IPs for V.A.
  * Build in insights with latest vulnerabilities
  * No usage time limit
  * Report export as PDF, HTML, CSV formats
* [Nessus Documentation](https://docs.tenable.com/nessus/Content/GettingStarted.htm)

<figure><img src="https://blog.syselement.com/~gitbook/image?url=https%3A%2F%2F1996978447-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FlhjuckuLbvBn36EoFL7P%252Fuploads%252Fgit-blob-753feea5b3802395e3999f98a8daf853ec8651da%252Fimage-20230227120920771.png%3Falt%3Dmedia&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=b492761b&#x26;sv=1" alt=""><figcaption><p>Nessus</p></figcaption></figure>
