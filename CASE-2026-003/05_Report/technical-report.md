Technical Investigation Report



Case ID: CASE-2026-003

Investigation Type: Endpoint Forensics

Evidence Examined: E001 — Windows Memory image

Investigator: Ioannis

Investigation Date: 24/10/2026

Report Date: 25/10/2026



1\. Case Information



&#x20;1.1 Objective



Company X has contacted you to perform forensics work on a recent incident that occurred. One of their employees had received an e-mail from a co-worker that pointed to a PDF file. Upon opening, the employee did not notice anything; however, they recently had unusual activity in their bank account.

The initial theory is that a user received an e-mail, containing an URL leading to a forged PDF document. Opening that document in Acrobat Reader triggers a malicious Javascript that initiates a sequence of actions to take over the victim's system.

Company X was able to obtain a memory image of the employee's virtual machine upon suspected infection and asked you as a security blue team analyst to analyze the virtual memory and provide answers to the questions.



2\. Scope



The examination was limited to the supplied memory image E001.



No disk image, network capture, endpoint detection telemetry, or additional system logs were available unless explicitly documented below.



3\. Evidence



&#x20;E001



Description: Windows memory image

Filename: Bob.vmem

Source: CyberDefenders training environment

Evidence status: Externally supplied evidence

Original acquisition performed by: Unknown / not performed by investigator

SHA-256: 1b9bd5c614cb3726cd048eb5f094e81389e56c888424b8b7affb41694e7caad8



The investigator did not perform the original acquisition. The supplied file was therefore treated as the original evidence received for this investigation.



4\. Evidence Handling



The supplied evidence was copied into the case evidence directory and preserved without modification.



A working copy was created for analysis.



&#x20;Integrity verification



Original:



Bob.vmem

SHA-256: 1b9bd5c614cb3726cd048eb5f094e81389e56c888424b8b7affb41694e7caad8



Working copy:



Bob\_Working.vmem

SHA-256: 1b9bd5c614cb3726cd048eb5f094e81389e56c888424b8b7affb41694e7caad8



Result: MATCH



Analysis was conducted against the working copy.

Software hive: SOFTWARE
Mft records: $MFT



5\. Tools

Tool            Version         Purpose
Volatility 3    2.28.2          Memory analysis
sha256sum       9.4             Hash calculation
RECmd           2026.5.0        Registry analysis on software hive dumped from memory.
MFTECmd         2026.5.0        MFT analysis on mft records dumped from memory.



6\. Methodology



The investigation proceeded from general system identification toward targeted examination.



The following sequence was used:



1. Identify system information.
2. Identify suspicious processes.
3. Examine suspicious processes.
4. Identify suspicious files.
5. Examine suspicious files.
6. Construct timeline.



The investigation was driven by observations and resulting investigative questions rather than by assuming the presence of a particular malware family.

Extracted SOFTWARE hive with windows.dumpfiles after windows.filescan
Extracted $MFT records with windows.dumpfiles after windows.filescan


7\. Findings



&#x20;7.1 Finding F-001



\### Finding



Evidence indicates the presence of a compromised Adobe Acrobat Reader process.



\### Evidence



\* E001

\* PID 1752 AcroRd32.exe

\* PPID 1756 Firefox.exe

\* PDF.php



\### Observations



1\. Malicious file found within the process handles through windows.pslist, windows.handles.



\### Interpretation



The observation supports the assessment that the malicious PDF.php file was executed through Adobe Acrobat being initialized by Firefox.

\### Confidence



High, subject to the limitations described in Section 10.

&#x20;7.2 Finding F-002



\### Finding



A service host process was subsequently hollowed and injected with a malicious dll. URL consistent with a download was found in the process memory which may have been used to download the second stage mechanism e.exe.



\### Evidence



\* E001

\* PID 1384 svchost.exe

\* URL http://193.104.22.71/~produkt/983745213424/34650798253 

\* e.exe

\* sdra64.exe


\### Observations



1\. PID 1384 was loaded with a module that was False in InLoad, InInit and Inmem through windows.ldrmodules.

2\. The URL http://193.104.22.71/~produkt/983745213424/34650798253 is found in its memory through windows.memmapp and strings.

