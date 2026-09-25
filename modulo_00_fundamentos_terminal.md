# Módulo 0 — Fundamentos do Terminal e Anatomia do Linux

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender a anatomia exata de uma linha de comando: o que é o **comando**, o que são **opções/flags** e o que são **argumentos/alvos**.
- Desmistificar comportamentos que assustam o iniciante: digitação cega de senhas, cópia/cola no terminal e cancelamento de processos travados.
- Compreender a autoridade e o risco do comando `sudo` e a diferença entre usuário comum e `root`.
- Dominar o mapa mental da **Árvore de Diretórios do Linux (FHS — Filesystem Hierarchy Standard)**: o papel de `/`, `/etc`, `/var`, `/home`, `/tmp`, `/bin`, `/dev` e `/proc`.
- Conhecer a navegação espacial por caminhos absolutos vs. relativos (`.`, `..`, `~`).

---

## 0. O Que é o Terminal e Como Ele Pensa?

### Conceito-Chave: Anatomia de um Comando

No terminal, você conversa diretamente com a **Shell** (geralmente o `bash`). Toda instrução no terminal segue uma sintaxe padrão de três partes:

```
comando  -opção(ões)    argumento(s)
  │          │               └─ Alvo da ação (arquivo, diretório, texto)
  │          └─ Modificadores de comportamento (flags/opções)
  └─ O programa ou binário executável
```

#### Exemplo Prático:
```bash
ls -lh /var/log
│  │   └─ Argumento: onde executar a ação (/var/log)
│  └─ Opções: -l (formato longo) e -h (tamanho legível)
└─ Comando: invoca o binário 'ls'
```

---

### O Que Causa Estranhamento no Início?

1. **A senha que "não aparece":**
   - No Linux, ao digitar a senha no comando `sudo` ou `su`, **nenhum caractere, asterisco ou bolinha é exibido na tela**. 
   - Isso é uma medida de segurança histórica e intencional chamada *silent input* (evita que quem esteja atrás de você saiba o tamanho da sua senha). Apenas digite normalmente e pressione `Enter`.

2. **Copiar e Colar no Terminal:**
   - No terminal Linux clássico, o atalho `Ctrl+C` **não copia** (ele envia o sinal `SIGINT` para interromper o programa em execução).
   - Para copiar e colar no MobaXterm / terminal Linux:
     - **Copiar:** Selecione o texto com o mouse (no MobaXterm ele copia automaticamente ao selecionar!) ou use `Ctrl + Shift + C`.
     - **Colar:** Clique com o botão direito do mouse ou use `Shift + Insert` / `Ctrl + Shift + V`.

3. **Autocompletar com `Tab`:**
   - Você **nunca** deve digitar nomes longos de pastas ou arquivos manualmente.
   - Digite as primeiras letras e pressione `Tab`. Pressione `Tab` duas vezes para listar as alternativas disponíveis.

---

### O Modelo Administrativo: `sudo` e `root`

- **Usuário Comum (`$` no prompt):** Tem controle restrito apenas sobre seu próprio diretório home (`/home/usuario/`). Não pode alterar arquivos do sistema.
- **Superusuário Root (`#` no prompt):** O administrador supremo. Tem permissão de leitura, escrita e exclusão sobre absolutamente qualquer arquivo do sistema operacional, sem confirmações de segurança.
- **`sudo` (Superuser Do):** Permite que um usuário autorizado execute um comando pontual com privilégios de `root`, registrando a ação em logs de auditoria (`/var/log/auth.log` ou `/var/log/secure`).

---

### A Árvore de Diretórios (FHS — Filesystem Hierarchy Standard)

