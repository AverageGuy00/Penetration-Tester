The most critical back-end vulnerabilities are those exploitable externally, allowing attackers to control the server without local access. These usually result from coding mistakes and range from simple to highly complex vulnerabilities.

# **Public CVE**

Public web applications, both open-source and proprietary, are widely tested, leading to frequent discovery of vulnerabilities. Once patched, these are assigned a **CVE (Common Vulnerabilities and Exposures)** record and score. Many proof-of-concept exploits are also made publicly available for testing and education.

First, identify the web application version (e.g., from source code or version files like `version.php`). Then search for public exploits for that version via Google or exploit databases like **Exploit DB**, **Rapid7 DB**, or **Vulnerability Lab**. Prioritize exploits with **CVE scores 8–10** or those leading to **Remote Code Execution**. Check external components (e.g., plugins) for vulnerabilities too.

# **Common Vulnerability Scoring System (CVSS)**

CVSS is an open standard to measure vulnerability severity, helping organizations prioritize responses. Scores range from 0–10, based on **Base**, **Temporal**, and **Environmental** metrics. The **National Vulnerability Database (NVD)** provides Base scores for most public vulnerabilities. Current CVSS versions are **v2** and **v3**, with v3 offering updated metrics for more accurate scoring.

CVSS scoring ratings differ slightly between V2 and V3 as can be seen in the following tables:

| CVSS V2.0 Ratings | Base Score Range |
| :---------------: | :--------------: |
|        Low        |     0.0-3.9      |
|      Medium       |     4.0-6.9      |
|       High        |     7.0-10.0     |

| CVSS V3.0 Ratings | Base Score Range |
| :---------------: | :--------------: |
|       None        |       0.0        |
|        Low        |     0.1-3.9      |
|      Medium       |     4.0-6.9      |
|       High        |     7.0-8.9      |
|     Critical      |     9.0-10.0     |

