# Sessão 04 — Gestão Segura de Acessos Remotos SSH em Linux

## Módulo
Linux e Cibersegurança — Curso Reskilling

## Objetivo

Proteger o canal de gestão remota de um servidor Ubuntu através de autenticação criptográfica SSH, eliminando a utilização tradicional de passwords.

---

# Ambiente Virtual

- Plataforma: KillerCoda Ubuntu Playground
- Sistema Operativo: Ubuntu Linux
- Utilizador administrador: root
- Utilizador de teste criado: teste

---

# 1. Criação do utilizador de teste

Foi criado um novo utilizador para autenticação SSH:

```bash
sudo adduser teste
```

Resultado:

```
Adding new user `teste'
Adding user `teste' to group `users'
```

---

# 2. Geração do par de chaves Ed25519

Foi criado um par de chaves utilizando o algoritmo Ed25519:

```bash
ssh-keygen -t ed25519
```

Ficheiros gerados:

```
/root/.ssh/id_ed25519
/root/.ssh/id_ed25519.pub
```

A chave pública foi criada com sucesso.

---

# 3. Identificação do IP do servidor

O endereço IP do ambiente foi obtido através do comando:

```bash
hostname -I
```

Resultado:

```
172.30.1.2
172.17.0.1
```

Foi utilizado:

```
172.30.1.2
```

---

# 4. Transferência da chave pública

A chave pública foi transferida para o utilizador remoto:

```bash
ssh-copy-id teste@172.30.1.2
```

Resultado:

```
Number of key(s) added: 1
```

A autenticação por chave foi configurada para o utilizador:

```
teste
```

---

# 5. Configuração do daemon SSH

O ficheiro de configuração SSH foi editado:

```bash
sudo nano /etc/ssh/sshd_config
```

Alterações aplicadas:

```text
PermitRootLogin no
PasswordAuthentication no
Port 2222
```

A cópia limpa da configuração encontra-se no ficheiro:

```
sshd_config.txt
```

---

# 6. Validação da configuração

Antes do reinício do serviço foi realizada a validação da sintaxe:

```bash
sudo sshd -t
```

Resultado:

```
Sem erros apresentados.
```

---

# 7. Reinício do serviço SSH

Foi tentado o reinício utilizando o comando indicado no laboratório:

```bash
sudo systemctl restart sshd
```

Resultado:

```
Failed to restart sshd.service:
Unit sshd.service not found.
```

No Ubuntu, o serviço SSH utiliza o nome:

```bash
sudo systemctl restart ssh
```

O serviço foi reiniciado através do comando correto para Ubuntu:

```bash
sudo systemctl restart ssh
```

---

# 8. Teste de autenticação através da chave privada

Foi realizado o teste de acesso utilizando a chave criptográfica:

```bash
ssh -i /root/.ssh/id_ed25519 -p 2222 teste@172.30.1.2
```

Resultado obtido:

```
ssh: connect to host 172.30.1.2 port 2222: Connection refused
```

---

# Análise do resultado

A configuração do utilizador, geração das chaves, transferência da chave pública, alteração do ficheiro SSH e validação da sintaxe foram concluídas.

O teste final apresentou falha de ligação na porta 2222 no ambiente KillerCoda, indicando que o serviço SSH não ficou disponível nessa porta após a alteração.

---

# Evidências

## Utilizador criado

```bash
sudo adduser teste
```

## Chave criada

```bash
ssh-keygen -t ed25519
```

## Chave transferida

```bash
ssh-copy-id teste@172.30.1.2
```

## Configuração SSH

```text
PermitRootLogin no
PasswordAuthentication no
Port 2222
```

---

# Segurança

Este repositório não contém:

- Chaves privadas SSH
- Passwords
- Informações sensíveis do sistema

Apenas contém documentação técnica e configurações públicas utilizadas no laboratório.