Diferente do Windows (que utiliza letras de unidade como `C:\`, `D:\`), o Linux possui uma **única raiz universal representada pela barra (`/`)**. Tudo no Linux — discos rígidos, pendrives, placas de rede, processos — está montado sob essa única árvore:

```
/ (Raiz - Root do Filesystem)
├── bin / sbin   → Binários essenciais do sistema (ls, cp, ping, reboot)
├── etc          → Arquivos de configuração de todos os serviços e do SO
├── home         → Diretórios pessoais dos usuários comuns (/home/aluno)
├── root         → Diretório pessoal exclusivo do superusuário root
├── var          → Dados variáveis que crescem com o tempo (logs, bancos de dados, filas)
│   └── log      → Onde o sistema e os serviços gravam o histórico de eventos
├── tmp          → Arquivos temporários (limpos periodicamente ou no reboot)
├── dev          → Dispositivos de hardware mapeados como arquivos (/dev/sda, /dev/zero, /dev/urandom)
├── proc         → Pseudofilesystem: a memória RAM e o Kernel expostos em forma de texto
├── sys          → Interface moderna com módulos de hardware e drivers do Kernel
├── opt          → Softwares de terceiros ou pacotes proprietários corporativos
├── usr          → Bibliotecas e utilitários instalados para os usuários do sistema
└── mnt / media  → Pontos de montagem manuais e mídias removíveis (pendrives, discos externos)
```

---

### Atividade 0.1 — Sobrevivência no Console: Navegação Espacial e Autocompletar

**Objetivo:** Praticar caminhos absolutos, caminhos relativos e o uso do `Tab` para nunca digitar caminhos manualmente.

**Passos:**

1. Descubra onde você está agora no sistema operacional:
   ```bash
   pwd
   ```
   *(Print Working Directory — exibe o caminho absoluto atual).*

2. Entenda a navegação por símbolos:
   - `.` = O diretório atual
   - `..` = O diretório pai (um nível acima)
   - `~` = Atalho para o seu diretório pessoal (`$HOME`)
   - `/` = A raiz universal do sistema

3. Pratique a movimentação:
   ```bash
   cd /
   pwd
   ls -l
   ```

4. Suba até a raiz de configurações usando caminho absoluto:
   ```bash
   cd /etc
   pwd
   ```

5. Volte para o seu diretório pessoal de qualquer lugar usando o til:
   ```bash
   cd ~
   pwd
   ```

6. **Treino de Autocompletar:**
   Digite `cd /v` e pressione `Tab`. Digite `l` e pressione `Tab`. O terminal completará para `/var/log/`. Pressione `Enter`.
   ```bash
   cd /var/log/
   pwd
   ```

7. Retorne ao diretório onde você estava anteriormente usando o hífen:
   ```bash
   cd -
   ```

**Pergunta reflexiva:** Por que é mais seguro usar caminhos absolutos (iniciados com `/`) em scripts de automação?

---

### Atividade 0.2 — A anatomia do `sudo`, senhas silenciosas e controle de processos

**Objetivo:** Experimentar o comportamento de privilégios, entender a digitação de senhas e dominar atalhos de controle do terminal.

**Passos:**

1. Tente inspecionar o arquivo mais protegido de senhas do sistema sem privilégios:
   ```bash
   cat /etc/shadow
   ```
   O terminal retornará: `Permission denied`.

2. Agora execute a mesma instrução com `sudo`:
   ```bash
   sudo cat /etc/shadow | head -n 5
   ```
   *Atenção:* Ao digitar sua senha, observe que o cursor permanece imóvel. Digite os caracteres e aperte `Enter`.

3. **Experimente o cancelamento de comandos travados (`Ctrl + C`):**
   Execute um comando que roda indefinidamente:
   ```bash
   ping 127.0.0.1
   ```
   Observe o fluxo contínuo. Pressione **`Ctrl + C`** para abortar imediatamente a execução.

4. **Experimente a limpeza de tela (`Ctrl + L`):**
   Em vez de digitar `clear`, pressione **`Ctrl + L`** para reposicionar o prompt no topo da tela.

5. **Histórico de comandos e busca reversa:**
   - Use a seta para cima `↑` e para baixo `↓` para navegar pelo histórico.
   - Pressione **`Ctrl + R`** e digite `shadow`. O terminal buscará no histórico o último comando que continha essa palavra. Pressione `Enter` para executá-lo ou `Esc` para cancelar.

---

### Atividade 0.3 — Explorando o FHS: Inspeção Prática das Pastas Fundamentais

**Objetivo:** Navegar pelas principais pastas do Linux e inspecionar o que realmente existe dentro de `/proc`, `/etc`, `/var/log` e `/dev`.

**Passos:**

1. **Inspecione a pasta `/etc` (O cérebro das configurações):**
   ```bash
   ls /etc/*.conf | head -n 10
   ```
   *Toda a configuração de rede, segurança e serviços reside aqui em texto simples.*

2. **Inspecione a pasta `/proc` (A ilusão de arquivos da memória RAM):**
   A pasta `/proc` não ocupa espaço em disco; são dados ao vivo do Kernel:
   ```bash
   # Quantos núcleos de CPU sua máquina possui:
   cat /proc/cpuinfo | grep "model name"
   
   # Quanta memória RAM total e livre:
   cat /proc/meminfo | head -n 5
   ```

3. **Inspecione a pasta `/dev` (Hardware como arquivos):**
   No Linux, tudo é arquivo. Discos, geradores de bytes e terminais vivem aqui:
   ```bash
   ls -l /dev/null /dev/zero /dev/urandom
   ```

4. **Inspecione a pasta `/var/log` (A caixa preta de eventos):**
   ```bash
   ls -lh /var/log/
   ```

5. **Validação visual no MobaXterm:**
   No painel SFTP à esquerda:
   - Clique no ícone de pasta com seta verde para subir até a raiz `/`.
   - Dê um duplo-clique em `/etc`, depois navegue até `/var/log`.
   - Observe visualmente como a árvore do FHS corresponde exatamente ao que você navegou via linha de comando.

---

[➡ Próximo: Módulo 1 — Navegação, Inspeção e Manipulação](modulo_01_navegacao_inspecao.md)

[⬅ Voltar ao Índice](curso_linux.md)
