`Pre-engagement` is the stage of preparation for the actual `penetration test`. During this stage, many `questions` are asked, and some `contractual agreements` are made. The `client` informs us about what they want to be `tested`, and we explain in detail how to make the `test` as `efficient` as possible.

The entire `pre-engagement process` consists of three essential components:

- `Scoping questionnaire`
- `Pre-engagement meeting`
- `Kick-off meeting`

Before any of these can be discussed in detail, a `Non-Disclosure Agreement` (`NDA`) must be signed by all parties. There are several types of `NDAs`:

|      **Type**      |                                                                                             **Description**                                                                                             |
| :----------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|  `Unilateral NDA`  |                         This type of NDA obligates only one party to maintain confidentiality and allows the other party to share the information received with third parties.                          |
|  `Bilateral NDA`   |        In this type, both parties are obligated to keep the resulting and acquired information confidential. This is the most common type of NDA that protects the work of penetration testers.         |
| `Multilateral NDA` | Multilateral NDA is a commitment to confidentiality by more than two parties. If we conduct a penetration test for a cooperative network, all parties responsible and involved must sign this document. |

Exceptions can also be made in `urgent` cases, where we jump into the `kick-off meeting`, which can occur via `online conference`. It is essential to know who in the company is permitted to _contract_ us for a `penetration test` because we **cannot** accept such orders from just anyone. For example, an employee could hire us under the pretext of checking the `corporate network`'s security, but after the `assessment` it might turn out they intended to harm the company without proper `authorization` — putting us in a critical `legal` situation.

Sample (not exhaustive) list of company members who may be authorized to hire us for `penetration testing` (varies by organization, larger companies often delegate to `IT`, `Audit`, or `IT Security senior management` rather than C-level directly):

`Chief Executive Officer (CEO)`, `Chief Technical Officer (CTO)`, `Chief Information Security Officer (CISO)`, `Chief Security Officer (CSO)`, `Chief Risk Officer (CRO)`, `Chief Information Officer (CIO)`, `VP of Internal Audit`, `Audit Manager`, `VP or Director of IT/Information Security`

It is vital to determine early who has `signatory authority` for the `contract`, `Rules of Engagement` documents, and who will serve as the `primary` and `secondary points of contact`, `technical support`, and `escalation contact`.

---

This stage also requires preparing several `documents` before a `penetration test` can be conducted, which must be signed by both the `client` and us so that the `declaration of consent` can be presented in written form if needed. Without these, the `penetration test` could violate the `Computer Misuse Act`.

|                             **Document**                             |               **Timing for Creation**               |
| :------------------------------------------------------------------: | :-------------------------------------------------: |
|                `1. Non-Disclosure Agreement` (`NDA`)                 |               `After` Initial Contact               |
|                      `2. Scoping Questionnaire`                      |         `Before` the Pre-Engagement Meeting         |
|                        `3. Scoping Document`                         |         `During` the Pre-Engagement Meeting         |
| `4. Penetration Testing Proposal` (`Contract/Scope of Work` (`SoW`)) |         `During` the Pre-engagement Meeting         |
|                   `5. Rules of Engagement` (`RoE`)                   |            `Before` the Kick-Off Meeting            |
|          `6. Contractors Agreement` (Physical Assessments)           |            `Before` the Kick-Off Meeting            |
|                             `7. Reports`                             | `During` and `after` the conducted Penetration Test |

---

# Scoping Questionnaire

After `initial contact` with the `client`, we typically send a `Scoping Questionnaire` to understand the `services` they are seeking. This questionnaire should clearly explain our `services` and often asks the `client` to select one or more from the following options:

| Service Option                      |                                       |
| ----------------------------------- | ------------------------------------- |
| `Internal Vulnerability Assessment` | `External Vulnerability Assessment`   |
| `Internal Penetration Test`         | `External Penetration Test`           |
| `Wireless Security Assessment`      | `Application Security Assessment`     |
| `Physical Security Assessment`      | `Social Engineering Assessment`       |
| `Red Team Assessment`               | `Web Application Security Assessment` |

Under each `service option`, the `questionnaire` should let the `client` specify details: `web` or `mobile assessment`, `secure code review`, `black box` or `semi-evasive` `Internal Penetration Test`, `phishing` or `vishing` for `Social Engineering`. This ensures we understand the `client`'s `needs` and can deliver the required `assessment`.

Other critical information includes: `client name`, `address`, and key `personnel contacts`.

