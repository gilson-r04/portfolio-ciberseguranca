# Sessão 01 — Introdução ao Linux para Segurança e Comandos de Rede

## Objetivo

O objetivo desta sessão foi mapear e analisar a superfície de exposição de um servidor alvo, identificando a interface de rede local, os serviços em escuta e os serviços disponíveis numa máquina remota através da ferramenta **Nmap**.

---

# Ambiente Virtual

- **KillerCoda Ubuntu Playground** – Utilizado para executar comandos Linux e analisar a interface de rede local.
- **TryHackMe – Further Nmap** – Utilizado para realizar o scan de reconhecimento da máquina alvo com o Nmap.

---

# 1. Identificação da Interface de Rede

## Comando utilizado

```bash
ip a
```

## Output

```text
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
```

### Análise

A interface de rede principal identificada foi **enp1s0**, com o endereço IPv4 **172.30.1.2/24**.

---

# 2. Serviços em Escuta no Ambiente Local

## Comando utilizado

```bash
ss -tuln
```

## Output

```text
root@ubuntu:~$ ss -tuln

Netid    State     Recv-Q    Send-Q                           Local Address:Port          Peer Address:Port
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
```

### Análise

O comando `ss -tuln` permitiu identificar os serviços que estavam em estado de escuta (LISTEN) na máquina local, incluindo o serviço SSH na porta 22 e outros serviços internos do ambiente de laboratório.

---

# 3. Análise da Máquina Alvo com Nmap

## Comando utilizado

```bash
nmap -sV -sC 10.129.136.147
```

---

## Número de portas abertas

Foram identificadas **5 portas abertas** na máquina alvo.

> **Nota:** As portas **7777** e **7778** encontram-se no estado **filtered**, pelo que **não são consideradas portas abertas**.

---

## Serviços em execução e versões identificadas

| Porta | Estado | Serviço | Versão Detetada |
|-------:|---------|----------|-----------------|
| 22/tcp | Open | SSH | OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 |
| 53/tcp | Open | DNS | dnsmasq 2.90 |
| 139/tcp | Open | NetBIOS | Samba smbd 4.6.2 |
| 445/tcp | Open | SMB | Samba smbd 4.6.2 |
| 8443/tcp | Open | HTTPS (Amazon DCV) | dcv |

Portas filtradas:

| Porta | Estado | Serviço |
|-------:|---------|----------|
| 7777/tcp | Filtered | cbt |
| 7778/tcp | Filtered | interwise |

---

## Sistema Operativo Identificado

O Nmap identificou o sistema operativo da máquina alvo como:

```text
Linux
```

---

## Resumo da Análise

O scan realizado com o Nmap permitiu identificar os principais serviços disponíveis na máquina alvo, bem como as respetivas versões.

Foram encontrados serviços essenciais de administração remota (SSH), resolução de nomes (DNS), partilha de ficheiros (Samba/SMB) e acesso remoto seguro através do Amazon DCV.

Esta informação é fundamental numa fase inicial de reconhecimento de um teste de segurança, permitindo compreender a superfície de ataque disponível.

---

# Conclusão

Nesta sessão foi possível:

- Identificar a interface de rede da máquina local;
- Listar os serviços em escuta utilizando o comando `ss -tuln`;
- Realizar um scan de reconhecimento com o Nmap;
- Identificar **5 portas abertas** na máquina alvo;
- Identificar os serviços em execução e as respetivas versões.

O laboratório permitiu aplicar conceitos fundamentais de reconhecimento e enumeração de serviços em ambientes Linux, utilizando ferramentas essenciais para análise de redes e cibersegurança.
