Technical Investigation Report



Case ID: CASE-002

Investigation Type: Endpoint Forensics

Evidence Examined: E001 — Windows disk image

Investigator: Ioannis

Investigation Date: 02/10/2026

Report Date: 06/10/2026



1\. Case Information



&#x20;1.1 Objective



The objective of this examination was to determine whether the supplied Windows disk image contained evidence of suspicious or unauthorized activity and, where possible, reconstruct the relevant activity.



Specific investigative objectives were:



1\. Identify initial access mechanisms.

2\. Identify persistence mechanisms .

3\. Identify data exfiltration mechanisms.

4\. Reconstruct a timeline of relevant activity.

5\. Document limitations and alternative explanations.



2\. Scope



The examination was limited to the supplied disk image E001.



No memory image, network capture, endpoint detection telemetry, or additional system logs were available unless explicitly documented below.



3\. Evidence



&#x20;E001



Description: Windows disk image

Filename: c125-SpottedInTheWild.vhd

Source: CyberDefenders training environment

Evidence status: Externally supplied evidence

Original acquisition performed by: Unknown / not performed by investigator

SHA-256: 2C29D0B089560029EF528E1660C02AA578606BBE1F4D4F4A54EDECFECD5B8671



The investigator did not perform the original acquisition. The supplied file was therefore treated as the original evidence received for this investigation.



4\. Evidence Handling



The supplied evidence was copied into the case evidence directory and preserved without modification.



A working copy was created for analysis.



&#x20;Integrity verification



Original:



c125-SpottedInTheWild.vhd

SHA-256: 2C29D0B089560029EF528E1660C02AA578606BBE1F4D4F4A54EDECFECD5B8671



Working copy:



c125-SpottedInTheWild\_Working.vhd

SHA-256: 2C29D0B089560029EF528E1660C02AA578606BBE1F4D4F4A54EDECFECD5B8671



Result: MATCH



Analysis was conducted against the working copy.



5\. Tools



| Tool          | Version   		| Purpose            |

| ------------  | ---------		| ------------------ |

| Autopsy	|  4.21.0   		| File analysis	     |

| Powershell    | 5.1.26100.9444	| Hash calculation   |



6\. Methodology



The investigation proceeded from general system identification toward targeted examination.



The following sequence was used:



1. Identify users.
2. Identify suspicious files.
3. Examine suspicious files.
4. Correlate with system logs.
5. Construct timeline.



The investigation was driven by observations and resulting investigative questions rather than by assuming the presence of a particular malware family.



7\. Findings



&#x20;7.1 Finding F-001



\### Finding



Evidence indicates the presence of an archive with an embedded malicious file.



\### Evidence



\* E001

\* SANS\_SEC401.rar

\* SANS SEC401.pdf.cmd



\### Observations



1\. SANS SEC401.pdf.cmd (malicious) found within SANS\_SEC401.rar.

2\. SANS\_SEC401.pdf (benign) file found within SANS\_SEC401.rar.



\### Interpretation



The combined observations support the assessment that CVE-2023-38831 was exploited on WinRAR version 6.22 to execute the malicious file on opening the archive.



\### Confidence



High, subject to the limitations described in Section 10.



&#x20;7.2 Finding F-002



\### Finding



A BAT file was subsequently identified which acts as a second stage mechanism to create and run the script that performs the data exfiltration as well as set up persistence.



\### Evidence



\* E001

\* run.bat



\### Observations



1\. Run.bat was encoded with Environment Variable Expansion.

2\. It creates and runs the exfiltration mechanism run.ps1.

3\. It creates and runs the event-log tampering mechanism Eventlogs.ps1.

4\. It schedules a task called whoisthebaba to run the script every 3 minutes.



\### Interpretation



The combined observations support the assessment that a detection evasion attempt took place and a persistence mechanism was established.



\### Confidence



High, subject to the limitations described in Section 10.



&#x20;7.3 Finding F-003



\### Finding



A PowerShell script was discovered that scans local IP addresses and saves the ones that are active in a list on another file to be sent to a specific IP address.



