# PortSwigger Labs

Write-ups for PortSwigger Web Security Academy labs.

Each note focuses on vulnerability analysis, proof-of-concept reasoning, impact, and remediation awareness.

## Available Write-Ups

### [In-band SQLi UNION-based - XML Entity Encoding WAF Bypass](writeups/portswiggerlab/SQLi-UNION-based.md)

- Vulnerability: in-band UNION-based SQL injection in an XML `storeId` parameter
- Bypass method: XML entity encoding using Hackvertor
- Outcome: concatenated username and password extraction, followed by validation through the lab workflow
- Key techniques: UNION SQL injection, XML entity encoding, single-column concatenation, Burp Repeater validation
- Status: completed

## Review Focus

These notes are written to show how a web vulnerability is identified, tested, explained, and documented in a controlled environment.
