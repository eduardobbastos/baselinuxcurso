# Módulo 5 — Conexão SSH e Acesso Remoto via WSL

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender o protocolo SSH e seu papel na administração remota de servidores.
- Configurar o servidor SSH no WSL (Windows Subsystem for Linux).
- Conectar-se ao WSL via MobaXterm usando SSH.
- Transferir arquivos entre a máquina local e o WSL via SCP/SFTP.
- Configurar autenticação por chave pública para conexões seguras e sem senha.

---

## 8. SSH — Secure Shell

### Conceito-Chave

O **SSH (Secure Shell)** é o protocolo padrão para acesso remoto seguro a servidores Linux. Ele fornece:

- **Criptografia** de toda a comunicação (diferente do antigo Telnet)
- **Autenticação** por senha ou por chave pública/privada
- **Tunelamento** de portas e encaminhamento de X11
- **Transferência segura** de arquivos (SCP/SFTP)

### Arquitetura Cliente-Servidor

```
┌─────────────────┐         SSH (porta 22)         ┌─────────────────┐
│   MobaXterm     │ ──────────────────────────────▸ │   WSL (Ubuntu)  │
│   (Cliente)     │                                 │   (Servidor)    │
│   Windows       │ ◂────────────────────────────── │   sshd          │
└─────────────────┘      Canal criptografado        └─────────────────┘
```

### Componentes Essenciais

| Componente | Descrição |
|------------|-------------------------------------------|
| `ssh` | Cliente SSH (usado para conectar) |
| `sshd` | Servidor/daemon SSH (escuta conexões) |
| `scp` | Cópia segura de arquivos via SSH |
| `sftp` | Transferência interativa via SSH |
| `ssh-keygen` | Geração de pares de chaves |
| `ssh-copy-id` | Copia chave pública para servidor remoto |

---

### Atividade 5.1 — Configurando o Servidor SSH no WSL e Conectando via MobaXterm

**Objetivo:** Instalar e configurar o serviço SSH no WSL, identificar o endereço IP e estabelecer conexão via MobaXterm.

**Passos no WSL (abra o terminal do Ubuntu/Debian no Windows):**

1. Instale o servidor SSH (se necessário):

```bash
sudo apt update
sudo apt install openssh-server -y
```

2. Configure o SSH para aceitar login por senha (edite o arquivo de configuração):

```bash
sudo nano /etc/ssh/sshd_config
```

Localize e garanta que estas linhas estejam configuradas:

```
Port 22
ListenAddress 0.0.0.0
PasswordAuthentication yes
PermitRootLogin no
```

3. Inicie (ou reinicie) o serviço SSH:

```bash
sudo service ssh start
```

4. Verifique que o serviço está rodando:

```bash
sudo service ssh status
```

5. Descubra o endereço IP do WSL:

```bash
hostname -I
```

Anote o IP exibido (exemplo: `172.28.160.1` ou `127.0.0.1`).

6. Confirme que a porta 22 está escutando:

```bash
ss -tlnp | grep 22
```

**Passos no MobaXterm (na máquina Windows):**

7. Abra o MobaXterm e clique em **Session** → **SSH**.

8. Preencha os campos:
   - **Remote host:** O IP obtido no passo 5 (ou `localhost` / `127.0.0.1`)
   - **Port:** 22
   - **Username:** Seu usuário do WSL

9. Clique em **OK** e digite sua senha quando solicitado.

10. Você agora tem um terminal SSH conectado ao WSL! Verifique:

```bash
whoami
hostname
uname -a
```

11. Observe que o painel SFTP à esquerda do MobaXterm agora mostra os arquivos do WSL.

> **💡 Dica de Automação no WSL:** Para garantir que o serviço SSH inicie sempre que você abrir o terminal do WSL, adicione ao seu `~/.bashrc`:
> ```bash
> if ! pgrep -x sshd > /dev/null; then
>     sudo service ssh start 2>/dev/null
> fi
> ```

---

### 🔧 Troubleshooting MobaXterm × WSL (Diagnóstico de Conexão)

Se você encontrar dificuldades para conectar via SSH ou SFTP, verifique os 4 problemas mais comuns:

