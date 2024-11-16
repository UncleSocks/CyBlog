+++
title = "Connecting Information Security and the U.S. Healthcare: An Information Security Professional's Detailed Guide and Review to the Health Insurance Portability and Accountability Act (HIPAA) of 1996, Title II - Administrative Simplification"
date = 2024-11-02T22:30:52+08:00
author = "Tyrone Kevin Ilisan"
draft = false
+++

Similar to my previous post regarding RFC 6598 and Network Address Translation (NAT), when I was still studying for my master's degree in Information Security (InfoSec), I was tasked to present the Health Insurance Portability and Accountability Act (HIPAA) of 1996 and how it relates to InfoSec. Being in this industry we are often tasked with safeguarding sensitive personal information, and this may include protected health information (PHI). HIPAA of 1996 establishes a standard for protecting sensitive health information of patients from unauthorized disclosures [`1`]. However, understanding HIPAA of 1996 and how to comply with it as an InfoSec professional can be daunting due to its scope and complexity. In this blog post, I will try to make HIPAA of 1996 digestible and highlight available tools and resources to assist professionals in their journey to compliance. I hope this blog post can become a single-page reference or guide in understanding HIPAA in the context of InfoSec.

# Overview of the HIPAA of 1996

The HIPAA of 1996 (hereinafter known as "HIPAA") was signed or *enacted* into Law by President Bill Clinton on August 21, 1996 [`2`]. The act became **Public Law 104-191**, which means it was passed during the 104th Congress with 191 as its numerical designation [`3`]. As previously stated, HIPAA establishes a standard for protecting sensitive health information of patients from unauthorized disclosures [`1`].

Technically, according to the long title, HIPAA is:

```
“An act to amend the Internal Revenue Code of 1986 to improve portability and continuity of health insurance coverage in the group and individual markets, to combat waste, fraud, and abuse in health insurance and health care delivery, to promote the use of medical savings accounts, to improve access to long-term care services and coverage, to simplify the administration of health insurance, and for other purposes.”
``` 

At a glance, the long title does not specify anything about the security or privacy of PHI as the initial purposes of HIPAA were **Portability of Health Coverage** and **Administrative Simplification**. Over time, the act was continuously updated, including the addition of security-related rules.

Under HIPAA, Title I ensures health insurance portability by providing continuous health insurance coverage for employees when in between or losing jobs. Additionally, Title 1 protects employees with pre-existing conditions from discrimination [`4`]. This blog post will not dive into Title I as it does not pertain to the security and privacy of PHI. However, the Centers for Medicare & Medicaid Services (CMS) has a great resource outlining the specific circumstances to become eligible for HIPAA's health coverage portability, including examples [`5`].

The Administrative Simplification in a nutshell ensures consitency in electronic communication across different U.S. healthcare systems by mandating standards, code sets, and identifiers, and thus improving efficiency [`7`]. Administrative Simplification and the rest of Title II (subtle foreshadowing) are discussed further in this post.

## Brief Timeline and History of HIPAA

Changes have been made to HIPAA since its enactment in 1996, with the latest Notice of Proposed Rulemaking (NPRM) this 2024, strengthening the reproductive healthcare privacy under the HIPAA Privacy Rule [`8`]. An NPRM is the official document announcing and explaining the federal agency's plan to address a problem or accomplish a goal, while providing the public an opportunity to submit comments; NPRMs are published in the Federal Register [`9`].

Fig. 1 below outlines the important historical HIPAA events for InfoSec professionals, including the introduction of different security and privacy rules: Security Rule, Privacy Rule, Enforcement Rule, and Breach Notification Rule

![HIPAA Hisotical Timeline](../../hipaa/hipaa-history.png)
*Fig. 1. HIPAA History Timeline*

Following the timeline three (3) NPRMs were added to HIPAA, shown in Fig. 2 below, that are relevant to InfoSec at the time of writing [`10`]:

![HIPAA NPRMs](../../hipaa/hipaa-nprms.png)
*Fig. 2. HIPAA NPRMs*

