# Voleur — Full walkthrough (HTB, Retired, Medium)

**Autor:** Miguel Ángel Torres Mercado  
**Fecha:** 2025-11-07  
**Plataforma:** Hack The Box — Retired  
**Disclaimer:** Lab retired — publicación permitida. Este documento es sólo para fines educativos. No reutilizar credenciales reales fuera de entornos autorizados.

---

## TL;DR
Partimos de credenciales de bajo privilegio (`ryan.naylor:HollowOct31Nyt`), enumeramos SMB, encontramos `Access_Review.xlsx` (protegido), extraemos y rompemos su contraseña (`football1`), obtenemos credenciales de servicio (svc_ldap, svc_iis), usamos `WriteSPN` para realizar un **Kerberoast dirigido** y obtener `svc_winrm` → shell WinRM (user flag). Con privilegios adicionales restauramos el usuario `todd.wolfe`, extraemos y desciframos blobs DPAPI para recuperar `jeremy.combs` creds → encontramos `id_rsa` → SSH a WSL en puerto `2222` como `svc_backup` → obtenemos `NTDS.dit` + `SYSTEM` + `SECURITY` → extraemos hash de `Administrator` → pass-the-hash y root (root.txt).

---

## Entorno / placeholders
Sustituye los placeholders por tus valores:
- `TARGET_IP` — IP objetivo
- `DOMAIN` — `voleur.htb`
- `DC_HOST` — `dc.voleur.htb`
- Usuarios y contraseñas mencionados en el writeup ya están en el texto (son del lab).

---

## Herramientas principales
`nmap`, `enum4linux`, `smbclient`, `smbmap`, Impacket (`GetUserSPNs.py`, `secretsdump.py`), `office2john.py` + `john`/`hashcat`, `mimikatz`/`dpapick.py`, `evil-winrm`, `ssh`, `scp`.

---

## 1 — Introducción y Reconocimiento inicial
- Máquina: Domain Controller (hostname `DC`, domain `voleur.htb`).  
- Credenciales iniciales (assume breach):  
  `ryan.naylor : HollowOct31Nyt`  
- Nota: NTLM deshabilitado → usar Kerberos. Sincroniza hora si es necesario: `sudo ntpdate dc.voleur.htb`.

**Nmap (ejemplo)**  
```bash
nmap -sC -sV -p- -oN nmap_full.txt TARGET_IP
