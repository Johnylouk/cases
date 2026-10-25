Executive Investigation Report



Case ID: CASE-2026-003

Investigation Type: Endpoint Forensics

Evidence Examined: E001 — Windows memory image

Investigator: Ioannis

Investigation Date: 24/10/2026

Report Date: 25/10/2026



1. Executive Summary



An investigation was conducted on a Windows workstation following concerns regarding potential compromise.

The examination identified artifacts consistent with suspicious activity involving input capture and persistence mechanisms.

The available evidence does not establish the identity of the responsible individual or the full extent of the activity.




2\. Scope



The investigation was limited to the supplied Windows memory image.

No conclusions were made regarding evidence that was not available for examination.



3\. Evidence Examined



| ID   | Description          | Source                       |

| ---- | -------------------- | ---------------------------- |

| E001 | Windows memory image | CyberDefenders training case |



The original supplied evidence was preserved and a working copy was used for analysis.



The SHA-256 hash of the original evidence was recorded before analysis.



4\. Key Findings


&#x20;7.1 Finding F-001 — Compromised Adobe Acrobat Reader process.


Malicious file found within the process handles through windows.pslist, windows.handles.


\*\*Assessment:\*\* The observation supports the assessment that the malicious PDF.php file was executed through Adobe Acrobat being initialized by Firefox.


&#x20;7.2 Finding F-002 — Hollowed process and Second stage mechanism.


A service host process was subsequently hollowed and injected with a malicious dll. URL consistent with a download was found in the process memory which may have been used to download the second stage mechanism e.exe.

\*\*Assessment:\*\* The combined observations support the assessment that the hollowed process achieved the initialization of the second stage mechanism.


&#x20;7.3 Finding F-003 — Persistence and Input Capture.

The malware then moved to achieve persistence and log user inputs.


The malware achieved persistence by writing itself in a way that it start with the system itself and captures all user input.

\*\*Assessment:\*\* The combined observations support the assessment that the malware achieved persistence and established an input capture mechanism.


5\. Timeline



Event   Source	Description	                        Timestamp	                Notes
1	MFTScan	MFT SI entry for PDF.php	        2010-02-27 20:12:28.2726250 UTC	Entry Point
2   	MFTScan	MFT SI entry for e.exe	  	        2010-02-27 20:12:32.8038750 UTC	Temporary Executable
3	MFTScan	MFT SI entry for sdra64.exe	        2010-02-27 20:12:34.3820000 UTC	Proper Executable
4	RECmd	Last write time for Winlogon hive	2010-02-27 20:12:34.3820000 UTC	Persistence Established


6\. Indicators



The investigation identified the following investigation-relevant indicators:



No	Type	Value	                                                                            Notes
1	File	pdf.php	                                                                            551147a39bc439f46421a994fdbe6c5bafed90bf3b79121722d95bfa1ea435a1
2	File	pid.1384.vad                                                                        2e7aa9262eac9161d0b122ba374e2576ca8640b6d8aa54d522a3baea0406fd36
3	URL	http://193.104.22.71/~produkt/983745213424/34650798253 	
4	File	e.exe	                                                                            65b493cfea216a900dfc337b0c85451f3c0114dfc9e0eb4dd826d8ed1fca4e5c
5	File	sdra64.exe	                                                                    65b493cfea216a900dfc337b0c85451f3c0114dfc9e0eb4dd826d8ed1fca4e5c
6	RegKey	Userinit (RegSz) C:\WINDOWS\system32\userinit.exe,C:\WINDOWS\system32\sdra64.exe	


7\. Impact



Based on the available evidence, unauthorized or suspicious execution occurred on the examined workstation.



The available evidence is insufficient to determine the complete scope of activity or whether data was in fact exfiltrated.



8\. Limitations



The following limitations apply:



1\. Only a memory image was available.

2\. The investigator did not perform the original acquisition.

3\. No disk image was available.

4\. No complete network telemetry was available.

5\. No independent authentication logs were available.

6\. The identity of the person responsible for the observed activity cannot be established from the examined evidence alone.

7\. The registry hive and MFT data were reconstructed from memory and may be incomplete, because some pages may not be resident.



These limitations restrict conclusions regarding the complete scope, persistence, origin, and impact of the activity.




9\. Recommendations



Based on the findings, next steps to be considered:



1\. Examination of the workstation's disk image, if available.

2\. Review of network and DNS logs.

3\. Credentials: treat every account used on the machine as compromised. Change the passwords from a clean device, revoke sessions, and turn on MFA.

4\. Block: the IP address and the URL at the firewall or proxy, and the hashes in endpoint tools.

5\. The co-worker: the email came from a co-worker’s account, so that account and machine may be compromised too.

6\. Rebuild the machine from a known-good image rather than trusting a cleanup.

7\. Harden: update Acrobat Reader and the browser, turn off JavaScript in the PDF reader, and run awareness training.



\ 10. Conclusion



An investigation was conducted on a Windows workstation following concerns regarding potential compromise.

The examination identified artifacts consistent with suspicious activity involving input capture and persistence mechanisms.

After the execution of the malicious pdf, a second stage was initiated which then established the foothold of the malware in the system.

This was done through hollowing processes to insert itself, tampering with registry to ensure that it runs on log on, a deep kernel hook for input capture.

The available evidence does not establish the identity of the responsible individual or the full extent of the activity.