#### 1. Conexão recusada em `localhost` / `127.0.0.1` (WSL 2 em modo NAT)
No WSL 2 padrão, a máquina virtual roda atrás de um switch Hyper-V com IP próprio.
- **Diagnóstico:** No terminal do WSL, execute:
  ```bash
  hostname -I
  ```
- **Solução:** Em vez de `localhost` ou `127.0.0.1`, insira no campo *Remote host* do MobaXterm o primeiro IP retornado pelo comando acima (exemplo: `172.28.160.10`).

#### 2. Serviço SSH parado após reiniciar a máquina Windows
Diferente de um servidor dedicado com `systemd`, o WSL finaliza seus processos quando todas as janelas são fechadas.
- **Solução:** Abra o terminal do WSL e suba o serviço:
  ```bash
  sudo service ssh start
  sudo service ssh status
  ```

#### 3. Chave de Host alterada (*Host Key Verification Failed*)
Se você reinstalou ou reiniciou distribuições WSL com o mesmo IP, o cliente SSH pode acusar choque de chaves antigas.
- **Solução no MobaXterm:** Remova a entrada conflitante do cache de chaves locais:
  ```bash
  ssh-keygen -R 127.0.0.1
  # ou
  ssh-keygen -R <IP_DO_WSL>
  ```

#### 4. Firewall do Windows bloqueando o tráfego da porta 22
Se o MobaXterm der erro de *Connection timed out* ao tentar o IP da vEthernet:
- **Solução (no PowerShell como Administrador):**
  ```powershell
  New-NetFirewallRule -Name "WSL_SSH" -DisplayName "WSL SSH (Porta 22)" -Direction Inbound -LocalPort 22 -Protocol TCP -Action Allow
  ```

---

### Atividade 5.2 — Transferência de Arquivos com SCP e SFTP

**Objetivo:** Transferir arquivos entre a máquina Windows e o WSL usando SCP pela linha de comando e SFTP pelo MobaXterm.

**Passos (a partir do terminal MobaXterm conectado via SSH):**

1. No WSL, crie um arquivo de teste:

```bash
echo "Arquivo criado no WSL em $(date)" > ~/arquivo_do_wsl.txt
cat ~/arquivo_do_wsl.txt
```

2. **Via SFTP do MobaXterm:** No painel esquerdo, navegue até o diretório home. Você verá o `arquivo_do_wsl.txt`. Arraste-o para uma pasta na sua máquina Windows (ou clique direito → Download).

3. **Via SCP na linha de comando** (abra uma aba local no MobaXterm):

```bash
# Copiar DO WSL para o Windows (a partir do MobaXterm local)
scp seu_usuario@127.0.0.1:~/arquivo_do_wsl.txt /drives/c/Users/SeuUsuario/Desktop/
```

4. **Enviar arquivo DO Windows PARA o WSL:**

```bash
# Crie um arquivo local primeiro
echo "Arquivo do Windows" > /drives/c/Users/SeuUsuario/Desktop/arquivo_windows.txt

# Copie para o WSL
scp /drives/c/Users/SeuUsuario/Desktop/arquivo_windows.txt seu_usuario@127.0.0.1:~/
```

5. No terminal SSH, confirme que o arquivo chegou:

```bash
ls -l ~/arquivo_windows.txt
cat ~/arquivo_windows.txt
```

6. **Transferência de diretório inteiro** com SCP (flag `-r`):

```bash
# Copiar um diretório completo do WSL
scp -r seu_usuario@127.0.0.1:~/lab_linux/ /drives/c/Users/SeuUsuario/Desktop/lab_linux_backup/
```

7. **Via SFTP interativo:**

```bash
sftp seu_usuario@127.0.0.1
```

Dentro da sessão SFTP:

```
sftp> ls
sftp> cd lab_linux/
sftp> ls
sftp> get scripts/sysinfo.sh
sftp> put /drives/c/Users/SeuUsuario/Desktop/arquivo_windows.txt
sftp> bye
```

**Cenário real:** Em ambientes de produção, SCP e SFTP são as formas padrão de transferir configurações, logs e deploys entre servidores.

