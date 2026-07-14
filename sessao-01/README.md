Sessão 01 — Introdução ao Linux para Segurança e Comandos de Rede

Objetivo

Mapear e analisar a superfície de exposição de um servidor alvo, identificando a interface de rede local, serviços em escuta e serviços disponíveis numa máquina remota através do Nmap.

Ambiente

- KillerCoda Ubuntu Playground
- TryHackMe — Further Nmap

 ##1. Identificação da interface de rede

Comando utilizado:

```bash
ip a
resultado:
root@ubuntu:~$ ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc fq_codel state UP group default qlen 1000
    link/ether ba:f4:7a:96:65:b0 brd ff:ff:ff:ff:ff:ff
    inet 172.30.1.2/24 brd 172.30.1.255 scope global dynamic noprefixroute enp1s0
       valid_lft 86310331sec preferred_lft 75521131sec
    inet6 fe80::4b05:e5ae:876:dcf9/64 scope link 
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1454 qdisc noqueue state DOWN group default 
    link/ether 42:88:fa:3b:a7:d0 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
</> bash
ss -tuln
root@ubuntu:~$ ss -tuln
Netid    State     Recv-Q    Send-Q                           Local Address:Port          Peer Address:Port    Process    
udp      UNCONN    0         0                                   127.0.0.54:53                 0.0.0.0:*                  
udp      UNCONN    0         0                                127.0.0.53%lo:53                 0.0.0.0:*                  
udp      UNCONN    0         0                                   172.30.1.2:68                 0.0.0.0:*                  
udp      UNCONN    0         0                            172.30.1.2%enp1s0:68                 0.0.0.0:*                  
udp      UNCONN    0         0            [fe80::4b05:e5ae:876:dcf9]%enp1s0:546                   [::]:*                  
tcp      LISTEN    0         4096                             127.0.0.53%lo:53                 0.0.0.0:*                  
tcp      LISTEN    0         4096                                   0.0.0.0:22                 0.0.0.0:*                  
tcp      LISTEN    0         511                                    0.0.0.0:40205              0.0.0.0:*                  
tcp      LISTEN    0         128                                    0.0.0.0:40200              0.0.0.0:*                  
tcp      LISTEN    0         4096                                 127.0.0.1:40435              0.0.0.0:*                  
tcp      LISTEN    0         4096                                127.0.0.54:53                 0.0.0.0:*                  
tcp      LISTEN    0         4096                                      [::]:22                    [::]:*                  
tcp      LISTEN    0         4096                                         *:40300                    *:*                  
tcp      LISTEN    0         4096                                         *:40305                    *:*                  


nmap -sV -sC 10.129.136.147
root@ip-10-129-136-147:~# nmap -sV -sC 10.129.136.147
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-07-14 20:40 UTC
Nmap scan report for ip-10-129-136-147.eu-west-3.compute.internal (10.129.136.147)
Host is up (0.000014s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE    SERVICE       VERSION
22/tcp   open     ssh           OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3d:1f:a5:e7:cd:31:d7:04:05:90:55:b4:49:13:9c:27 (ECDSA)
|_  256 b9:c7:43:62:c1:a5:be:43:9d:85:2f:a7:2c:e2:f8:7f (ED25519)
53/tcp   open     domain        dnsmasq 2.90
| dns-nsid: 
|_  bind.version: dnsmasq-2.90
139/tcp  open     netbios-ssn   Samba smbd 4.6.2
445/tcp  open     netbios-ssn   Samba smbd 4.6.2
7777/tcp filtered cbt
7778/tcp filtered interwise
8443/tcp open     ssl/https-alt dcv
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 400 Bad Request
|     Server: dcv
|     Date: Tue, 14 Jul 2026 20:41:07 GMT
|     Content-Length: 0
|   RTSPRequest, SIPOptions: 
|     HTTP/1.0 400 Bad Request
|     Server: dcv
|_    Content-Length: 0
|_http-title: Amazon DCV
|_http-server-header: dcv
| ssl-cert: Subject: commonName=ip-172-31-39-192/countryName=US
| Subject Alternative Name: IP Address:172.31.39.192
| Not valid before: 2026-02-16T14:25:51
|_Not valid after:  2027-02-16T14:25:51
|_ssl-date: TLS randomness does not represent time
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8443-TCP:V=7.94SVN%T=SSL%I=7%D=7/14%Time=6A569EE3%P=x86_64-pc-linux
SF:-gnu%r(GetRequest,61,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nServer:\x20
SF:dcv\r\nDate:\x20Tue,\x2014\x20Jul\x202026\x2020:41:07\x20GMT\r\nContent
SF:-Length:\x200\r\n\r\n")%r(HTTPOptions,61,"HTTP/1\.0\x20400\x20Bad\x20Re
SF:quest\r\nServer:\x20dcv\r\nDate:\x20Tue,\x2014\x20Jul\x202026\x2020:41:
SF:07\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r(FourOhFourRequest,61,"HTT
SF:P/1\.0\x20400\x20Bad\x20Request\r\nServer:\x20dcv\r\nDate:\x20Tue,\x201
SF:4\x20Jul\x202026\x2020:41:07\x20GMT\r\nContent-Length:\x200\r\n\r\n")%r
SF:(RTSPRequest,3C,"HTTP/1\.0\x20400\x20Bad\x20Request\r\nServer:\x20dcv\r
SF:\nContent-Length:\x200\r\n\r\n")%r(SIPOptions,3C,"HTTP/1\.0\x20400\x20B
SF:ad\x20Request\r\nServer:\x20dcv\r\nContent-Length:\x200\r\n\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-time: 
|   date: 2026-07-14T20:43:08
|_  start_date: N/A
|_nbstat: NetBIOS name: , NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 142.04 seconds
root@ip-10-129-136-147:~# 

<img width="1440" height="900" alt="Captura de ecrã 2026-07-14, às 19 35 31" src="https://github.com/user-attachments/assets/7e131ce1-7823-4853-95c5-b65dc3f73420" />
<img width="1440" height="900" alt="Captura de ecrã 2026-07-14, às 19 41 29" src="https://github.com/user-attachments/assets/0a37307b-9362-4013-8cc3-78f8e85cb5ac" />
<img width="1440" height="900" alt="Captura de ecrã 2026-07-14, às 19 45 05" src="https://github.com/user-attachments/assets/08505b69-fcd1-468c-b89b-6156a3ff4342" />
<img width="1440" height="900" alt="Captura de ecrã 2026-07-14, às 19 45 11" src="https://github.com/user-attachments/assets/45954aed-d451-410d-a3d7-9b616e24bcaf" />
