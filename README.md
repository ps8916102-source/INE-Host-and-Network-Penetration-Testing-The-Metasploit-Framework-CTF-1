# INE Host & Network Penetration Testing: The Metasploit Framework CTF 1

## Overview

This repository documents my learning process while completing the **Host & Network Penetration Testing: The Metasploit Framework CTF 1** lab on INE.

The walkthrough covers service enumeration, Microsoft SQL Server investigation, a Metasploit exploit leading to a Meterpreter session, privilege escalation, and manual Windows filesystem investigation.

> **Note to readers:** This repository documents my personal learning journey. Don't just copy the commands—try the lab yourself, understand the process, and learn from your mistakes. The goal is to build skills, not just capture flags.

## Lab Environment

- **Platform:** INE
- **Target hostname:** `target.ine.local`
- **Target OS:** Windows Server
- **Objective:** Investigate the exposed services and locate four lab flags.

## Tools Used

- Nmap
- Metasploit Framework
- Meterpreter
- Windows Command Prompt

## Key Findings

1. Nmap identified Microsoft SQL Server on TCP port `1433`.
2. Metasploit's `exploit/windows/mssql/mssql_clr_payload` module established a Meterpreter session in the lab.
3. The initial session encountered an access-denied error when entering the Windows configuration directory.
4. `getprivs` and `getsystem` were used to inspect and elevate the session's privileges in the lab.
5. Manual file enumeration located flag files in the root of `C:\`, the Windows configuration directory, a Windows System32 hint file, and the Administrator desktop.

## Repository Contents

- `README.md` — overview, objectives, tools, and key findings.
- `lab-notes.md` — detailed commands, explanations, and the step-by-step methodology.

## Responsible Use

This write-up is based on an authorized training environment. Flag values are redacted. Always obtain permission before testing systems and review INE's current publication rules before sharing detailed lab solutions publicly.