3\. It downloads and executes the malware first as the temporary file e.exe.

4\. It then writes itself as its proper form of sdra64.exe.

5\. e.exe and sdra64.exe have identical SHA-256 hashes (65b493cfea216a900dfc337b0c85451f3c0114dfc9e0eb4dd826d8ed1fca4e5c), indicating that sdra64.exe is a copy of e.exe.



\### Interpretation



The combined observations support the assessment that the hollowed process achieved the initialization of the second stage mechanism.

\### Confidence



High, subject to the limitations described in Section 10.

&#x20;7.3 Finding F-003

\### Finding



The malware then moved to achieve persistence and log user inputs.



\### Evidence



\* E001

\* sdra64.exe

\* Winlogon Userinit value

\* DirectDrawCreateEx kernel hook



\### Observations



1\. The malware achieved persistence by writing itself in the Winlogon Userinit key value.

2\. It created a DirectInput object with the function DirectDrawCreateEx.



\### Interpretation



The combined observations support the assessment that the malware achieved persistence and established an input capture mechanism.

\### Confidence



High, subject to the limitations described in Section 10.




8\. Indicators



No	Type	Value	                                                                            Notes
1	File	pdf.php	                                                                            551147a39bc439f46421a994fdbe6c5bafed90bf3b79121722d95bfa1ea435a1
2	File	pid.1384.vad                                                                        2e7aa9262eac9161d0b122ba374e2576ca8640b6d8aa54d522a3baea0406fd36
3	URL	http://193.104.22.71/~produkt/983745213424/34650798253 	
4	File	e.exe	                                                                            65b493cfea216a900dfc337b0c85451f3c0114dfc9e0eb4dd826d8ed1fca4e5c
5	File	sdra64.exe	                                                                    65b493cfea216a900dfc337b0c85451f3c0114dfc9e0eb4dd826d8ed1fca4e5c
6	RegKey	Userinit (RegSz) C:\WINDOWS\system32\userinit.exe,C:\WINDOWS\system32\sdra64.exe	


9\. Timeline



Event   Source	Description	                        Timestamp	                Notes
1	MFTScan	MFT SI entry for PDF.php	        2010-02-27 20:12:28.2726250 UTC	Entry Point
2   	MFTScan	MFT SI entry for e.exe	  	        2010-02-27 20:12:32.8038750 UTC	Temporary Executable
3	MFTScan	MFT SI entry for sdra64.exe	        2010-02-27 20:12:34.3820000 UTC	Proper Executable
4	RECmd	Last write time for Winlogon hive	2010-02-27 20:12:34.3820000 UTC	Persistence Established



The timeline represents reconstructed activity supported by the available evidence. It should not be interpreted as a complete history of the workstation.


10\. Limitations



The following limitations apply:



1\. Only a memory image was available.

2\. The investigator did not perform the original acquisition.

3\. No disk image was available.

4\. No complete network telemetry was available.

5\. No independent authentication logs were available.

6\. The identity of the person responsible for the observed activity cannot be established from the examined evidence alone.

7\. The registry hive and MFT data were reconstructed from memory and may be incomplete, because some pages may not be resident.



These limitations restrict conclusions regarding the complete scope, persistence, origin, and impact of the activity.



11\. Conclusion



The examination identified artifacts consistent with suspicious activity involving input capture and persistence mechanisms.

After the execution of the malicious pdf, a second stage was initiated which then established the foothold of the malware in the system.

The SHA-256 of sdra64.exe matches a sample that VirusTotal detections label as Zbot (looked up on 2026-10-25; 62 of 73 engines).

This was done through hollowing processes to insert itself, tampering with registry to ensure that it runs on log on, a deep kernel hook for input capture.

The available evidence does not establish the identity of the responsible individual or the full extent of the activity.



12\. Reproducibility



The following materials are retained within the case directory:



\* original evidence;

\* evidence hashes;

\* working copy;

\* derived artifacts;

\* derived-artifact hashes;

\* timeline;

\* IOC list.



Another investigator should be able to reproduce the principal findings using these materials and the documented tool versions and commands.



