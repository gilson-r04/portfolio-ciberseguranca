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


