# Laboratório – Sessão 06
# MiniCTF Defensivo Linux

## Curso
**Reskilling – Linux e Cibersegurança**

**Formador:** Péricles Borges

---

# Objetivo

Este laboratório teve como objetivo realizar uma auditoria de segurança a um servidor Ubuntu comprometido da empresa fictícia **Linux Agency**, identificando vulnerabilidades, implementando medidas de contenção, reforçando a configuração do sistema e documentando todas as ações executadas durante a intervenção.

---

# Cenário

Foi disponibilizado um servidor Ubuntu com indícios de atividade suspeita e diversas configurações inseguras. A missão consistiu em executar uma auditoria defensiva, reduzir a superfície de ataque, aplicar medidas de hardening e validar a melhoria da postura de segurança do sistema.

---

# Ambiente Utilizado

- Plataforma: TryHackMe
- Sala utilizada: Linux Agency
- Sistema Operativo: Ubuntu Linux
- Ferramentas utilizadas:
  - ss
  - nmap
  - UFW
  - OpenSSH
  - Nano

---

# Fase 1 – Identificação e Triagem

## 1.1 Análise de Rede

Foi iniciada uma auditoria à rede para identificar portas abertas e serviços em execução.

### Comandos executados

```bash
ss -tuln

nmap -sV localhost
```

## Serviços Identificados

| Porta | Serviço | Versão | Observação |
|------:|----------|---------|------------|
|22|OpenSSH|9.6p1|Administração remota|
|53|dnsmasq|2.90|Servidor DNS|
|111|rpcbind|2-4|Serviço RPC|
|139|Samba|4.6.2|Partilha de ficheiros|
|445|Samba|4.6.2|Partilha SMB|
|7777|Nginx|1.28.2|Servidor Web|
|7778|Nginx|1.29.5|Servidor Web|
|8443|DCV HTTPS|SSL|Serviço HTTPS alternativo|

### Análise

A auditoria revelou vários serviços ativos além do SSH. Alguns destes serviços, como Samba (139 e 445) e os servidores Web (7777 e 7778), aumentam significativamente a superfície de ataque do sistema e devem ser avaliados quanto à sua real necessidade.

---

# 1.2 Auditoria de Contas

Foi realizada uma verificação das contas do sistema e dos mecanismos de autenticação.

### Comandos executados

```bash
sudo cat /etc/shadow | awk -F: '($2==""){print $1}'

cat ~/.ssh/authorized_keys
```

## Resultado

A auditoria não identificou contas sem palavra-passe configurada.

Foram encontradas diversas chaves públicas autorizadas no ficheiro `authorized_keys`, indicando múltiplos acessos por autenticação SSH.

Embora não representem necessariamente uma vulnerabilidade, recomenda-se validar periodicamente a legitimidade de todas as chaves autorizadas e remover aquelas que já não sejam necessárias.

---

# Fase 2 – Contenção

O objetivo desta fase consistiu em reduzir imediatamente a superfície de ataque através da configuração da firewall do sistema.

## Comandos executados

```bash
sudo ufw default deny incoming

sudo ufw allow 22/tcp

sudo ufw enable

sudo ufw status verbose
```

## Medidas Implementadas

- Política padrão alterada para bloquear todas as ligações de entrada.
- Apenas a porta SSH (22/TCP) permaneceu autorizada.
- Firewall ativada permanentemente.

## Resultado

Após a aplicação destas regras, o servidor passou a aceitar apenas ligações SSH, reduzindo significativamente a exposição a acessos não autorizados.

---

# Fase 3 – Enrijecimento (Hardening)

Nesta fase foram implementadas medidas de reforço da segurança do serviço SSH.

## Configuração alterada

### Antes

```text
PermitRootLogin yes
PasswordAuthentication yes
```

### Depois

```text
PermitRootLogin no
PasswordAuthentication no
```

## Validação

Após as alterações foi validada a sintaxe da configuração.

```bash
sudo sshd -t
```

Não foram encontrados erros.

Em seguida o serviço foi reiniciado.

```bash
sudo systemctl restart ssh
```

O reinício foi concluído com sucesso.

Foi igualmente criada uma cópia limpa do ficheiro de configuração para documentação.

```bash
cp /etc/ssh/sshd_config ~/sshd_config
```

---

# Validação Final

Foi tentada a execução da ferramenta Lynis para avaliar a postura global de segurança do sistema.

```bash
sudo lynis audit system
```

## Resultado

A ferramenta não se encontrava instalada na máquina disponibilizada pelo laboratório.

```text
sudo: lynis: command not found
```

Por este motivo não foi possível obter o Hardening Index solicitado.

---


# Competências Aplicadas

Durante esta atividade foram aplicados conhecimentos relacionados com:

- Auditoria de sistemas Linux
- Enumeração de serviços
- Análise de portas
- Hardening SSH
- Configuração da firewall UFW
- Gestão de acessos remotos
- Boas práticas de segurança
- Documentação técnica

---

# Conclusão

O laboratório permitiu simular uma resposta a um incidente de segurança num servidor Linux. Foram identificados os principais serviços ativos, auditadas as contas do sistema, reforçada a configuração do SSH e implementadas políticas restritivas na firewall UFW.

As medidas aplicadas reduziram significativamente a superfície de ataque do servidor, melhorando a segurança do ambiente e demonstrando a importância de uma abordagem estruturada em auditoria, contenção e remediação de sistemas Linux.

Apesar de não ter sido possível executar a validação com o Lynis devido à indisponibilidade da ferramenta no ambiente fornecido, todas as restantes tarefas previstas no laboratório foram concluídas com sucesso.
