# Relatório Técnico de Análise Forense e Auditoria Avançada

## Curso
**Reskilling — Linux e Cibersegurança**

## Módulo
**Linux e Cibersegurança**

## Sessão Laboratorial
**Sessão 02 — Auditoria de Sistemas Linux e Análise Avançada de Logs**

## Laboratório
**TryHackMe — Linux Server Forensics**

## Investigador / Analista Forense
mbaptista

## Data do Incidente
19 de Julho de 2026

---

# 1. Sumário Executivo

Este relatório apresenta a investigação forense realizada sobre um servidor Linux comprometido no ambiente de laboratório Linux Server Forensics.

A análise teve como objetivo identificar:

- A origem do ataque;
- O método utilizado pelo atacante;
- As tentativas de autenticação realizadas;
- O momento exato do comprometimento;
- O utilizador afetado;
- Possíveis mecanismos de persistência.

A investigação foi baseada na análise de logs do sistema, principalmente:

```
/var/log/auth.log
```

Foram analisados eventos de autenticação SSH, atividades suspeitas e indicadores de comprometimento.

---

# 2. Ambiente de Investigação

## Sistema analisado

Servidor Linux comprometido.

## Diretório de logs analisado:

```bash
cd /var/log/
```

## Principais ficheiros analisados:

```
auth.log
apache2/access.log
/etc/passwd
/root/.bash_history
/root/.ssh/authorized_keys
```

---

# 3. Identificação do Incidente

Após análise dos artefactos do sistema foram identificados os seguintes indicadores:

| Informação | Resultado |
|---|---|
| IP do atacante | 192.168.56.24 |
| Serviço atacado | SSH |
| Utilizador comprometido | fred |
| Data do incidente | 19/07/2026 |
| Hora do comprometimento | 13:30:15 |
| Método de acesso | Password SSH |

---

# 4. Análise dos Logs de Autenticação

## 4.1 Pesquisa de tentativas falhadas

Comando utilizado:

```bash
grep "Failed password" auth.log
```

Resultado identificado:

```text
Jul 19 13:28:10 server sshd[2104]: Failed password for invalid user admin from 192.168.56.24 port 43210 ssh2

Jul 19 13:29:02 server sshd[2108]: Failed password for fred from 192.168.56.24 port 43216 ssh2
```

## Análise

Foram identificadas várias tentativas de autenticação inválidas provenientes do IP:

```
192.168.56.24
```

O padrão observado é compatível com um ataque de força bruta contra SSH.

---

# 5. Identificação da Origem do Ataque

Comando utilizado:

```bash
grep "Failed password" auth.log | awk '{print $11}' | sort | uniq -c | sort -nr
```

Resultado:

```
192.168.56.24
```

O endereço IP com maior número de tentativas foi identificado como:

```
192.168.56.24
```

Este endereço foi considerado o principal indicador de comprometimento.

---

# 6. Confirmação de Acesso Bem-Sucedido

Comando utilizado:

```bash
grep -E "Accepted password|Accepted publickey" auth.log
```

Resultado:

```text
Jul 19 13:30:15 server sshd[2140]:
Accepted password for fred from 192.168.56.24 port 43222 ssh2
```

## Conclusão

O atacante conseguiu autenticar-se com sucesso utilizando a conta:

```
fred
```

às:

```
13:30:15
```

através do serviço SSH.

---

# 7. Linha Temporal do Ataque

```
13:28:10
|
|-- Tentativa falhada utilizando utilizador inexistente "admin"
|
13:29:02
|
|-- Tentativa falhada contra utilizador "fred"
|
13:30:15
|
|-- Autenticação SSH bem sucedida
|
|-- Conta "fred" comprometida
```

---

# 8. Investigação Pós-Comprometimento

Após o acesso inicial, foram analisados outros artefactos do sistema para identificar possíveis alterações realizadas pelo atacante.

---

# 8.1 Análise de utilizadores do sistema

Ficheiro analisado:

```bash
/etc/passwd
```

Foi identificada uma conta suspeita:

```
root2:x:0:0:Malicious Account:/root:/bin/bash
```

## Impacto

A conta possuía UID 0, indicando privilégios equivalentes ao utilizador root.

---

# 8.2 Análise de chaves SSH

Ficheiro analisado:

```bash
/root/.ssh/authorized_keys
```

Foi encontrada uma chave SSH adicionada pelo atacante:

```
kali@kali
```

## Impacto

A chave permitia acesso persistente ao sistema sem necessidade de password.

---

# 8.3 Análise do histórico de comandos

Comando utilizado:

```bash
cat /root/.bash_history
```

Foi possível identificar comandos executados após a obtenção de privilégios administrativos.

---

# 9. Indicadores de Comprometimento (IOC)

## IP atacante

```
192.168.56.24
```

## Utilizador comprometido

```
fred
```

## Conta privilegiada criada

```
root2
```

## Chave SSH suspeita

```
kali@kali
```

## Flag de persistência identificada

```
gh0st_1n_the_machine
```

---

# 10. Evidências Forenses

## Log Apache — Reconhecimento

```text
192.168.56.24 - -
[19/Jul/2026:13:30:15 +0100]
"GET /nmaplowercheck1681912425 HTTP/1.1"
404
"Nmap Scripting Engine"
```

Indica utilização do Nmap durante a fase de reconhecimento.

---

## Log SSH

```text
Jul 19 13:28:10
Failed password for invalid user admin
from 192.168.56.24

Jul 19 13:29:02
Failed password for fred
from 192.168.56.24

Jul 19 13:30:15
Accepted password for fred
from 192.168.56.24
```

---

# 11. Classificação do Ataque

## Fase 1 — Reconhecimento

Ferramenta identificada:

```
Nmap
```

Objetivo:

- Descoberta de serviços;
- Identificação do servidor.

---

## Fase 2 — Tentativas de Acesso

Método:

```
Brute Force SSH
```

Origem:

```
192.168.56.24
```

---

## Fase 3 — Comprometimento

Acesso obtido:

```
fred
```

Serviço:

```
SSH
```

---

## Fase 4 — Persistência

Técnicas identificadas:

- Criação de utilizador UID 0;
- Inserção de chave SSH;
- Alteração de ficheiros críticos.

---

# 12. Recomendações de Mitigação

## Segurança SSH

- Desativar login remoto do utilizador root;

Editar:

```bash
/etc/ssh/sshd_config
```

Adicionar:

```
PermitRootLogin no
```

---

## Proteção contra força bruta

Instalar:

```bash
sudo apt install fail2ban
```

Configurar bloqueio automático de IPs suspeitos.

---

## Monitorização

Implementar:

- Wazuh;
- AIDE;
- Sistemas SIEM.

---

## Gestão de utilizadores

Auditar regularmente:

```bash
/etc/passwd
```

Procurar contas com:

```
UID 0
```

---

# 13. Conclusão Final

A investigação confirmou que o servidor Linux sofreu um comprometimento através de ataques de autenticação SSH.

O atacante identificado pelo IP:

```
192.168.56.24
```

realizou múltiplas tentativas falhadas antes de conseguir acesso válido à conta:

```
fred
```

às:

```
13:30:15
```

Após o acesso inicial foram identificados mecanismos de persistência e elevação de privilégios, demonstrando a necessidade de uma política contínua de monitorização, hardening e análise de logs.

---

# Fim do Relatório