On April 22, 2024, the Office for Civil Rights (OCR), issued the Final Rule for the privacy of reproductive health, entitled **HIPAA Privacy Rule to Support Reproductive Health Care Privacy** [`11`].


## The Titles of HIPAA

HIPAA has five (5) Titles:
- Title I - Health Care Access, Portability, and Renewability
- Title II - Preventing Health Care Fraud and Abuse; Administrative Simplification; Medical Liability Reform
- Title III - Tax-related Provisions
- Title IV - Application and Enforcement of Group Health Plan Requirements
- Title V - Revenue Offsets

As InfoSec professionals, we will primarily concern ourselves with Title II. This title includes the Administrative Simplication, Security, and Privacy Rules [`12`]. The rest of the blog post basically deep dives into Title II in the context of InfoSec.

### Code of Federal Regulations Governing HIPAA Title II 

**[`IMPORTANT!`]**: Throughout this post, I will be referencing the **Title 45 of the Code of Federal Regulations (CFR)** or **45 CFR**. Title II of HIPAA is primarily implemented under **45 CFR Parts 160, 162, and 164**, specifically within **Subchapter C - Administrative Data Standards and Related Requirements** [`12`]:

- Part 160 - General Administrative Requirements
- Part 162 - Administrative Requirements (Electronic Transactions and Code Sets)
- Part 164 - Security and Privacy

