---
layout: default
title: wireshark
parent: kb
has_toc: true
---
<details close markdown="block">
  <summary>
    Содержание
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Filters
Search http request wiht string "UNION"
```bash
http.request && http contains "UNION"
```
Search SQLi attempt
```bash
http.request.uri contains "1=1" or http.request.uri matches "(?i)and%20" or http.request.uri matches "(?i)--"
```
Database-specific commands 'UNION SELECT' or 'INFORMATION_SCHEMA'
```bash
http.request.uri contains "INFORMATION_SCHEMA"
```

# Top 100 Wireshark Filters for Investigating Attacks or Hacker Activity

## 1. Basic Traffic Monitoring

- **Traffic to/from a specific IP address:**
  ```wireshark
  ip.addr == x.x.x.x
  ```
  
- **HTTP traffic on port 80:**
  ```wireshark
  tcp.port == 80
  ```

- **DNS traffic:**
  ```wireshark
  udp.port == 53
  ```

- **ICMP (ping) traffic:**
  ```wireshark
  icmp
  ```

- **TCP traffic:**
  ```wireshark
  ip.proto == 6
  ```

- **UDP traffic:**
  ```wireshark
  ip.proto == 17
  ```

- **HTTP requests:**
  ```wireshark
  http.request
  ```

- **HTTP responses:**
  ```wireshark
  http.response
  ```

## 2. Suspicious TCP/UDP Traffic

- **SYN packets (potential scanning activity):**
  ```wireshark
  tcp.flags.syn == 1 and tcp.flags.ack == 0
  ```

- **FIN scans:**
  ```wireshark
  tcp.flags.fin == 1 and tcp.flags.ack == 0
  ```

- **Push flag set (might indicate data exfiltration):**
  ```wireshark
  tcp.flags.psh == 1
  ```

- **TCP retransmissions (could indicate DoS or network issues):**
  ```wireshark
  tcp.analysis.retransmission
  ```

- **Traffic on port 4444 (common for Metasploit):**
  ```wireshark
  tcp.port == 4444
  ```

- **TFTP traffic (can be used for file transfers in attacks):**
  ```wireshark
  udp.port == 69
  ```

- **IKE (potential VPN activity):**
  ```wireshark
  udp.port == 500
  ```

- **NAT-T (VPN traffic):**
  ```wireshark
  udp.port == 4500
  ```

## 3. DNS-Related Activity

- **DNS query for a specific domain:**
  ```wireshark
  dns.qry.name == "malicious.com"
  ```

- **A record DNS queries:**
  ```wireshark
  dns.qry.type == 1
  ```

- **Queries containing suspicious strings:**
  ```wireshark
  dns.qry.name.contains == "suspicious"
  ```

- **Responses with TTL set to 0 (can indicate fast-flux DNS):**
  ```wireshark
  dns.resp.ttl == 0
  ```

- **Queries for a bad domain:**
  ```wireshark
  dns.flags.response == 0 and dns.qry.name == "bad.domain.com"
  ```

- **Truncated DNS packets (could indicate large payloads):**
  ```wireshark
  dns.flags.truncated == 1
  ```

## 4. HTTP/HTTPS Traffic

- **HTTP traffic to/from a specific domain:**
  ```wireshark
  http.host == "malicious.com"
  ```

- **POST requests (used in data exfiltration):**
  ```wireshark
  http.request.method == "POST"
  ```

- **Suspicious user agents:**
  ```wireshark
  http.user_agent contains "SuspiciousUserAgent"
  ```

- **Session cookies:**
  ```wireshark
  http.cookie contains "sessionID"
  ```

- **TLS client hello (can be useful to identify SSL/TLS versions):**
  ```wireshark
  tls.handshake.type == 1
  ```

- **Encrypted application data (can indicate hidden data):**
  ```wireshark
  tls.record.content_type == 23
  ```

- **Possible command injection via URI:**
  ```wireshark
  http.request.uri contains "cmd.exe"
  ```

## 5. FTP/SMB/Other Protocols

- **FTP login attempts:**
  ```wireshark
  ftp.request.command == "USER"
  ```

- **FTP file uploads:**
  ```wireshark
  ftp.request.command == "STOR"
  ```

- **All SMB traffic (useful for identifying lateral movement):**
  ```wireshark
  smb
  ```

- **SMB2 session setup (potential lateral movement):**
  ```wireshark
  smb2.cmd == 5
  ```

- **Access to specific SMB shares:**
  ```wireshark
  smb2.tree == "\\malicious\share"
  ```

- **NTLM authentication attempts with specific usernames:**
  ```wireshark
  ntlmssp.auth.username == "admin"
  ```

- **Kerberos traffic for administrator accounts:**
  ```wireshark
  kerberos.CNameString == "administrator"
  ```

