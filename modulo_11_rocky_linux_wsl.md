# Módulo 11 — Expansão Enterprise: Rocky Linux no WSL e Portabilidade Red Hat / Debian

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender as diferenças essenciais entre as famílias **Debian/Ubuntu** (`apt`, `service`, caminhos padrão) e **RHEL/Rocky Linux** (`dnf`, `systemctl`, SELinux, firewall).
- Instalar e inicializar uma distribuição corporativa **Rocky Linux** no WSL.
- Configurar o servidor OpenSSH no Rocky Linux e estabelecer sessão com o **MobaXterm**.
- Repetir de forma adaptada os **Módulos 1 e 2** no Rocky Linux, consolidando a fluência multiplataforma e a validação visual via SFTP.

---

## 14. O Ecossistema Enterprise (Família RHEL / Rocky Linux)

### Conceito-Chave

No mercado corporativo de servidores e ambientes de missão crítica, a família **Red Hat Enterprise Linux (RHEL)** e seus derivados diretos (como o **Rocky Linux**) representam a base da maioria das arquiteturas corporativas. 

Embora comandos utilitários como `ls`, `mkdir`, `cp`, `find` e `dd` compartilhem o padrão POSIX em ambas as famílias, a administração do sistema apresenta particularidades fundamentais:

| Característica | Família Debian (Ubuntu/Debian) | Família Red Hat (Rocky Linux / RHEL) |
|---|---|---|
| **Gerenciador de Pacotes** | `apt` / `dpkg` | `dnf` / `rpm` |
| **Configuração do SSH** | `/etc/ssh/sshd_config` | `/etc/ssh/sshd_config` (com crypto-policies corporativas) |
| **Camada de Segurança** | AppArmor (padrão) | SELinux (Security-Enhanced Linux) |
| **Geração de Chaves de Host** | Automática no `apt install` | Pode requerer `ssh-keygen -A` explícito |
| **Grupo Administrativo** | `sudo` | `wheel` |

---

### Atividade 11.1 — Instalação do Rocky Linux no WSL e Configuração do SSH

**Objetivo:** Baixar e importar a imagem oficial do Rocky Linux no WSL, configurar o daemon OpenSSH e autenticar via MobaXterm.

#### Passo 1: Instalação do Rocky Linux no Windows

No PowerShell do Windows (ou CMD):

1. Verifique as distribuições disponíveis online:
   ```powershell
   wsl --list --online
   ```
2. Instale o Rocky Linux diretamente se estiver disponível:
   ```powershell
   wsl --install -d RockyLinux-9
   # Ou baixe o rootfs/tar oficial do Rocky Linux Cloud Images e importe:
   # wsl --import RockyLinux C:\WSL\Rocky C:\Downloads\rocky-9-container.tar.xz
   ```
3. Inicialize a sessão no Rocky Linux:
   ```powershell
   wsl -d RockyLinux-9
   ```

---

#### Passo 2: Configuração do SSH no Rocky Linux (Terminal do Rocky)

1. Atualize os repositórios corporativos com o gerenciador `dnf`:
   ```bash
   sudo dnf update -y
   sudo dnf install openssh-server openssh-clients tree findutils util-linux -y
   ```

2. Gere as chaves de host do servidor SSH (etapa típica de distribuições RHEL minimalistas):
   ```bash
   sudo ssh-keygen -A
   ```

3. Ajuste a porta e autenticação no arquivo de configuração:
   ```bash
   sudo vi /etc/ssh/sshd_config
   ```
   Garanta as seguintes diretivas ativas (ou use uma porta alternativa como `2222` caso queira manter o Ubuntu e o Rocky rodando simultaneamente):
   ```
   Port 2222
   ListenAddress 0.0.0.0
   PasswordAuthentication yes
   PermitRootLogin yes
   ```

4. Crie ou defina uma senha forte para o seu usuário:
   ```bash
   sudo passwd $(whoami)
   ```

5. Inicie o daemon do SSH:
   ```bash
   sudo /usr/sbin/sshd
   # ou se tiver suporte a service/systemctl ativo no WSL:
   # sudo systemctl start sshd
   ```

6. Identifique o IP interno da instância Rocky Linux:
   ```bash
   hostname -I
   ```

---

#### Passo 3: Conexão via MobaXterm

1. No MobaXterm, vá em **Session** ➔ **SSH**.
2. Preencha os parâmetros:
   - **Remote host:** O IP do Rocky Linux (ou `127.0.0.1` se configurou na porta `2222`).
   - **Port:** `2222` (ou `22`).
   - **Username:** Seu usuário do Rocky Linux.
3. Clique em **OK** e insira a senha.
4. **Validação:** Verifique a distribuição no terminal e observe a árvore de arquivos no SFTP à esquerda:
   ```bash
   cat /etc/os-release
   ```