A good resource to navigate through the different parts of 45 CFR Subchapter C is using [`Cornell Law School's Administrative Data Standards and Related Requirements`](https://www.law.cornell.edu/cfr/text/45/chapter-A/subchapter-C) webpage.

# Entities Required to Comply with HIPAA

CMS defines Administrative Simplification as the set of HIPAA requirements that govern how **covered entities** conduct electronic, administrative transactions and set standards for transmitting PHI [`13`]. The definition sheds some light on how security and privacy can play a role in HIPAA.

## Covered Entities

Covered Entities are simply individuals and organizations that need to comply with HIPAA, which includes **health plans**, **health care providers**, and **health care clearinghouses** [`14`]. 

A [`Covered Entity Decision Tool`](https://www.cms.gov/Regulations-and-Guidance/Administrative-Simplification/HIPAA-ACA/Downloads/CoveredEntitiesChart20160617.pdf), as shown in Fig. 4 below, is provided by the CMS to help indivuals and organizations determined whether they are a covered entity [`15`]. 

![CMS' Covered Entity Decision Tool](../../hipaa/covered-entity-decision-tool.PNG)
*Fig. 3. CMS Covered Entity Decision Tool*

### Health Plans

Health Plans are individual or group plans that provide or pay the cost of medical care, including [`2`]:
- Health insurance companies
- HMOs or health maintenance oraganizations
- Employer-sponsored health plans
- Government programs that pay for health care (i.e., Medicare, Medicaid, and military and veterans' health programs)

**Exception**: Health plans with *less than 50 participants* and *administered solely by the employerer* is not considered a covered entity.

### Health Care Providers

Health Care Providers any person or organization that provides medical or health-related services, and furnishes, bills, or is paid for health care in the normal course of business [`16`]. A health care provider becomes a covered entity when it electronically transmits HIPAA Transactions and Code Rule Sets [`14`]. These providers include, but are not limited to:
- Doctors
- Clinics
- Psychologists
- Dentists
- Chiropractors
- Nursing homes
- Pharmacies

### Health Care Clearninghouses

Health Care Clearinghouses processes health information received from one entity in a nonstandard format to a standard transaction, and vice versa [`17`]. 

## Business Associates

Business Associates (BA) are essentially third-party service providers of covered entities that use or disclose identifiable PHI as part of their function, service, or activity [`18`]. Under the Security Rule and Privacy Rule of HIPAA, covered entities are required to obtain written **Business Associate Agreements** (BAA) or contracts that provides satisfactory assurance that the BA will appropriately safeguard the PHI. This post will expand on the BAA under the Security Rule and Privacy Rule sections.

# A Deep Dive Into the HIPAA Administrative Simplification Rules

Originally, HHS initiated five rules to enforce Administrative Simplification: Transaction and Code Sets Rule, Privacy Rule, Security Rule, Unique Identifiers Rule, and Enforcement Rule [`19`]. In 2009, the Breach Notification Rule was added as part of the **Health Information Technology for Economic and Clinical Health (HITECH) Act**. Thus, before delving into the security and privacy aspects of HIPAA, this post will first introduce **45 CFR Part 162**, also known as **Administrative Requirements** [`20`]. The Administrative Requirements include the Transaction and Code Sets Rule, and Unique Identifiers Rule. 

## Administrative Requirements
The CMS, on behalf of the HHS, is responsible for administering and enforcing the Administrative Requirements of HIPAA but their authority does not extend to the HIPAA Security Rule and Privacy Rule [`21`]. Specifically, the CMS investigates HIPAA transaction complaints and audits:
- Transactions (Standards)
- Code Sets
- Unique Identifiers

### Transactions (Standards)

In the long title of HIPAA, two of its purposes is to combat waste and to simplify the administration of health insurance. Thus, HIPAA required HHS to adopt standard transactions for the electronic exchange of health care data, resulting in the use of consistent language, format, and code sets (discussed in the next subchapter) [`22, 23`]. A **Transaction** is an electronic exchange of information between entities to carry out financial or administrative activities relating to health care [`22`]. 

Each HIPAA adopted transaction uses a specific **ASC X12N - Insurance Version 5010** standard for the electronic exchange of health care data (Electronic Data Interchange or EDI), as shown in TABLE 1 below [`23`]:

|  Transaction  |  Standard  |
|----------|----------|
| Health Care Claim | X12N 837 |
| Health Care Claim Payment Advice | X12N 835 |
| Health Care Claim Status Request/Notification | X12N 276/277 |
| Eligibility, Coverage, or Benefit Inquiry/Information | X12N 270/271 |
| Benefit Enrollment and Maintenance | X12N 834 |
| Health Care Service Review Information | X12N 278 |
| Payment Order/Remittance Advice | X12N 820 |

*TABLE 1. HIPAA Adopted Transactiona and X12N Standard Mapping*

*Note:* This excludes retail pharmacies, which uses NCPDP Version D.0 and NCPDP Version 3.0. This post will not list these standards for simplicity since this is only meant to provide an overview [`24`].

Fig. 3 below displays sample standardized transactions between providers and insurance/payors:

![HIPAA Transactions](../../hipaa/hipaa-transactions.png)
*Fig. 3. HIPAA Transactions*

A great infographic from CMS regarding Health Care Transaction Basics can be accessed [`here`](https://www.cms.gov/files/document/health-care-transactions-basics.pdf). 

### Code Sets

Code Sets are shared list of codes that simplifies longer names or explanations, resulting in faster information format translations [`25`]. Additionaly, specific code sets are adopted by the HHS for its electronic health care transactions, which are used in different health care functions [`24`]:
- Billing
- Tracking Public Health Trends
- Research

TABLE 2 outlines some of health care activities found in standard transactions and their respective identifying code sets [`25`]:
|  Health Care Activity/Item  |  Adopted Code Set  |
|----------|----------|
| Diagnoses (ICD-10-CM) and Procedures (ICD-10-PCS) | ICD-10 (International Classification of Diseases, 10th edition) |
| Outpatient services/procedures | CPT (Current Procedure Terminology) |
| Health Care Equipment/Supplies and Services Not Covered by CPT Codes | HCPCS (Health Care Common Procedure Coding System) |
| Dental Procedures | CDT (Code on Dental Procedures and Nomenclature) |
| Drug Products | National Drug Codes (NDC) |

*TABLE 2. Health Care Activity and Adopted Code Set Mapping*

Another great infographic from CMS about Code Set Basics can be found [`here`](https://www.cms.gov/files/document/code-sets.pdf).

### Unique Identifiers

HIPAA has set unique identifiers for employers and providers, which must be used on all HIPAA transactions [`26`]. Employers are required to have an Internal Revenue Service (IRS)-issued **Employer Identification Number (EIN)** [`25`]. Providers must use a unique 10-digit **National Provider Identifier (NPI)** [`26`]. At the time of writing, no identifiers are adopted for health plans and patients [`26`].

## Security and Privacy

Now that the Administrative Requirements are out of the way (phew!), the rest of the post will focus on the security and privacy aspect of HIPAA under **45 CFR Part 164**, specifically [`27`]:
- Subpart C: Security Standards for the Protection of Electronic Protected Health Information (Security Rule)
- Subpart D: Notification in the Case of Breach of Unsecured Protected Health Information (Breach Notification Rule)
- Subpart E: Privacy of Individually Identifiable Health Information (Privacy Rule)

These subparts highlight the technical requirements for the Security Rule, Breach Notification Rule, and Privacy Rule. 

### Privacy Rule

Although the Security Rule comes first in the 45 CFR, it is important to understand which information are considered protected health information and thus must be protected by the HIPAA. 

The *Privacy of Individually Identifiable Health Information* or the Privacy Rule, issued by the HHS, sets the national standards for the protection of certain health information [`29`]. This Rule ensures the protection of individual's health information while permitting the flow of necessary information, promoting high quality health care and protecting the public's health and well-being [`29`]. 

HHS' OCR is responsible for the implementation and enforcement of the Privacy Rule (and Security Rule) with voluntary compliance activities and civil monetary penalties [`29`].

The HHS released a summary of the Privacy Rule that can be used as a reference: [`SUMARY OF THE HIPAA PRIVACY RULE`](https://www.hhs.gov/sites/default/files/ocr/privacy/hipaa/understanding/summary/privacysummary.pdf).

#### Patient Information Protection

The Privacy Rule protects *any individually identifiable health information* or PHI that is transmitted and maintained by the covered entities and its BAs in any form (electronic and other mediums), with certain exceptions [`28`]:
- In education records covered by the Family Educational Rights and Privacy Act, as amended, 20 U.S.C. 1232g;
- In records described at 20 U.S.C. 1232g(a)(4)(B)(iv);
- In employment records held by a covered entity in its role as employer; and
- Regarding a person who has been deceased for more than 50 years.

Specifically, information created or received by covered entities, public health authority, employer, life insurer, and school or university; including the following [`29`]:
- Past, present, or future physical or mental health or condition of an individual;
- The provision of health care to an individual; 
- Past, present, or future for the provision of health care to an individual;
- Genetic health information [(`45 CFR § 160.103`)](https://www.law.cornell.edu/cfr/text/45/160.103); or
- Information traceable to a patient by one or more of the HIPAA 18 identifiers

The HHS lists 18 identifiers that can potentially identify, contact, or locate an individual [`30`]: 
1. Names;
2. All geographical subdivisions smaller than a state;
3. All elements of dates, except year, directly related to an individual;
4. Phone numbers;
5. Fax numbers;
6. Electronic mail addresses;
7. Social security numbers (SSN);
8. Medical record numbers (MRN);
9. Health plan beneficiary numbers;
10. Account numbers;
11. Certificate or license numbers;
12. Vehicle identifiers and serial numbers, including license plate numbers;
13. Device identifiers and serial numbers;
14. Web Universal Resource Locators (URLs);
15. Internet Protocol (IP) address numbers;
16. Biometric identifiers, including finger and voice prints;
17. Full face photographic images and any comparable images; and 
18. Any other unique identifying number, characteristic, or code

#### De-identified Health Information
A **De-identified Health Information** is a health information that does not identify and cannot be used to identify an individual [`31`]. 45 CFR § 164.514(b) outlines two methods a covered entity can use to determine whether a PHI is de-identified [`31`]:
1. Expert Determination

    Where a person with the appropriate knowledge of and experience with accepted statistical and scientific principles and methods, or an expert, determines that the risk is *very small* that the health information alone or in combination with other information can be used to identify an individual; and documents the methods and results of the analysis to justify the determination.

2. Safe Harbor  

    Where the 18 HIPAA Identifiers (listed above) of the individual, or of relatives, employers, or household members of the individual are removed; and the covered entity does not have actual knowledge that the information could be used alone or in combination with other information to identify an individual.

45 CFR § 164.514(c) outlines the method covered entities can implement to re-identify the information, specifically by assigning a code or other record identification, provided that [`31`]:
1. [Deviation] the code or other record identification was not derived from or related to the information of the individual, and cannot be translated to identify an individual -- basically think of the best practices when implementing a cookie in your web applications; and 
2. [Security] the covered entity does not use or disclose the code or other record identification for other purposes beside to re-identify the information, and does not disclose the mechanism for re-identification.

#### Permitted Use and Disclosure

### Security Rule

The **Security Rule** requires that covered entities and BAs must ensure the confidentiality, integrity, and availability (CIA) of all electronic protected health information (e-PHI) they create, receive, maintain, or transmit [`28`]. Furthermore, this Rule requires that the covered entities and BAs protect the integrity or security of the e-PHI against any reasonable anticipated threats, and misuse or unauthohrized disclosures [`28`]. Lastly, ensure the compliance of their workforce with this Rule [`28`].

# References

1. CDC, Health Insurance Portability and Accountability Act of 1996 (HIPAA), cdc.gov, https://www.cdc.gov/phlp/php/resources/health-insurance-portability-and-accountability-act-of-1996-hipaa.html (accessed November 02, 2024)
2. U.S. Department of Health and Human Services, Summary of the HIPAA Privacy Rule, hhs.giv, https://www.hhs.gov/hipaa/for-professionals/privacy/laws-regulations/index.html (accessed November 02, 2024)
3. United States Senate, Laws and Acts, senate.gov, https://www.senate.gov/pagelayout/legislative/one_item_and_teasers/Laws_and_Acts_page.htm (accessed November 02, 2024)
4. American Speech-Language-Hearing Association, Health Insurance Portability and Accountability Act, asha.org, https://www.asha.org/practice/reimbursement/hipaa/ (accessed November 02, 2024)
5. Centers for Medicare & Medicaid Services, The Health Insurance Portability and Accountability Act (HIPAA) of 1996 Helpful Tips, cms.gov, https://www.cms.gov/regulations-and-guidance/health-insurance-reform/healthinsreformforconsume/downloads/hipaa_helpful_tips_rev_1.pdf (accessed November 02, 2024)
6. Steve Alder, HIPAA Explained, hipaajournal.com, https://www.hipaajournal.com/hipaa-explained/ (accessed November 03, 2024)
7. AMA, HIPAA administrative simplification, ama-assn.org, https://www.ama-assn.org/practice-management/hipaa/hipaa-administrative-simplification (accessed November 03, 2024)
8. Steve Alder, HIPAA Updates and HIPAA Changes in 2024, hipaajournal.com, https://www.hipaajournal.com/hipaa-updates-hipaa-changes/ (accessed November 03, 2024)
9. Office of the Federal Register, A Guide to the Rulemaking Process, federalregister.gov, https://www.federalregister.gov/uploads/2011/01/the_rulemaking_process.pdf (accessed November 03, 2024)
10. U.S. Department of Health and Human Services, Special Topics in Health Information Privacy, hhs.gov, https://www.hhs.gov/hipaa/for-professionals/special-topics/index.html (accessed November 03, 2024)
11. U.S. Department of Health and Human Services, HIPAA and Reproductive Health, hhs.gov, https://www.hhs.gov/hipaa/for-professionals/special-topics/reproductive-health/index.html (accessed November 03, 2024)
12. Cornell Law School, 45 CFR Subchapter C - SUBCHAPTER C—ADMINISTRATIVE DATA STANDARDS AND RELATED REQUIREMENTS, https://www.law.cornell.edu/cfr/text/45/chapter-A/subchapter-C (accessed November 03, 2024)
13. Centers for Medicare & Medicaid Services, HIPAA Administrative Simplification Resources and FAQs, cms.gov, https://www.cms.gov/training-education/look-up-topics/hipaa-administrative-simplification-resources-and-faqs (accessed November 03, 2024)
14. Centers for Medicare & Medicaid Services, Are You a Covered Entity?, cms.gov, https://www.cms.gov/priorities/key-initiatives/burden-reduction/administrative-simplification/hipaa/covered-entities (accessed November 03, 2024)
15. Centers for Medicare & Medicaid Services, Covered Entity Decision Tool, cms.gov, https://www.cms.gov/Regulations-and-Guidance/Administrative-Simplification/HIPAA-ACA/Downloads/CoveredEntitiesChart20160617.pdf (accessed November 03, 2024)
16. Cornell Law School, Health care provider, law.cornell.edu, https://www.law.cornell.edu/definitions/index.php?width=840&height=800&iframe=true&def_id=808782b490d63d2744ee5d9a1336d988&term_occur=999&term_src=Title:45:Chapter:A:Subchapter:C:Part:160:Subpart:A:160.103 (accessed November 03, 2024)
17. Cornell Law School, Health care clearinghouse, law.cornell.edu, https://www.law.cornell.edu/definitions/index.php?width=840&height=800&iframe=true&def_id=8b76bcc5b120eabe975323d7896f0cf3&term_occur=999&term_src=Title:45:Chapter:A:Subchapter:C:Part:160:Subpart:A:160.103 (accessed November 03, 2024)
18. Cornell Law School, 45 CFR § 160.103 - Definitions, law.cornell.edu, https://www.law.cornell.edu/cfr/text/45/160.103 (accessed November 03, 2024)
19. P.F. Edemekong, et. al., Health Insurance Portability and Accountability Act, National Library of Medicine, https://www.ncbi.nlm.nih.gov/books/NBK500019/ (accessed November 14, 2024)
20. Cornell Law School, 45 CFR Part 162 - PART 162—ADMINISTRATIVE REQUIREMENTS, law.cornell.edu, https://www.law.cornell.edu/cfr/text/45/part-162 (accessed November 08, 2024)
21. Centers for Medicare & Medicaid Services, Enforcement and Compliance, cms.gov, https://www.cms.gov/priorities/key-initiatives/burden-reduction/administrative-simplification/enforcement (accessed November 08. 2024)
22. Centers for Medicare & Medicaid Services, Transactions Overview, cms.gov, https://www.cms.gov/priorities/key-initiatives/burden-reduction/administrative-simplification/transactions (accessed November 08. 2024)
23. Centers for Medicare & Medicaid Services, Health Care Transactions Basics, cms.gov, https://www.cms.gov/files/document/health-care-transactions-basics.pdf (accessed November 08. 2024)
24. Centers for Medicare & Medicaid Services, Adopted Standards and Operating Rules. cms.gov, https://www.cms.gov/priorities/key-initiatives/burden-reduction/ (accessed November 08. 2024)
25. Centers for Medicare & Medicaid Services, Code Sets Basics, cms.gov, https://www.cms.gov/files/document/code-sets.pdf (accessed November 08. 2024)
26. Centers for Medicare & Medicaid Services, Unique Identifiers, cms.gov, https://www.cms.gov/priorities/key-initiatives/burden-reduction/administrative-simplification/unique-identifiers (accessed November 08. 2024)
27. Cornell Law School, 45 CFR Part 164 - PART 164—SECURITY AND PRIVACY, law.cornell.edu, https://www.law.cornell.edu/cfr/text/45/part-164 (accessed November 08, 2024)
28. Cornell Law School, Protected health information, law.cornell.edu, https://www.law.cornell.edu/definitions/index.php?width=840&height=800&iframe=true&def_id=7383a4ae647bf28b2388260d0de8b4ef&term_occur=999&term_src=Title:45:Chapter:A:Subchapter:C:Part:164:Subpart:E:164.502 (accessed November 16, 2024)
29. https://www.hhs.gov/sites/default/files/ocr/privacy/hipaa/understanding/summary/privacysummary.pdf
30. https://biospecimens.cancer.gov/resources/sops/docs/GTEx_SOPs/BBRB-ER-0001-F1%20List%20of%20HIPAA%20Identifiers.pdf
3-. Cornell Law School, 45 CFR § 164.306 - Security standards: General rules., law.cornell.edu, https://www.law.cornell.edu/cfr/text/45/164.306 (accessed November 16, 2024)