\### Evidence



\* E001

\* run.ps1

\* BL4356.txt



\### Observations



1\. Run.ps1 was encoded with base64.

2\. It scans local IP addresses 192.168.1.1-99 for UP state.

3\. It logs them into BL4356.txt.

4\. It then is designed to exfiltrate the data to IP address 192.168.1.5:8000.



\### Interpretation



The combined observations support the assessment that the file with the list was created successfully but without network data we cannot confirm anything was transmitted.



\### Confidence



High, subject to the limitations described in Section 10.



8\. Indicators



Type		Value			Source		Confidence

Filename	SANS\_SEC401.rar		filesystem	high

Filename	SANS SEC401.pdf.cmd	filesystem	high

Filename	run.bat			filesystem	high

Filename	run.ps1			filesystem	high

Filename	BL4356.txt		filesystem	high

IP		192.168.1.5:8000	run.ps1		high



Complete IOC information is maintained separately in:



`iocs/E001-iocs.csv`



9\. Timeline



Timestamp		Event					Source		Evidence	Confidence

2024-02-03 07:33:20 UTC	SANS SEC401.rar Received via Telegram	SANS SEC401.rar	Metadata	High

2024-02-03 07:34:23 UTC	SANS SEC401.rar Accessed		SANS SEC401.rar	Metadata	High

2024-02-03 07:34:39 UTC	Eventlogs.ps1 Created			MFT	Metadata	High

2024-02-03 07:34:49 UTC	run.bat Created			MFT	Metadata	High

2024-02-03 07:34:40 UTC	run.ps1 Created			MFT	Metadata	High

2024-02-03 07:37:02 UTC	BL4356.txt Created			MFT	Metadata	High

2024-02-03 07:38:01 UTC	Logs Cleared	Event Viewer (1102)	Metadata	High

2024-02-03 07:38:01 UTC	Eventlogs.ps1 Deleted	MFT	Metadata	High


The timeline represents reconstructed activity supported by the available evidence. It should not be interpreted as a complete history of the workstation.



Full timeline:



`timeline/E001-timeline.csv`



10\. Limitations



The following limitations apply:



1\. Only a disk image was available.

2\. The investigator did not perform the original acquisition.

3\. No memory image was available.

4\. No complete network telemetry was available.

5\. No independent authentication logs were available.

6\. The identity of the person responsible for the observed activity cannot be established from the examined evidence alone.



These limitations restrict conclusions regarding the complete scope, persistence, origin, and impact of the activity.



11\. Conclusion



The examination identified artifacts consistent with suspicious activity involving an archive containing a command file, PowerShell script execution, an attempted or configured event-log-tampering mechanism, and a scheduled task intended to provide recurring execution.

The examined PowerShell script was designed to scan addresses in the 192.168.1.1–192.168.1.99 range, record responsive hosts in BL4356.txt, and send the resulting data to 192.168.1.5:8000. The available evidence supports the identification of this configured behavior but does not independently establish successful network transmission.

The archive contents are consistent with a potential CVE-2023-38831 exploitation scenario involving WinRAR. Confirmation of exploitation requires correlation with evidence of the relevant vulnerability conditions and resulting execution.

Event ID 1102, if verified in the available event record, supports the conclusion that the Windows Security audit log was cleared. Attribution of this action to a particular script or individual requires additional corroboration.

The reported timeline contains timestamp inconsistencies that must be resolved before a definitive sequence of events can be established. The conclusions are limited to the supplied disk image and the artifacts documented in this report.

The available evidence does not establish the identity of the responsible individual, the full extent of the activity, or whether data was successfully transmitted to the identified destination.



12\. Reproducibility



The following materials are retained within the case directory:



\* original evidence;

\* evidence hashes;

\* working copy;

\* derived artifacts;

\* derived-artifact hashes;

\* investigator notes;

\* timeline;

\* IOC list.



Another investigator should be able to reproduce the principal findings using these materials and the documented tool versions and commands.