## 6. Email Traffic (SMTP/POP3/IMAP)

- **SMTP parameters containing suspicious strings:**
  ```wireshark
  smtp.req.parameter contains "malicious"
  ```

- **Capturing the data part of an email:**
  ```wireshark
  smtp.req.command == "DATA"
  ```

- **Email recipient addresses:**
  ```wireshark
  smtp.req.parameter == "RCPT TO"
  ```

- **POP3 login attempts:**
  ```wireshark
  pop.request.command == "USER"
  ```

- **Specific usernames in POP3:**
  ```wireshark
  pop.request.parameter contains "admin"
  ```

- **IMAP login attempts:**
  ```wireshark
  imap.request.command == "LOGIN"
  ```

- **Emails from specific addresses:**
  ```wireshark
  smtp.mailfrom == "attacker@example.com"
  ```

## 7. ARP/ICMP/DHCP

- **Duplicate ARP addresses (potential MITM):**
  ```wireshark
  arp.duplicate-address-frame
  ```

- **ARP requests from a specific IP:**
  ```wireshark
  arp.src.proto_ipv4 == x.x.x.x
  ```

- **ICMP Echo Requests (pings):**
  ```wireshark
  icmp.type == 8 and icmp.code == 0
  ```

- **Destination unreachable (could indicate scanning):**
  ```wireshark
  icmp.type == 3
  ```

- **DHCP requests for specific IPs:**
  ```wireshark
  dhcp.option.requested_ip_address == x.x.x.x
  ```

- **All DHCP traffic:**
  ```wireshark
  dhcp
  ```

## 8. VPN/Encrypted Traffic

- **IPsec ESP traffic:**
  ```wireshark
  ip.addr == x.x.x.x and ip.proto == 50
  ```

- **OpenVPN traffic:**
  ```wireshark
  udp.port == 1194
  ```

- **GRE traffic (potential VPN):**
  ```wireshark
  gre
  ```

- **SSL/TLS Client Hello:**
  ```wireshark
  ssl.handshake.type == 1
  ```

- **SSL/TLS SNI field:**
  ```wireshark
  ssl.handshake.extensions_server_name == "vpn.example.com"
  ```

## 9. Indicators of Compromise

- **Traffic to/from known bad IPs:**
  ```wireshark
  ip.addr == bad_ip
  ```

- **HTTP traffic to known malicious domains:**
  ```wireshark
  http.host == "knownmalicious.com"
  ```

- **Attempts to access WordPress login pages:**
  ```wireshark
  http.request.uri contains "/wp-login.php"
  ```

- **Accessing potential web shells:**
  ```wireshark
  http.request.uri contains "/shell"
  ```

- **DNS queries for C2 servers:**
  ```wireshark
  dns.qry.name == "command-and-control.com"
  ```

- **Suspicious traffic on non-standard ports:**
  ```wireshark
  tcp.port == 1337
  ```

- **Traffic to/from specific countries:**
  ```wireshark
  ip.geoip.country == "CN"
  ```

- **Traffic with low TTL values (potential indication of IP spoofing):**
  ```wireshark
  ip.ttl == 1
  ```

## 10. File Transfer Protocols

- **TFTP traffic (often used for transferring files):**
  ```wireshark
  tftp
  ```

- **FTP data transfer:**
  ```wireshark
  ftp-data
  ```

- **NFS traffic:**
  ```wireshark
  nfs
  ```

- **TFTP file transfers with suspicious filenames:**
  ```wireshark
  tftp.request.filename contains "malware"
  ```

## 11. Malformed/Bogus Packets

- **Malformed IP packets:**
  ```wireshark
  ip.len < 20
  ```

- **Malformed TCP headers:**
  ```wireshark
  tcp.hdr_len < 20
  ```

- **Broadcast traffic (often

 used in network scanning):**
  ```wireshark
  eth.dst == ff:ff:ff:ff:ff:ff
  ```

- **TCP flags analysis (captures potential anomalies):**
  ```wireshark
  tcp.analysis.flags
  ```

- **Redirect (could be part of a routing attack):**
  ```wireshark
  icmp.type == 5
  ```

- **Non-standard DNS response codes:**
  ```wireshark
  dns.flags.rcode != 0
  ```

## 12. High Traffic Volume

- **Large packets:**
  ```wireshark
  frame.len > 1000
  ```

- **Zero window size (potential signs of DoS):**
  ```wireshark
  tcp.window_size == 0
  ```

- **Traffic to/from specific IPs with large time gaps:**
  ```wireshark
  ip.addr == x.x.x.x and frame.time_delta > 1
  ```

- **Long TCP streams:**
  ```wireshark
  tcp.stream eq 0 and frame.time_relative > 60
  ```

