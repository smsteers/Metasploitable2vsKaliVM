# Reconnaissance & Scanning

## Target
- **IP:** 192.168.56.102 (Metasploitable2)
- **Network:** Isolated VirtualBox Host-only network (192.168.56.0/24)

## Objective
Enumerate open ports on the target and identify the services and
software versions running on each, in order to prioritize which
vulnerabilities to investigate further.

## Tools Used
- Nmap 7.99

## Methodology
A service/version detection scan was run against the target to enumerate
open ports and fingerprint the software running behind them:

```
nmap -sV 192.168.56.102
```

The `-sV` flag was used specifically to pull version information, since
exploitability is almost always tied to a specific software version
rather than just an open port.

## Findings

21 open ports were discovered. The scan is summarized below, with the
most significant findings highlighted:

| Port | Service | Version | Notes |
|---|---|---|---|
| 21/tcp | ftp | **vsftpd 2.3.4** | Known backdoored version |
| 22/tcp | ssh | OpenSSH 4.7p1 Debian 8ubuntu1 | Outdated |
| 23/tcp | telnet | Linux telnetd | Unencrypted protocol |
| 25/tcp | smtp | Postfix smtpd | |
| 53/tcp | domain | ISC BIND 9.4.2 | |
| 80/tcp | http | Apache httpd 2.2.8 (Ubuntu) | Outdated |
| 111/tcp | rpcbind | | |
| 139/445/tcp | netbios-ssn | **Samba smbd 3.X–4.X** | Historically vulnerable version range |
| 512–514/tcp | exec/login/shell | rlogin-family services | Legacy, insecure by design |
| 1099/tcp | java-rmi | GNU Classpath grmiregistry | |
| **1524/tcp** | shell | **Metasploitable root shell** | Open, unauthenticated shell |
| 2049/tcp | nfs | | |
| 2121/tcp | ftp | ProFTPD 1.3.1 | |
| 3306/tcp | mysql | MySQL 5.0.51a-3ubuntu5 | |
| 5432/tcp | postgresql | PostgreSQL DB 8.3.0–8.3.7 | |
| 5900/tcp | vnc | VNC (protocol 3.3) | |
| **6667/tcp** | irc | **UnrealIRCd** | Known backdoored version |
| 8009/tcp | ajp13 | Apache Jserv | |
| 8180/tcp | http | Apache Tomcat/Coyote JSP engine 1.1 | Default credentials risk |

## Prioritization

Of the 21 discovered services, two stood out as immediate, high-value
targets due to publicly known backdoors requiring no credential
guessing or complex exploit development:

1. **vsftpd 2.3.4** (port 21) — contains a well-documented backdoor
   triggered via a malformed username
2. **UnrealIRCd** (port 6667) — a historically backdoored release
   allowing remote command execution

Both were selected for the exploitation phase documented in
`02-exploitation/writeup.md`.

## Next Steps
Proceed to exploitation phase, beginning with the vsftpd backdoor as the
initial foothold, followed by the UnrealIRCd backdoor as a secondary,
independent attack path.
