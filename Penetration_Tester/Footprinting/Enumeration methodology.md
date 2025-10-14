Complex processes must follow a standardized `methodology` so we keep bearings and avoid omitting aspects by mistake. Targets vary widely; relying solely on habit produces an `experience-based approach` not a repeatable `methodology`.

The `enumeration` process is dynamic. We use a static `enumeration methodology` for `external` and `internal` penetration tests that preserves freedom for dynamics and adapts to the environment. This methodology is nested in `6 layers` and represents boundaries we attempt to pass during the `enumeration process`.

- The whole `enumeration process` is divided into three different `levels` to structure effort and escalation.  
- The static `methodology` provides consistent checkpoints while allowing iterative adjustments.  
- Apply the methodology to both `external` and `internal` tests; adapt layer progression per engagement scope, access, and rules of engagement.
- Document assumptions, constraints, and deviations at each `level` and `layer` to preserve repeatability and post-engagement analysis.

![[{B46A5A0D-3BB5-4AA5-B90E-535629057D6D}.png]]

# Enumeration Layers

Consider these lines as obstacles — like a wall. We look for an entrance, a gap to fit through, or a place to climb over to reach our goal. Smashing through headfirst often wastes time because there may be no passage on the other side.

These layers are designed as follows:

|         `Layer`          |                                    `Description`                                     |                                            `Information Categories`                                            |
| :----------------------: | :----------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------: |
|  1. `Internet Presence`  |    Identification of internet presence and externally accessible infrastructure.     | `Domains`, `Subdomains`, `vHosts`, `ASN`, `Netblocks`, `IP Addresses`, `Cloud Instances`, `Security Measures`  |
|       2. `Gateway`       | Identify possible security measures protecting external and internal infrastructure. |      `Firewalls`, `DMZ`, `IPS/IDS`, `EDR`, `Proxies`, `NAC`, `Network Segmentation`, `VPN`, `Cloudflare`       |
| 3. `Accessible Services` |     Identify accessible interfaces and services hosted externally or internally.     |                `Service Type`, `Functionality`, `Configuration`, `Port`, `Version`, `Interface`                |
|      4. `Processes`      | Identify internal processes, sources, and destinations associated with the services. |                           `PID`, `Processed Data`, `Tasks`, `Source`, `Destination`                            |
|     5. `Privileges`      |  Identification of internal permissions and privileges to the accessible services.   |                        `Groups`, `Users`, `Permissions`, `Restrictions`, `Environment`                         |
|      6. `OS Setup`       |               Identification of internal components and systems setup.               | `OS Type`, `Patch Level`, `Network config`, `OS Environment`, `Configuration files`, `sensitive private files` |

- Document findings per layer separately.
- Escalate to the next layer only after exhausting discovery options in the current layer.
- Treat missing artifacts as leads; validate assumptions before pivoting.