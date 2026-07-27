# Sessão 05 — Auditoria de Segurança e Hardening com Lynis

## Módulo
Linux e Cibersegurança — Curso Reskilling

## 1. Introdução

Nesta sessão foi realizada uma auditoria de segurança ao sistema Linux utilizando a ferramenta **Lynis**, uma ferramenta de auditoria e hardening para sistemas Unix/Linux.

O objetivo foi identificar possíveis falhas de segurança, analisar o nível de proteção atual do sistema e propor medidas corretivas para melhorar a postura de segurança.

---

# 2. Ambiente de Auditoria

| Item | Informação |
|---|---|
| Sistema Operativo | Ubuntu 24.04.4 LTS |
| Kernel | 6.8.0-136-generic |
| Hostname | ubuntu |
| Lynis Version | 3.0.9 |
| Tipo de auditoria | Security Audit |
| Data da auditoria | 27/07/2026 |

---

# 3. Instalação do Lynis

Primeiramente foi atualizado o sistema e instalada a ferramenta Lynis.

### Atualização dos repositórios

```bash
sudo apt update
```

### Instalação do Lynis

```bash
sudo apt install lynis -y
```

---

# 4. Execução da Auditoria

A auditoria completa foi executada através do comando:

```bash
sudo lynis audit system
```

O relatório gerado foi guardado através do comando:

```bash
sudo cp /var/log/lynis-report.dat ~/lynis-report.txt
```

Visualização do relatório:

```bash
cat ~/lynis-report.txt | head -50
```

---

# 5. Resultado Inicial da Auditoria

## Hardening Score Inicial

Resultado obtido:

```
Hardening index : 62
```

Resumo:

| Indicador | Resultado |
|-|-|
| Hardening Index | 62/100 |
| Testes realizados | 262 |
| Plugins ativos | 1 |
| Firewall | Ativo |
| Malware Scanner | Não instalado |

---

# 6. Warnings Encontrados

Durante a auditoria foi identificado o seguinte aviso:

## WARNING - Pacotes vulneráveis

Código:

```
PKGS-7392
```

Descrição:

```
Found one or more vulnerable packages
```

O sistema possui pacotes que necessitam de atualização.

Impacto:

- Possível exploração de vulnerabilidades conhecidas;
- Risco de comprometimento do sistema;
- Falta de aplicação de patches de segurança.

---

# 7. Suggestions Relevantes Encontradas

Foram identificadas várias recomendações de melhoria.

## 7.1 Falta do Fail2Ban

Código:

```
DEB-0880
```

Problema:

O sistema não possui proteção automática contra múltiplas tentativas de autenticação.

Impacto:

Um atacante pode realizar ataques de força bruta contra serviços como SSH.

---

## 7.2 Hardening do SSH

Código:

```
SSH-7408
```

Foram encontradas configurações SSH que podem ser melhoradas:

- AllowTcpForwarding ativo
- MaxAuthTries elevado
- MaxSessions elevado
- AllowAgentForwarding ativo

Impacto:

Uma configuração SSH inadequada pode aumentar a superfície de ataque.

---

## 7.3 Ausência de ferramenta de integridade de ficheiros

Código:

```
FINT-4350
```

Problema:

Não existe ferramenta para monitorizar alterações em ficheiros críticos.

Impacto:

Alterações não autorizadas podem passar despercebidas.

---

# 8. Vulnerabilidades Selecionadas

Foram selecionadas duas vulnerabilidades para correção.

---

# Vulnerabilidade 1 — Falta de proteção contra ataques de força bruta SSH

## Identificação

Código Lynis:

```
DEB-0880
```

Problema:

O serviço SSH estava exposto sem mecanismo automático de bloqueio após várias tentativas falhadas.

---

## Medida Corretiva

Instalação do Fail2Ban:

```bash
sudo apt install fail2ban -y
```

Ativar o serviço:

```bash
sudo systemctl enable fail2ban
```

Iniciar o serviço:

```bash
sudo systemctl start fail2ban
```

Verificar estado:

```bash
sudo systemctl status fail2ban
```

---

## Resultado Esperado

Após implementação:

- Bloqueio automático de IPs suspeitos;
- Redução de ataques brute-force;
- Maior proteção do serviço SSH.

---

# Vulnerabilidade 2 — Configuração SSH pouco restritiva

## Identificação

Código Lynis:

```
SSH-7408
```

Problema:

Algumas opções do SSH permitem maior liberdade de utilização e aumentam a superfície de ataque.

---

## Medida Corretiva

Editar configuração SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

Adicionar ou alterar:

```
MaxAuthTries 3

MaxSessions 2

AllowTcpForwarding no

AllowAgentForwarding no

LogLevel VERBOSE
```

Guardar alterações.

---

Reiniciar serviço SSH:

```bash
sudo systemctl restart ssh
```

Verificar configuração:

```bash
sudo sshd -t
```

---

## Resultado Esperado

Melhorias:

- Redução de tentativas de login;
- Menor possibilidade de abuso de sessões SSH;
- Maior rastreabilidade através de logs.

---

# 9. Excerto do Relatório Lynis

Trecho principal obtido:

```
[ Lynis 3.0.9 Results ]

Warnings (1):

! Found one or more vulnerable packages. [PKGS-7392]


Suggestions (48):

* Install fail2ban to automatically ban hosts
that commit multiple authentication errors.

* Consider hardening SSH configuration.

* Install a file integrity tool to monitor changes.

```

---

# 10. Estado de Segurança Atual

Antes das correções:

| Área | Estado |
|-|-|
| Firewall | Ativo |
| SSH | Funcionando |
| AppArmor | Ativo |
| Fail2Ban | Ausente |
| Auditoria de ficheiros | Ausente |
| Hardening Score | 62/100 |

---

# 11. Conclusão

A auditoria realizada através do Lynis permitiu identificar pontos de melhoria na segurança do sistema Ubuntu.

As principais vulnerabilidades encontradas estavam relacionadas com:

- Proteção contra ataques de autenticação;
- Configuração do serviço SSH.

As medidas propostas permitem reduzir riscos, melhorar o controlo de acessos e aumentar o nível geral de hardening do sistema.

Após aplicar as correções recomendadas, deve ser realizada uma nova auditoria:

```bash
sudo lynis audit system
```

para comparar o novo Hardening Score e validar as melhorias implementadas.

---