| Question                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------------------- |
| How many expected `live hosts`?                                                                                                                   |
| How many `IPs/CIDR ranges` in scope?                                                                                                              |
| How many `Domains/Subdomains` are in scope?                                                                                                       |
| How many `wireless SSIDs` in scope?                                                                                                               |
| How many `web/mobile applications`? If testing is `authenticated`, how many `roles` (standard user, admin, etc.)?                                 |
| For a `phishing assessment`, how many users will be targeted? Will the client provide a list, or must we gather this list via `OSINT`?            |
| If the client requests a `Physical Assessment`, how many locations? If multiple sites are in-scope, are they geographically dispersed?            |
| What is the objective of the `Red Team Assessment`? Are any activities (such as `phishing` or physical security attacks) explicitly out of scope? |
| Is a separate `Active Directory Security Assessment` desired?                                                                                     |
| Will network testing be conducted from an `anonymous user` on the network or a standard `domain user`?                                            |
| Do we need to bypass `Network Access Control (NAC)`?                                                                                              |

Finally, we will want to ask about information disclosure and evasiveness (if applicable to the assessment type):

- Is the `penetration test` `black box` (no info), `grey box` (IPs/CIDRs/URLs only), or `white box` (detailed info)?

- Desired evasiveness: `non-evasive`, `hybrid-evasive` (start quiet, escalate), or `fully evasive`?

This guides resource allocation, timeline, and cost (e.g., `Vulnerability Assessment` < `Pen Test` < `Red Team`; 10 external IPs << internal test covering 30 /24s).

---

## Pre-Engagement Meeting

Once we have an initial understanding of the `client`'s `project requirements`, we move to the `pre-engagement meeting`. This meeting covers all relevant `components` before the `penetration test`, explaining them to the `client`. The `information` gathered here, together with data from the `scoping questionnaire`, forms the basis for the `Penetration Testing Proposal` (also called the `Contract` or `Scope of Work (SoW)`).

Think of this phase like visiting a `doctor` to discuss planned `examinations`. This usually happens via `e-mail`, `online conference call`, or an `in-person meeting`.

#### Contract - Checklist

