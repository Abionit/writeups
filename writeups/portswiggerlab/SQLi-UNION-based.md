# SQL injection with filter bypass via XML encoding

**Author:** Miguel Torres 
**Date:** 2025-11-07  
**Platform:** PortSwigger Web Security Academy

## Short description
SQLi vulnerability in `POST /product/stock` where the `<storeId>` parameter (sent in XML) is evaluated by the server. A WAF blocks direct payloads; it is bypassed by encoding the payload as XML entities (e.g., Hackvertor `hex_entities`) to perform a **UNION-based SQLi** that returns `username~password` from the `users` table.

## Attack type
**In-band SQL Injection — UNION-based.**  
(WAF bypass via XML entity encoding; DBMS appears to support `||` for concatenation.)

## Summary of steps
1. Send a baseline `POST /product/stock` with `<storeId>1</storeId>` to confirm behavior. 


2. Test evaluation with `<storeId>1+1</storeId>` → the app evaluates expressions.  

<img width="1343" height="706" alt="image" src="https://github.com/user-attachments/assets/b882591f-cc34-45eb-8ac8-94361ae8f2c3" />

3. Attempt a direct `UNION SELECT` and observe it being blocked by the WAF.  

<img width="1324" height="710" alt="image" src="https://github.com/user-attachments/assets/ad9e4b81-3269-4c5d-9c3d-c0fcd707f18b" />


4. Encode the payload using XML entities (Hackvertor: `Encode -> hex_entities`) to bypass the filter.  

<img width="1339" height="710" alt="image" src="https://github.com/user-attachments/assets/f17878f0-67a8-41a7-9a5d-e8d5be516b14" />


5. Use a concatenated payload for a single-column result: `1 UNION SELECT username || '~' || password FROM users` (encoded).  

<img width="1347" height="713" alt="image" src="https://github.com/user-attachments/assets/725cca87-22a4-423c-b4ea-ed56e8db545e" />


6. Extract the admin row (`username~password`) and use the credentials to log in.

administrator~7wgfkp4g0psyz5xugx38

<img width="1345" height="631" alt="image" src="https://github.com/user-attachments/assets/d258038e-c29e-49c5-bdae-19e02d0ab56e" />


completed!