---

### Atividade 5.3 — Autenticação por Chave Pública (Passwordless SSH)

**Objetivo:** Configurar autenticação por chave SSH para eliminar a necessidade de digitar senha a cada conexão.

**Conceito-Chave:**

```
┌──────────────┐                    ┌──────────────┐
│   Cliente    │                    │   Servidor   │
│              │                    │              │
│ id_rsa       │───── Challenge ───▸│ authorized_  │
│ (PRIVADA)    │◂── Signed resp. ───│ keys         │
│ 🔒 Secreta   │                    │ (PÚBLICA)    │
└──────────────┘                    └──────────────┘
```

- A **chave privada** fica no cliente (nunca compartilhada)
- A **chave pública** é copiada para o servidor
- O servidor envia um desafio; o cliente prova posse da chave privada sem revelá-la

**Passos:**

1. **Gere o par de chaves** (no MobaXterm ou terminal local):

```bash
ssh-keygen -t ed25519 -C "aluno@curso-linux"
```

- Quando perguntar o local, aceite o padrão (`~/.ssh/id_ed25519`)
- A passphrase é opcional (deixe vazia para testes, use em produção)

2. Verifique as chaves geradas:

```bash
ls -la ~/.ssh/
cat ~/.ssh/id_ed25519.pub
```

3. **Copie a chave pública para o WSL:**

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub seu_usuario@127.0.0.1
```

Digite a senha do WSL pela **última vez**.

4. **Teste a conexão sem senha:**

```bash
ssh seu_usuario@127.0.0.1
```

Deve conectar diretamente, sem pedir senha!

5. **Verifique no servidor** que a chave foi adicionada:

```bash
cat ~/.ssh/authorized_keys
```

6. **Configure um alias SSH** para facilitar conexões futuras:

```bash
nano ~/.ssh/config
```

Adicione:

```
Host wsl
    HostName 127.0.0.1
    User seu_usuario
    Port 22
    IdentityFile ~/.ssh/id_ed25519
```

7. Agora conecte usando apenas o alias:

```bash
ssh wsl
```

8. Teste SCP com o alias:

```bash
scp wsl:~/arquivo_do_wsl.txt .
```

**Segurança:**

- Após confirmar que a chave funciona, considere desabilitar login por senha:

```bash
# No WSL, edite o sshd_config
sudo nano /etc/ssh/sshd_config

# Altere:
PasswordAuthentication no

# Reinicie o SSH:
sudo service ssh restart
```

> ⚠️ **CUIDADO:** Só desabilite senha DEPOIS de confirmar que a chave funciona, ou você ficará trancado fora!

**Pergunta reflexiva:** Qual a vantagem de usar `ed25519` em vez de `rsa`? (Dica: pesquise sobre tamanho de chave e performance.)

---

## Resumo: Mapeamento MobaXterm para SSH

| Ação | Recurso do MobaXterm |
|------|---------------------|
| Conexão SSH | Session → SSH → preencher host/porta/usuário |
| Navegação de arquivos remotos | Painel SFTP lateral (aparece automaticamente ao conectar) |
| Transferência de arquivos | Drag & Drop no painel SFTP ou SCP/SFTP via terminal |
| Edição remota de arquivos | Duplo-clique no arquivo no painel SFTP (abre MobaTextEditor) |
| Gerenciamento de chaves | Tools → MobaKeyGen (interface gráfica para ssh-keygen) |
| Múltiplas sessões | MultiExec para rodar comandos em várias sessões simultaneamente |
| Túneis SSH | Tools → SSH Tunnel (MobaSSHTunnel) |

---

> **💡 Dica MobaXterm:** O MobaXterm salva sessões SSH. Após configurar a conexão com o WSL, ela ficará disponível no painel lateral esquerdo para reconexão rápida com um clique.

---

[⬅ Anterior: Módulo 4 — Automação e Scripts](modulo_04_automacao_scripts.md)

[➡ Próximo: Módulo 6 — Gerenciamento de Usuários e Grupos](modulo_06_usuarios.md)

[⬅ Voltar ao Índice](curso_linux.md)
