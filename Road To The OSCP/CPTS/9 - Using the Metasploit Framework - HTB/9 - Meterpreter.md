
El `Meterpreter` La carga útil es un tipo específico de carga útil extensible y multifacética que utiliza `DLL injection`para garantizar que la conexión con el host de la víctima sea estable y difícil de detectar mediante comprobaciones simples y se puede configurar para que sea persistente durante reinicios o cambios en el sistema.Además, Meterpreter reside completamente en la memoria del host remoto y no deja rastros en el disco duro, lo que dificulta su detección con técnicas forenses convencionales. 

```js
msf6 exploit(linux/local/sudo_baron_samedit) > db_nmap -sCV  10.129.203.65
[*] Nmap: Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-24 19:48 EDT
[*] Nmap: Stats: 0:00:24 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
[*] Nmap: Connect Scan Timing: About 55.28% done; ETC: 19:49 (0:00:19 remaining)
[*] 10.129.203.52 - Meterpreter session 1 closed.  Reason: Died
[*] Nmap: Nmap scan report for 10.129.203.65 (10.129.203.65)
[*] Nmap: Host is up (0.44s latency).
[*] Nmap: Not shown: 995 closed tcp ports (conn-refused)
[*] Nmap: PORT     STATE SERVICE       VERSION
[*] Nmap: 135/tcp  open  msrpc         Microsoft Windows RPC
[*] Nmap: 139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp  open  microsoft-ds?
[*] Nmap: 3389/tcp open  ms-wbt-server Microsoft Terminal Services
[*] Nmap: |_ssl-date: 2024-06-24T23:49:31+00:00; 0s from scanner time.
[*] Nmap: | rdp-ntlm-info:
[*] Nmap: |   Target_Name: WIN-51BJ97BCIPV
[*] Nmap: |   NetBIOS_Domain_Name: WIN-51BJ97BCIPV
[*] Nmap: |   NetBIOS_Computer_Name: WIN-51BJ97BCIPV
[*] Nmap: |   DNS_Domain_Name: WIN-51BJ97BCIPV
[*] Nmap: |   DNS_Computer_Name: WIN-51BJ97BCIPV
[*] Nmap: |   Product_Version: 10.0.17763
[*] Nmap: |_  System_Time: 2024-06-24T23:49:22+00:00
[*] Nmap: | ssl-cert: Subject: commonName=WIN-51BJ97BCIPV
[*] Nmap: | Not valid before: 2024-06-23T23:40:16
[*] Nmap: |_Not valid after:  2024-12-23T23:40:16
[*] Nmap: 5000/tcp open  http          Microsoft IIS httpd 10.0
[*] Nmap: |_http-server-header: Microsoft-IIS/10.0
[*] Nmap: |_http-title: FortiLogger | Log and Report System
[*] Nmap: | http-methods:
[*] Nmap: |_  Potentially risky methods: TRACE
[*] Nmap: Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
[*] Nmap: Host script results:
[*] Nmap: | smb2-time:
[*] Nmap: |   date: 2024-06-24T23:49:26
[*] Nmap: |_  start_date: N/A
[*] Nmap: | smb2-security-mode:
[*] Nmap: |   3:1:1:
[*] Nmap: |_    Message signing enabled but not required
[*] Nmap: Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 70.76 seconds
msf6 exploit(linux/local/sudo_baron_samedit) > hosts

Hosts
=====

address        mac  name           os_name  os_flavor           os_sp  purpose  info  comments
-------        ---  ----           -------  ---------           -----  -------  ----  --------
10.129.203.52       10.129.203.52  ubuntu   Ubuntu 20.04.4 LTS         server
10.129.203.65       10.129.203.65  Unknown                             device

msf6 exploit(linux/local/sudo_baron_samedit) > services
Services
========

host           port  proto  name           state  info
----           ----  -----  ----           -----  ----
10.129.203.65  135   tcp    msrpc          open   Microsoft Windows RPC
10.129.203.65  139   tcp    netbios-ssn    open   Microsoft Windows netbios-ssn
10.129.203.65  445   tcp    microsoft-ds   open
10.129.203.65  3389  tcp    ms-wbt-server  open   Microsoft Terminal Services
10.129.203.65  5000  tcp    http           open   Microsoft IIS httpd 10.0
```

