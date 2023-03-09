---
title: Windows ports
date: 2023-03-08 14:01:10 +0300
categories: [microsoft, ad]
tags: [ad]     # TAG names should always be lowercase
#img_path: /img/
---
# List of ports
| Port | Service                |   |   |   |
|---------|----------------------------|---|---|---|
| 53     | служба DNS |   |   |   |
| 80     | (HTTP) — веб-сервер Microsoft IIS/10.0 |   |   |   |
| 88     | служба Kerberos |   |   |   |
| 135     | служба удаленного вызова процедур (Microsoft RPC). Используется для взаимодействия контроллер — контроллер и контроллер — клиент |   |   |   |
| 139     | служба сеансов NetBIOS, NetLogon |   |   |   |
| 389     | служба LDAP |   |   |   |
| 445     | служба SMB |   |   |   |
| 464     | служба смены пароля Kerberos |   |   |   |
| 593     | (HTTP-RPC-EPMAP) — используется в службах DCOM и MS Exchange |   |   |   |
| 636     | LDAP с шифрованием SSL или TLS |   |   |   |
| 3268     | LDAP — для доступа к Global Catalog от клиента к контроллеру |   |   |   |
| 3269     | LDAPS — для доступа к Global Catalog от клиента к контроллеру через защищенное соединение |   |   |   |
| 5985     | службаудаленногоуправления(WinRM) |   |   |   |
| 9389     | веб-службы AD DS |   |   |   |

    
Port 53: This is used for DNS (Domain Name System), which is used to translate domain names into IP addresses.

Port 80: This is used for HTTP (Hypertext Transfer Protocol), which is used for web servers to deliver web pages.

Port 88: This is used for Kerberos, which is a network authentication protocol.

Port 135: This is used for Microsoft RPC (Remote Procedure Call), which is used for communication between computers.

Port 139: This is used for NetBIOS (Network Basic Input/Output System) sessions, which is used for communication between computers on a local network.

Port 389: This is used for LDAP (Lightweight Directory Access Protocol), which is used to access and manage directory information.

Port 445: This is used for SMB (Server Message Block), which is used for file sharing and printer access on a local network.

Port 464: This is used for Kerberos password change requests.

Port 593: This is used for HTTP-RPC-EPMAP (Endpoint Mapper), which is used by DCOM (Distributed Component Object Model) and Microsoft Exchange.

Port 636: This is used for LDAP with SSL/TLS encryption.

Port 3268: This is used for LDAP to access the Global Catalog from a client to a domain controller.

Port 3269: This is used for LDAPS (LDAP with SSL/TLS) to access the Global Catalog from a client to a domain controller over a secure connection.

Port 5985: This is used for WinRM (Windows Remote Management), which is used for remote management of Windows computers.

Port 9389: This is used for web services for Active Directory Domain Services.

Port 22 - SSH (Secure Shell): This port is used for secure remote access to a system. It is important to monitor this port as it can be used for unauthorized access if left unsecured.

Port 80/443 - HTTP/HTTPS: These ports are used for web traffic. They should be monitored to prevent web-based attacks such as SQL injection and cross-site scripting.

Port 25 - SMTP (Simple Mail Transfer Protocol): This port is used for email traffic. It should be monitored to prevent unauthorized email relay or spamming.

Port 3389 - Remote Desktop Protocol (RDP): This port is used for remote access to a system. It is important to monitor this port as it can be used for unauthorized access if left unsecured.

Port 1433 - Microsoft SQL Server: This port is used for Microsoft SQL Server traffic. It should be monitored to prevent SQL injection attacks.

Port 53 - DNS (Domain Name System): This port is used for DNS traffic. It should be monitored to prevent DNS spoofing or cache poisoning attacks.

Port 161/162 - SNMP (Simple Network Management Protocol): These ports are used for network management traffic. It should be monitored to prevent unauthorized access to network devices.