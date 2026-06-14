# Host-Based SIEM Configuration & Threat Detection Engineering Lab

## Project Overview
This project demonstrates the end-to-end deployment, configuration, and validation of an enterprise-grade Security Information and Event Management (SIEM) platform using Wazuh. The objective of this lab was to establish real-time host-level telemetry across an isolated virtual network topology, simulate an adversary defense-evasion technique, and verify the ingestion pipeline and rule-matching engine capabilities.

---

## Skills & Technologies Demonstrated
* **SIEM/XDR Platform Management:** Wazuh Architecture (Manager, Indexer, Dashboard).
* **Virtual Infrastructure & Networking:** Oracle VirtualBox, Host-Only private networking, resource optimization.
* **Endpoint Telemetry Provisioning:** Administrative Windows PowerShell scripting, MSI-based endpoint agent deployment.
* **Threat Simulation & Log Analysis:** Windows Event Subsystem architecture, defensive telemetry validation, structured JSON log analysis.

---

Phase-by-Phase Implementation
Phase 1: Virtual Infrastructure Provisioning
Deployed the Linux-based Wazuh SIEM virtual appliance (v4.14.5) within Oracle VirtualBox.

Configured proper system hardware resource constraints (4096 MB RAM, 4 vCPUs) to guarantee stability on both the host and guest operating systems.

Engineered a dedicated host-only network adapter subnet (192.168.56.0/24) to safely route system log payloads without exposing the internal lab environment to the open internet.

Phase 2: Telemetry Ingestion & Endpoint Enrollment
Generated an enrollment script mapping directly back to the SIEM manager infrastructure endpoint at 192.168.56.101.

Provisioned the host-level collector endpoint by executing an optimized administrative PowerShell web request script to silently fetch, unpack, and install the MSI agent package.

Initiated the background sensor service (NET START Wazuh) and verified connection status viability on the centralized dashboard management frame.

Phase 3: Threat Simulation & Analytical Validation
Executed a programmatic threat simulation modeling MITRE ATT&CK T1070.001 (Indicator Removal on Host: Clear Windows Event Logs) by executing:

PowerShell
  wevtutil cl Security
Analyzed the server's real-time detection pipeline, validating that the incoming Windows Event ID 1102 string mapped flawlessly to security rule ID 60122.

Inspected the underlying parsed, structured JSON metadata payload containing contextual security account indicators (Logon ID, Account Domain, unique identifier strings).

Evidence of Completion
1. Active Agent Telemetry
The endpoint monitor dashboard shows the host endpoint successfully checking in over the secure private network pipeline:

2. High-Severity Threat Alert Triggered
The SIEM Discovery search engine indexing and surfacing the real-time defense evasion behavior event:

Project Takeaways & Practical Insights
Log Ingestion Integrity: Gained hands-on experience confirming that system log streams can be captured and processed continuously across disparate operating systems.

JSON Schema Familiarity: Examined how unstructured system event details are translated into standardized, queryable structural objects within an enterprise SOC database.

Defensive Mindset: Validated the crucial operational concept that attackers trying to erase their footsteps actually create a massive, distinct indicator for defenders to track.

## Architecture & Data Flow

```text
 ┌────────────────────────────────────────────────────────────────────────┐
 │                      YOUR HOST LAPTOP (Windows 11)                     │
 │                                                                        │
 │  ┌─────────────────────────┐               ┌────────────────────────┐  │
 │  │      Wazuh Agent        │               │     Google Chrome      │  │
 │  │  (Background Sensor)    │               │    (SOC Management)    │  │
 │  └───────────┬─────────────┘               └───────────▲────────────┘  │
 └──────────────┼─────────────────────────────────────────┼───────────────┘
                │ Ingests Telemetry (Port 1514)           │ Accesses UI (HTTPS 443)
                ▼                                         │
 ┌────────────────────────────────────────────────────────┼───────────────┐
 │       VIRTUALBOX HOST-ONLY PRIVATE NETWORK (192.168.56.X Subnet)       │
 └──────────────┬─────────────────────────────────────────┼───────────────┘
                │                                         │
 ┌──────────────▼─────────────────────────────────────────┴───────────────┐
 │                  WAZUH SIEM VIRTUAL MACHINE (Linux)                     │
 │                                                                        │
 │   ┌───────────────────────┐                 ┌──────────────────────┐   │
 │   │  Indexer & Server     │────────────────►│    Wazuh Dashboard   │   │
 │   │  (Analyzes Event 1102)│  Internal Pipe  │   (Visual Log Feed)  │   │
 │   └───────────────────────┘                 └──────────────────────┘   │
 └────────────────────────────────────────────────────────────────────────┘

