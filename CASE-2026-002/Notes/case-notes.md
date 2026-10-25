\# CASE-002



Case opened:

2026-10-02 13:45 CEST



Investigator:

Ioannis



Case type:

Suspected endpoint compromise



Scope:

Reconstruct an attack timeline by analyzing disk images, event logs, and malicious scripts to identify initial access, persistence, and data exfiltration techniques.



Evidence received:

E001 - Windows 10 workstation



Initial observations:

\- This morning, the network monitoring system flagged unusual outbound traffic patterns from several workstations.

\- Preliminary analysis by the IT department has identified a potential compromise linked to an exploited vulnerability in WinRAR software.



Questions:

1\. WHO

2\. WHAT

3\. WHEN

4\. HOW

5\. WHY
6. WHERE



Status:

Analysis pending.



ANALYSIS



DESKTOP-2R3AR22	 - Windows 10 Pro

One Account - Administrator

Use of Telegram noted



Trojan C/Users/Administrator/Downloads/Telegram Desktop/SANS SEC401.rar/SANS SEC401.pdf /SANS SEC401.pdf.cmd

Second Stage /C/Windows/Temp/run.bat
Third Stage vol\_vol2/C/Windows/Temp/run.ps1



Log files were cleared at 2/3/2024 8:38:01 AM (Known Behavior of malware)



Both run.bat (08:29:20.000000000) and run.ps1 (00:33:00.000000000) show zeroed-out nanoseconds in their $STANDARD\_INFORMATION modification fields. This confirms the deployment utility used automated API backdating to disguise the payload files.



SANS SEC401.pdf.cmd

&#x20;         │ downloads

&#x20;         ▼

amanwhogetsnorest.jpg

&#x20;         │

&#x20;         │ certutil -decode

&#x20;         ▼

&#x20;     normal.zip

&#x20;         │

&#x20;         ├──────────────┬───────────────┐

&#x20;         ▼              ▼               ▼

&#x20;     run.bat        run.ps1       Eventlogs.ps1

&#x20;         │              │               │

&#x20;         │              │               └─ event-log tampering

&#x20;         │              │

&#x20;         │              └─ scans 192.168.1.1–99

&#x20;         │                   │

&#x20;         │                   ▼

&#x20;         │              BL4356.txt

&#x20;         │                   │

&#x20;         │                   ▼

&#x20;         │              HTTP → 192.168.1.5:8000

&#x20;         │

&#x20;         └─ runs run.ps1

&#x20;            every 3 minutes

&#x20;            via scheduled task (whoisthebaba)

