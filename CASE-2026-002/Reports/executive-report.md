Executive Investigation Report



Case ID: CASE-002

Investigation Type: Endpoint Forensics

Evidence Examined: E001 — Windows disk image

Investigator: Ioannis

Investigation Date: 02/10/2026

Report Date: 06/10/2026



1. Executive Summary



An investigation was conducted on a Windows workstation following concerns regarding potential compromise.



The examination identified evidence of PowerShell execution associated with data exfiltration and network communication.



The available evidence supports the conclusion that PowerShell was executed on the workstation and that data regarding the local network devices 

was stored in order to be exfiltrated.



The available evidence does not, by itself, establish the identity of the person responsible for the activity.



2\. Scope



The investigation was limited to the supplied Windows disk image.



The objectives were to:



Reconstruct an attack timeline by analyzing disk images, event logs, and malicious scripts to identify initial access, persistence, and data exfiltration techniques.



No conclusions were made regarding evidence that was not available for examination.



3\. Evidence Examined



| ID   | Description          | Source                       |

| ---- | -------------------- | ---------------------------- |

| E001 | Windows disk image | CyberDefenders training case |



The original supplied evidence was preserved and a working copy was used for analysis.



The SHA-256 hash of the original evidence was recorded before analysis.



4\. Key Findings



&#x20;Finding F-001 — RAR with embedded malicious file 



A RAR archive was identified in the "Telegram Desktop" folder in the image which contained a malicious file executed via a known vulnerability.



\*\*Assessment:\*\* The evidence supports that the archive originated from the Telegram Desktop application.



&#x20;Finding F-002 — Second stage mechanism



A BAT file was subsequently identified which acts a second stage mechanism to create and run the script that performs the data exfiltration as well as set up persistence.



\*\*Assessment:\*\* The evidence supports that the BAT file creates a scheduled task which runs the exfiltration script every 3 minutes.



&#x20;Finding F-003 — Detection evasion mechanism



A log of the execution of another script (EventLogs.ps1) which deleted logs was discovered, as well as evidence of disguise of timestamps in the form of timestomping.



\*\*Assessment:\*\* The evidence supports that an attempt was made to tamper with event-logs and timeline reconstruction.



&#x20;Finding F-004 — PowerShell exfiltration script



A PowerShell script was discovered that scans local IP addresses and saves the ones that are active in a list on another file to be sent to a specific IP address.



\*\*Assessment:\*\* The evidence supports that the file with the list was created successfully.



5\. Timeline



Timestamp		Event

2024-02-03 07:33:20 UTC SANS SEC401.rar Received via Telegramh

2024-02-03 07:34:23 UTC SANS SEC401.rar Accessed SANS SEC401.rar

2024-02-03 07:34:39 UTC Eventlogs.ps1 Created

2024-02-03 07:34:49 UTC run.bat Created

2024-02-03 07:34:40 UTC run.ps1 Created

2024-02-03 07:37:02 UTC BL4356.txt Created

2024-02-03 07:38:01 UTC Logs Cleared

2024-02-03 07:38:01 UTC Eventlogs.ps1 Deleted



6\. Indicators



The investigation identified the following investigation-relevant indicators:



Filename	SANS\_SEC401.rar		d1a55bb98b750ce9b9d9610a857ddc408331b6ae6834c1cbccca4fd1c50c4fb8

Filename	SANS SEC401.pdf.cmd	5790225b1bcfa692c57a0914dd78678ceef6e212fbe7042b7ddf5a06fd4ab70d

Filename	run.bat			f11e1927a12b0bf6bc41b4ea1363b45aa8b5d194737d4bb00f537956e2725324

Filename	run.ps1			771c29efb71da4459e130cf8df363849c26c8a2ea69bcbdcce2f1809a02f075a

Filename	BL4356.txt		ACE607318BBA614EAD615D2D8D9671C1FC2CF7A26C53EEFE4E226F8A06246E04

IP		192.168.1.5:8000



These indicators are documented in `iocs/E001-iocs.csv`.



7\. Impact



Based on the available evidence, unauthorized or suspicious execution occurred on the examined workstation.



The available evidence is insufficient to determine the complete scope of activity or whether data was in fact exfiltrated.



8\. Limitations



The investigation was based on a disk image rather than a complete collection of endpoint, network, authentication, and filesystem evidence.



Consequently, the investigation cannot independently establish:



\* the complete history of the workstation;

\* all files that may have existed on the system;

\* all network connections made outside the captured memory state;

\* the identity of the person operating the system;

\* the complete scope of any potential compromise.



9\. Recommendations



Based on the findings, further investigation should consider:



1\. Examination of the workstation's memory image, if available.

2\. Review of network and DNS logs.

3\. Investigation of identified IP addresses and file hashes.

4\. Examination of other potentially affected endpoints.

5\. Preservation of additional evidence before remediation where appropriate.



\ 10. Conclusion



The examination identified evidence of PowerShell execution and associated network communication.



The findings are based on the evidence available for examination and should be interpreted together with the documented limitations.



No conclusion is made regarding attribution to a specific individual without additional evidence.