## 13. Others

- **Captures MSS options in TCP (can identify unusual setups):**
  ```wireshark
  tcp.options.mss
  ```

- **TCP SACK options (can identify specific attack vectors):**
  ```wireshark
  tcp.options.sack_perm == 1
  ```

- **Fragmented IP traffic (often used in evasion techniques):**
  ```wireshark
  ip.frag_offset > 0
  ```

- **Traffic from specific MAC addresses:**
  ```wireshark
  eth.src == xx:xx:xx:xx:xx:xx
  ```

- **Traffic to specific MAC addresses:**
  ```wireshark
  eth.dst == xx:xx:xx:xx:xx:xx
  ```

- **Source country (can be filtered based on geographic info):**
  ```wireshark
  ip.geoip.src_country == "RU"
  ```

- **Destination country (same as above):**
  ```wireshark
  ip.geoip.dst_country == "RU"
  ```

- **TLSv1.2 traffic:**
  ```wireshark
  ssl.record.version == 0x0303
  ```

- **TLSv1.0 traffic:**
  ```wireshark
  ssl.record.version == 0x0301
  ```

- **Specific cipher suites:**
  ```wireshark
  ssl.handshake.ciphersuite == 0xcca9
  ```

- **TCP SYN/ACK packets:**
  ```wireshark
  tcp.flags.syn == 1 and tcp.flags.ack == 1
  ```

## General Wireshark Filters

1. **Filter by IP Address (Source)**
   ```plaintext
   ip.src == 192.168.1.1
   ```

2. **Filter by IP Address (Destination)**
   ```plaintext
   ip.dst == 192.168.1.1
   ```

3. **Filter by Protocol**
   ```plaintext
   tcp
   ```

4. **Filter by Port (TCP)**
   ```plaintext
   tcp.port == 80
   ```

5. **Filter by Port (UDP)**
   ```plaintext
   udp.port == 53
   ```

6. **Filter by Subnet**
   ```plaintext
   ip.addr == 192.168.1.0/24
   ```

7. **Filter by HTTP Requests**
   ```plaintext
   http.request
   ```

8. **Filter by DNS Traffic**
   ```plaintext
   dns
   ```

9. **Filter by ARP Traffic**
   ```plaintext
   arp
   ```

10. **Filter by ICMP (Ping) Traffic**
    ```plaintext
    icmp
    ```

11. **Filter by SSL/TLS Traffic**
    ```plaintext
    ssl or tls
    ```

12. **Filter by DHCP Traffic**
    ```plaintext
    bootp
    ```

13. **Filter by TCP SYN Packets**
    ```plaintext
    tcp.flags.syn == 1
    ```

14. **Filter by TCP SYN-ACK Packets**
    ```plaintext
    tcp.flags == 0x12
    ```

15. **Filter by TCP FIN Packets**
    ```plaintext
    tcp.flags.fin == 1
    ```

16. **Filter by Packet Size**
    ```plaintext
    frame.len > 1000
    ```

17. **Filter by MAC Address (Source)**
    ```plaintext
    eth.src == 00:11:22:33:44:55
    ```

18. **Filter by MAC Address (Destination)**
    ```plaintext
    eth.dst == 00:11:22:33:44:55
    ```

19. **Filter by TCP RST Packets**
    ```plaintext
    tcp.flags.rst == 1
    ```

20. **Filter by TCP Window Size**
    ```plaintext
    tcp.window_size == 0
    ```

21. **Filter by Packet Containing a String**
    ```plaintext
    frame contains "example"
    ```

22. **Filter by VLAN ID**
    ```plaintext
    vlan.id == 10
    ```

23. **Filter by Specific Hostname in DNS**
    ```plaintext
    dns.qry.name == "example.com"
    ```

24. **Filter by HTTP Response Code**
    ```plaintext
    http.response.code == 200
    ```

25. **Filter by IP Address Range**
    ```plaintext
    ip.addr >= 192.168.1.1 and ip.addr <= 192.168.1.10
    ```

26. **Filter by TCP Sequence Number**
    ```plaintext
    tcp.seq == 1000
    ```

27. **Filter by User-Agent in HTTP**
    ```plaintext
    http.user_agent contains "Mozilla"
    ```

28. **Filter by TCP Retransmissions**
    ```plaintext
    tcp.analysis.retransmission
    ```

29. **Filter by TCP Duplicate ACKs**
    ```plaintext
    tcp.analysis.duplicate_ack
    ```

30. **Filter by ICMP Echo Request**
    ```plaintext
    icmp.type == 8
    ```
31. **Filter upload file**
    ```plaintext
    mime_multipart.type == "multipart/form-data" && http.request.method == POST
    ```