---

### Atividade 11.2 — Repetição Prática do Módulo 1 no Rocky Linux

**Objetivo:** Executar os testes de inspeção com `ls`, construção determinística de árvores e preservação de atributos no novo ambiente empresarial.

**Passos:**

1. **Inspeção de metadados em diretórios do sistema:**
   ```bash
   ls -lah /var/log/
   ls -laht /var/log/ | head -n 10
   ```
   *Observe os logs típicos do ecossistema RHEL, como `/var/log/messages` e `/var/log/audit/`.*

2. **Criação da árvore completa de laboratório com `mkdir -p`:**
   ```bash
   mkdir -pv lab_rocky/{conf,data/raw,data/processed,scripts,logs,backup,tmp}
   ```
   *Confira no painel SFTP do MobaXterm a pasta `lab_rocky` surgindo em tempo real.*

3. **Duplo papel do `touch` e timestamps históricos:**
   ```bash
   touch lab_rocky/conf/enterprise.conf
   touch -d "2023-10-15 08:30:00" lab_rocky/conf/enterprise.conf
   stat lab_rocky/conf/enterprise.conf
   ```

4. **Preservação de atributos com `cp -a` vs `cp` simples:**
   ```bash
   cp lab_rocky/conf/enterprise.conf lab_rocky/conf/enterprise.conf.bak
   cp -a lab_rocky/conf/enterprise.conf lab_rocky/conf/enterprise.conf.archive
   ls -l lab_rocky/conf/
   ```
   *Validação no MobaXterm:* Clique com o botão direito nos arquivos pelo SFTP ➔ **Properties** e compare a preservação do timestamp de 2023 no arquivo `.archive`.

5. **Expansão em lote (Brace Expansion) e movimentação com `mv`:**
   ```bash
   touch lab_rocky/data/raw/inventario_2024_Q{1..4}.csv
   mv -v lab_rocky/conf/enterprise.conf.bak lab_rocky/data/raw/old_config.txt
   ls -la lab_rocky/data/raw/
   ```

---

### Atividade 11.3 — Repetição Prática do Módulo 2 no Rocky Linux

**Objetivo:** Validar o comportamento de busca granular com `find`, automações com `-exec` e manipulação direta de blocos com `dd`.

**Passos:**

1. **Criação de arquivos de teste para varredura:**
   ```bash
   touch lab_rocky/data/raw/servico_{01..05}.log
   touch lab_rocky/data/raw/config_{01..03}.yaml
   ```

2. **Filtros avançados e renomeação em lote via `-exec`:**
   ```bash
   find lab_rocky/data/raw/ -type f -name "*.log" -exec mv {} {}.bkp \;
   ls -l lab_rocky/data/raw/
   ```

3. **Geração de arquivos com tamanho exato via `dd`:**
   Gere um volume de testes de 25 MB preenchido com zeros:
   ```bash
   dd if=/dev/zero of=lab_rocky/data/swap_teste.img bs=1M count=25 status=progress
   ls -lh lab_rocky/data/swap_teste.img
   ```

4. **Teste de I/O e dados pseudoaleatórios corporativos:**
   Gere um payload de 5 MB a partir de `/dev/urandom`:
   ```bash
   dd if=/dev/urandom of=lab_rocky/data/payload_crypto.bin bs=1M count=5 status=progress
   ```

5. **Extração cirúrgica com `skip` e validação com `md5sum`:**
   Extraia os primeiros 2 MB e compare integridade:
   ```bash
   dd if=lab_rocky/data/payload_crypto.bin of=lab_rocky/data/parte1_2mb.bin bs=1M count=2
   md5sum lab_rocky/data/parte1_2mb.bin
   ```

---

## 💡 Síntese Pedagógica e Comparativa

Ao concluir este módulo, o aluno comprova que:
1. **Os utilitários essenciais do Linux (`ls`, `mkdir`, `cp`, `mv`, `find`, `dd`) comportam-se de maneira idêntica** no Ubuntu e no Rocky Linux, pois seguem padrões abertos da indústria.
2. **A habilidade chave do Administrador de Sistemas Sênior** é saber navegar entre as camadas de gestão do sistema (como `dnf` vs `apt`, inicialização do SSH e portas de escuta) mantendo a mesma precisão na manipulação do filesystem.
3. **O MobaXterm opera como um painel único multi-distribuição**, permitindo manter uma aba conectada ao Ubuntu e outra ao Rocky Linux simultaneamente (utilizando inclusive o modo *Multi-Execution* para comparar respostas em tempo real).

---

[⬅ Anterior: Módulo 10 — Editor vi/vim](modulo_10_vi.md)

[⬅ Voltar ao Índice](curso_linux.md)