| Checkpoint                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NDA`                              | `Non-Disclosure Agreement (NDA)` is a secrecy contract between the `client` and the `contractor` covering all written or verbal information about a project. Confidential information must be treated strictly confidential even after project completion. Exceptions, rights transfer, obligations, and penalties are defined. Signed before the `kick-off meeting` or at the latest before detailed information is discussed. |
| `Goals`                            | Milestones to be achieved during the project. Goal setting starts with major goals and continues with fine-grained, smaller ones.                                                                                                                                                                                                                                                                                               |
| `Scope`                            | Defines components to be tested: `domains`, `IP ranges`, hosts, accounts, `security systems`, etc. Legal basis for testing takes priority.                                                                                                                                                                                                                                                                                      |
| `Penetration Testing Type`         | Presents test options, explains advantages/disadvantages, recommends based on goals and scope. Final choice is the `client`'s decision.                                                                                                                                                                                                                                                                                         |
| `Methodologies`                    | Examples include `OSSTMM`, `OWASP`, automated/manual unauthenticated analysis of network components, `vulnerability assessments`, threat vectorization, verification, exploitation, and `exploit development` for evasion.                                                                                                                                                                                                      |
| `Penetration Testing Locations`    | `External`: Remote via secure VPN; `Internal`: Internal or Remote via secure VPN.                                                                                                                                                                                                                                                                                                                                               |
| `Time Estimation`                  | Defines start/end dates and duration for each phase (`Exploitation`, `Post-Exploitation`, `Lateral Movement`), during or outside regular hours, to plan the procedure accurately.                                                                                                                                                                                                                                               |
| `Third Parties`                    | Identifies third-party providers (cloud, ISPs, hosting). `Client` must obtain written consent allowing simulated attacks. Contractor should forward this permission for confirmation.                                                                                                                                                                                                                                           |
| `Evasive Testing`                  | Tests evasion techniques to bypass `security systems`. Use depends on `contractor` requirements.                                                                                                                                                                                                                                                                                                                                |
| `Risks`                            | Inform `client` of risks and potential consequences, set limitations, and take precautions accordingly.                                                                                                                                                                                                                                                                                                                         |
| `Scope Limitations & Restrictions` | Identify critical servers, workstations, or network components to avoid affecting the client or their customers.                                                                                                                                                                                                                                                                                                                |
| `Information Handling`             | Compliance with `HIPAA`, `PCI`, `HITRUST`, `FISMA/NIST`, etc.                                                                                                                                                                                                                                                                                                                                                                   |
| `Contact Information`              | List each person's `name`, `title`, `job title`, `e-mail`, `phone`, office number, and `escalation priority`.                                                                                                                                                                                                                                                                                                                   |
| `Lines of Communication`           | Document communication channels: `e-mail`, `phone`, or personal meetings.                                                                                                                                                                                                                                                                                                                                                       |
| `Reporting`                        | Discuss report structure, customer-specific requirements, reporting method, and whether results presentation is desired.                                                                                                                                                                                                                                                                                                        |
| `Payment Terms`                    | Explain `prices` and `payment terms`.                                                                                                                                                                                                                                                                                                                                                                                           |

---

The most crucial element of the meeting is the detailed presentation of the `penetration test` and its `focus` so we all agree on priorities. Every `infrastructure` is unique and clients often have specific `preferences` — find those priorities and build the test around them.

Think of it like ordering at a restaurant: if the client asks for `medium-rare` and we deliver `well-done` because we prefer it, we failed the engagement. Prioritize the client's wishes and deliver the steak they ordered.

Using the input from the `Contract Checklist` and the `scoping` phase, draft the `Penetration Testing Proposal (Contract)` and the `Rules of Engagement (RoE)` so expectations, limits, and responsibilities are crystal clear.

#### Rules of Engagement - Checklist

| **Checkpoint**                              | **Contents**                                                                                          |
| ------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `☐ Introduction`                            | Description of this document.                                                                         |
| `☐ Contractor`                              | Company name, contractor full name, job title.                                                        |
| `☐ Penetration Testers`                     | Company name, pentesters full name.                                                                   |
| `☐ Contact Information`                     | Mailing addresses, e-mail addresses, and phone numbers of all client parties and penetration testers. |
| `☐ Purpose`                                 | Description of the purpose for the conducted penetration test.                                        |
| `☐ Goals`                                   | Description of the goals that should be achieved with the penetration test.                           |
| `☐ Scope`                                   | All IPs, domain names, URLs, or CIDR ranges.                                                          |
| `☐ Lines of Communication`                  | Online conferences or phone calls or face-to-face meetings, or via e-mail.                            |
| `☐ Time Estimation`                         | Start and end dates.                                                                                  |
| `☐ Time of the Day to Test`                 | Times of the day to test.                                                                             |
| `☐ Penetration Testing Type`                | External/Internal Penetration Test/Vulnerability Assessments/Social Engineering.                      |
| `☐ Penetration Testing Locations`           | Description of how the connection to the client network is established.                               |
| `☐ Methodologies`                           | OSSTMM, PTES, OWASP, and others.                                                                      |
| `☐ Objectives / Flags`                      | Users, specific files, specific information, and others.                                              |
| `☐ Evidence Handling`                       | Encryption, secure protocols                                                                          |
| `☐ System Backups`                          | Configuration files, databases, and others.                                                           |
| `☐ Information Handling`                    | Strong data encryption                                                                                |
| `☐ Incident Handling and Reporting`         | Cases for contact, pentest interruptions, type of reports                                             |
| `☐ Status Meetings`                         | Frequency of meetings, dates, times, included parties                                                 |
| `☐ Reporting`                               | Type, target readers, focus                                                                           |
| `☐ Retesting`                               | Start and end dates                                                                                   |
| `☐ Disclaimers and Limitation of Liability` | System damage, data loss                                                                              |
| `☐ Permission to Test`                      | Signed contract, contractors agreement                                                                |

---

# Contractors Agreement

If the `penetration test` includes `physical testing`, an additional `contractor's agreement` is required — physical intrusion invokes different `laws` than virtual tests. Many `employees` may be unaware a test is happening; if our `physical attack` or `social engineering` runs into staff with high `security awareness` and we get caught, they’ll likely contact the `police`. That `contractor's agreement` is our written `proof` and effectively a `get out of jail free card` if things escalate. Keep it signed, clear, and on file before any boots-on-ground activity.

#### Contractors Agreement - Checklist for Physical Assessments

| **Checkpoint**                    |
| --------------------------------- |
| `☐ Introduction`                  |
| `☐ Contractor`                    |
| `☐ Purpose`                       |
| `☐ Goal`                          |
| `☐ Penetration Testers`           |
| `☐ Contact Information`           |
| `☐ Physical Addresses`            |
| `☐ Building Name`                 |
| `☐ Floors`                        |
| `☐ Physical Room Identifications` |
| `☐ Physical Components`           |
| `☐ Timeline`                      |
| `☐ Notarization`                  |
| `☐ Permission to Test`            |