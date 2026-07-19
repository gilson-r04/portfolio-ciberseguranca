# Sessão 03 — Hardening de Redes Linux e Configuração de Firewalls

![Linux Security](https://img.shields.io/badge/Linux-Cybersecurity-blue)
![Firewall](https://img.shields.io/badge/Firewall-UFW%20%7C%20iptables-green)

## Curso

**Percurso Reskilling — Linux e Cibersegurança**

## Módulo

**Linux e Cibersegurança**

## Sessão Laboratorial

**Sessão 03 — Hardening de Redes Linux e Configuração de Firewalls**

## Objetivo de Aprendizagem

**OA3 — Aplicar**

---

# 1. Introdução

Nesta sessão foi realizada a configuração de uma política defensiva de segurança
num ambiente Linux, utilizando mecanismos de firewall para reduzir a superfície
de ataque do servidor.

O objetivo principal foi impedir acessos não autorizados a serviços críticos,
permitindo apenas comunicações necessárias.

Foram utilizadas duas tecnologias:

- **UFW (Uncomplicated Firewall)** — ferramenta simplificada de gestão de firewall Linux;
- **iptables** — ferramenta avançada de filtragem de pacotes baseada no Netfilter.

---

# 2. Ambiente Laboratorial

## Plataforma utilizada

**TryHackMe — Network Security Essentials**

Sistema:

```
Ubuntu Linux
```

Terminal utilizado:

```
Bash Shell
```

---

# 3. Conceitos Aplicados

## Firewall

Um firewall é um mecanismo de segurança responsável por controlar o tráfego
de rede baseado em regras definidas pelo administrador.

Neste laboratório foi aplicada uma política:

```
Entrada  → Bloqueada por padrão
Saída    → Permitida
SSH      → Permitido
IP suspeito → Bloqueado
```

---

# 4. Configuração UFW

## 4.1 Verificação do estado inicial

Primeiramente foi verificado o estado atual do firewall.

Comando:

```bash
sudo ufw status
```

Resultado:

```text
[colocar output obtido no laboratório]
```

---

# 4.2 Política padrão de entrada

Foi configurada uma política restritiva para bloquear todas as ligações
recebidas por padrão.

Comando:

```bash
sudo ufw default deny incoming
```

Resultado esperado:

```text
Default incoming policy changed to 'deny'
```

### Explicação

Esta configuração impede que serviços não autorizados aceitem ligações
externas.

Apenas regras explicitamente permitidas poderão receber tráfego.

---

# 4.3 Política padrão de saída

Foi permitido que o servidor continue a comunicar com redes externas.

Comando:

```bash
sudo ufw default allow outgoing
```

Resultado esperado:

```text
Default outgoing policy changed to 'allow'
```

### Explicação

Permite operações como:

- atualizações do sistema;
- comunicação com servidores externos;
- instalação de pacotes.

---

# 4.4 Permissão de acesso SSH

Como o acesso administrativo remoto utiliza SSH, foi criada uma exceção.

Comando:

```bash
sudo ufw allow 22/tcp
```

Resultado esperado:

```text
Rules updated
Rules updated (v6)
```

### Explicação

A porta TCP 22 foi autorizada para permitir administração remota segura.

---

# 5. Configuração iptables

## 5.1 Bloqueio de endereço IP suspeito

Foi simulada uma tentativa de acesso proveniente de um endereço IP malicioso.

IP bloqueado:

```
203.0.113.50
```

Regra aplicada:

```bash
sudo iptables -A INPUT -s 203.0.113.50 -j DROP
```

---

## Explicação da regra

| Parâmetro | Função |
|---|---|
| iptables | Ferramenta de firewall Linux |
| -A | Adicionar nova regra |
| INPUT | Tráfego recebido pelo servidor |
| -s | Define endereço de origem |
| 203.0.113.50 | IP considerado suspeito |
| DROP | Bloqueia silenciosamente o tráfego |

---

# 6. Persistência das regras iptables

As regras do iptables podem desaparecer após reiniciar o sistema.

Para guardar a configuração foi utilizado:

```bash
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

Resultado:

```text
[colocar output obtido no laboratório]
```

---

# 7. Evidências da Configuração

## 7.1 Estado final UFW

Comando:

```bash
sudo ufw status verbose
```

Resultado:

```text
[colar aqui o resultado]
```

---

## 7.2 Regras atuais iptables

Comando:

```bash
sudo iptables -L -v
```

Resultado:

```text
[colar aqui o resultado]
```

---

# 8. Análise de Segurança

Após a implementação das regras foi criada uma política defensiva baseada
no princípio de menor privilégio.

As medidas aplicadas foram:

## Restrição de entrada

O bloqueio padrão de ligações recebidas reduz a possibilidade de exploração
de serviços expostos.

## Permissão controlada

Apenas o serviço SSH foi autorizado, mantendo o acesso administrativo.

## Bloqueio de origem suspeita

O endereço IP identificado como potencial ameaça foi bloqueado através
da chain INPUT do iptables.

## Persistência

As regras foram guardadas para garantir continuidade da configuração.

---

# 9. Problemas encontrados durante a execução

Durante a configuração foram identificados alguns erros:

## Erro de escrita no comando UFW

Comando incorreto:

```bash
sudo ufw default alow outgoing
```

Correção:

```bash
sudo ufw default allow outgoing
```

---

## Diretório iptables inexistente

Erro:

```text
/etc/iptables/rules.v4: No such file or directory
```

Correção:

Criação do diretório:

```bash
sudo mkdir -p /etc/iptables
```

Depois foi executado novamente:

```bash
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

---

# 10. Conclusão

Nesta sessão foi implementada uma política de hardening de rede num sistema Linux.

Foram aplicadas configurações utilizando UFW e iptables para:

- restringir ligações externas;
- permitir apenas serviços necessários;
- bloquear endereços suspeitos;
- guardar regras de firewall.

Estas medidas contribuem para aumentar a segurança do servidor,
reduzindo a superfície de ataque e protegendo serviços críticos.

---

# Autor

**mbaptista**

Curso:
**Reskilling — Linux e Cibersegurança**

Data:
**19 de Julho de 2026**
