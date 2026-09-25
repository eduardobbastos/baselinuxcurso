# 📘 Gabarito Comentado e Respostas dos Cenários do Curso

> **Material de apoio pedagógico para o instrutor e guia de autoestudo para os alunos.**  
> Contém as soluções detalhadas, justificativas de infraestrutura e boas práticas para todas as perguntas reflexivas e cenários de produção propostos nos 12 módulos.

---

## 📑 Navegação Rápida
- [Módulo 0 — Fundamentos do Terminal e FHS](#módulo-0--fundamentos-do-terminal-e-fhs)
- [Módulo 1 — Navegação, Inspeção e Manipulação](#módulo-1--navegação-inspeção-e-manipulação)
- [Módulo 2 — Busca Avançada e Manipulação de Baixo Nível](#módulo-2--busca-avançada-e-manipulação-de-baixo-nível)
- [Módulo 3 — Permissões, Posse e Execução](#módulo-3--permissões-posse-e-execução)
- [Módulo 4 — Automação e Scripts Shell](#módulo-4--automação-e-scripts-shell)
- [Módulo 5 — Conexão SSH e Acesso Remoto (WSL)](#módulo-5--conexão-ssh-e-acesso-remoto-wsl)
- [Módulo 6 — Gerenciamento de Usuários e Grupos](#módulo-6--gerenciamento-de-usuários-e-grupos)
- [Módulo 7 — Manipulação Avançada com dd](#módulo-7--manipulação-avançada-com-dd)
- [Módulo 8 — Visualização e Criação com cat](#módulo-8--visualização-e-criação-com-cat)
- [Módulo 9 — Editor de Texto nano](#módulo-9--editor-de-texto-nano)
- [Módulo 10 — Editor vi/vim](#módulo-10--editor-vivim)
- [Módulo 11 — Expansão Enterprise: Rocky Linux no WSL](#módulo-11--expansão-enterprise-rocky-linux-no-wsl)

---

## Módulo 0 — Fundamentos do Terminal e FHS

### Atividade 0.1: Pergunta Reflexiva (Caminhos Absolutos vs. Relativos)
- **Pergunta:** *Por que é mais seguro usar caminhos absolutos (iniciados com `/`) em scripts de automação?*
- **Resposta Técnica:** Caminhos relativos dependem estritamente do *working directory* atual (onde o operador ou o agendador `cron` estava posicionado no momento do disparo). Se um script contiver `rm -rf tmp/*` e for acionado a partir de `/`, ele poderá causar danos catastróficos. O caminho absoluto (ex: `rm -rf /home/app/tmp/*`) é determinístico e imune ao diretório de execução da chamada.

### Atividade 0.2: Segurança Operacional (Silent Input e Privilégios com `sudo`)
- **Pergunta/Conceito:** *Por que senhas no Linux não exibem asteriscos e por que `sudo` é preferível a logar como `root` direto?*
- **Resposta Técnica:** 
  1. **Silent Input:** Evita ataques de *shoulder surfing* (observação física do comprimento e ritmo da senha digitada).
  2. **Vantagem do `sudo`:** O login direto como `root` não deixa rastro individual em auditorias (não se sabe qual administrador executou o quê). Com `sudo`, o comando é gravado em `/var/log/auth.log` atrelando o timestamp e o comando exato ao login nominal do operador (`aluno ALL=(ALL) ALL`), garantindo rastreabilidade e princípio do menor privilégio.

---

## Módulo 1 — Navegação, Inspeção e Manipulação

### Atividade 1.1: Pergunta Reflexiva (Uso da flag `-h`)
- **Pergunta:** *Qual a diferença entre `-h` e sem `-h` na coluna de tamanho? Por que `4.0K` é mais útil que `4096` para um administrador?*
- **Resposta Técnica:** Sem `-h` (*human-readable*), o `ls` exibe o tamanho estritamente em bytes brutos. Quando lidamos com gigabytes ou terabytes, interpretar números como `10737418240` exige cálculo mental. O formato `-h` normaliza dinamicamente as ordens de grandeza (K, M, G, T) usando base 1024, permitindo triagem visual imediata durante incidentes de saturação de storage.

### Atividade 1.2: Cenário Real (Encontrar o maior log rapidamente)
- **Cenário:** *"O disco está cheio e preciso encontrar rapidamente o maior log para rotacionar."*
- **Comando Recomendado:**
  ```bash
  ls -lahS /var/log/ | head -n 10
  ```
  *(Dica extra de produção: `du -ah /var/log | sort -rh | head -n 10` para varrer também subdiretórios).*

### Atividade 1.3: Pergunta Reflexiva (`ls -R` vs `find`)
- **Pergunta:** *Em qual cenário o `ls -R` é preferível ao `find`? E vice-versa?*
- **Resposta Técnica:** `ls -R` é útil para inspeção visual rápida e humana da disposição espacial de diretórios pequenos. O `find` é obrigatório para automação, pipelines e diretórios grandes, pois não sobrecarrega o buffer de saída, consome menos memória e permite aplicar filtros analíticos (`-mtime`, `-size`, `-exec`).

### Atividade 1.4: Pergunta Reflexiva (`mkdir` com e sem `-p`)
- **Pergunta:** *O que acontece ao executar `mkdir lab_linux/conf` sem `-p` e com `-p` se ela já existir?*
- **Resposta Técnica:** Sem `-p`, o comando aborta com exit code `1` e emite `File exists`. Com `-p`, o comando opera de forma **idempotente**: cria diretórios pais se faltarem e não emite erro se o destino já existir, retornando exit code `0`. Essa idempotência é indispensável para scripts de deploy contínuo.

### Atividade 1.5: Cenário Real (Alteração deliberada de timestamps)
- **Cenário:** *Por que um administrador alteraria deliberadamente o timestamp com `touch -d`?*
- **Resposta Técnica:** Em testes de rotinas de expiração e expurgo (como jobs de logrotate ou scripts de retenção de backup), é arriscado esperar 30 dias reais. O `touch -d` simula arquivos criados no passado para validar se os filtros de `find -mtime +30 -delete` funcionam conforme o esperado antes da entrada em produção.

### Atividade 1.6: Pergunta Reflexiva (Brace Expansion `{01..10}` vs `{1..10}`)
- **Pergunta:** *Qual a diferença entre `{01..10}` e `{1..10}`?*
- **Resposta Técnica:** `{01..10}` força o preenchimento de zeros à esquerda (*zero-padding*). Isso é crítico para ordenação lexicográfica de logs. Sem zeros à esquerda, o comando `ls` lista na sequência: `1, 10, 2, 3...`, quebrando a ordem cronológica visual.

---

## Módulo 2 — Busca Avançada e Manipulação de Baixo Nível

### Atividade 2.1: Pergunta Reflexiva (`-name` vs `-iname`)
- **Pergunta:** *Qual a diferença entre `-name` e `-iname`?*
- **Resposta Técnica:** `-name` é sensível a maiúsculas/minúsculas (*case-sensitive*). `-iname` ignora a caixa (*case-insensitive*). É vital em ambientes mistos ou arquivos originados de Windows/SMB onde extensões variam entre `.csv`, `.CSV` e `.Csv`.

### Atividade 2.2: Cenário Real (Limpar logs antigos e pesados)
- **Cenário:** *Logs com mais de 30 dias e maiores que 10MB.*
- **Comando Recomendado:**
  ```bash
  find /var/log/ -type f -name "*.log" -mtime +30 -size +10M -exec ls -lh {} \;
  ```

### Atividade 2.3: Pergunta Reflexiva (`-exec ... \;` vs `-exec ... +`)
- **Pergunta:** *Qual a diferença entre `\;` e `+` no final do `-exec`?*
- **Resposta Técnica:** Com `\;`, o `find` instancia um processo novo do binário para cada arquivo encontrado (se houver 5000 arquivos, haverá 5000 execuções de `mv` ou `rm`). Com `+`, o `find` agrupa múltiplos arquivos como argumentos de uma única chamada do comando (semelhante ao `xargs`), reduzindo drasticamente o consumo de CPU e chamadas de sistema (*syscalls*).

### Atividade 2.4: Pergunta Reflexiva (Cálculo com `dd`)
- **Pergunta:** *Se `bs=1M count=50` dá 50MB, quanto gera `bs=512 count=2048`?*
- **Resposta:** `512 bytes * 2048 blocos = 1.048.576 bytes = exatamente 1 MB`.

### Atividade 2.5: Cenário Real (Incompressibilidade de dados pseudoaleatórios)
- **Cenário:** *Por que `/dev/urandom` não comprime e `/dev/zero` comprime quase 100%?*
- **Resposta Técnica:** Algoritmos como Gzip/Deflate baseiam-se na busca por redundâncias e padrões repetitivos (entropia baixa). `/dev/zero` possui entropia mínima, reduzindo centenas de megabytes a poucos bytes. `/dev/urandom` possui alta entropia (quase ruído puro), impossibilitando encontrar repetições; tentar comprimi-lo geralmente resulta em um arquivo ligeiramente maior devido aos headers do formato.

---

## Módulo 3 — Permissões, Posse e Execução

### Atividade 3.2: Exercício Mental de Permissões Octais
- **Caso A:** `chmod 421 arquivo.txt`  
  - Dono: `4` (`r--`)
  - Grupo: `2` (`-w-`)
  - Outros: `1` (`--x`)  
  - Representação: `r---w---x`
- **Caso B:** `chmod u=r,g=w,o=x arquivo.txt`  
  - Resultado idêntico: `r---w---x` (octal 421).

### Atividade 3.3: Pergunta Reflexiva (Por que `chown` exige `sudo`?)
- **Pergunta:** *Por que `chown` requer `sudo` mas `chmod` não?*
- **Resposta Técnica:** Qualquer usuário comum pode gerenciar permissões (`chmod`) daquilo que já lhe pertence. Porém, permitir que um usuário altere a posse (`chown`) de um arquivo para outro usuário abriria brechas graves de segurança: um usuário mal-intencionado poderia burlar cotas de disco criando arquivos gigantes e transferindo o dono para outro usuário, ou criar arquivos binários com permissões especiais para armadilhas de escalonamento.

---

## Módulo 4 — Automação e Scripts Shell

### Atividade 4.1: Tratamento de Diretório Inexistente
- **Solução Defensiva:**
  ```bash
  if [ ! -d "$ORIGEM" ]; then
      echo "[$DATA_EXEC] ERRO CRÍTICO: Origem '$ORIGEM' não existe." >> "$LOG_ARQUIVO"
      exit 2
  fi
  ```

### Atividade 4.2: Script com Múltiplos Diretórios
- **Solução:** Substituir `$1` por um loop iterando sobre todos os argumentos recebidos (`"$@"`):
  ```bash
  for DIR in "$@"; do
      echo ">>> Processando diretório: $DIR"
      # Rotinas de validação e análise...
  done
  ```

---

## Módulo 5 — Conexão SSH e Acesso Remoto (WSL)

### Atividade 5.3: Criptografia Moderna (`ed25519` vs `rsa`)
- **Pergunta:** *Por que usar `ed25519` em vez de chaves legadas `rsa`?*
- **Resposta Técnica:** O algoritmo Ed25519 é baseado em Curvas Elípticas (EdDSA). Uma chave Ed25519 de apenas **256 bits** oferece segurança comparável ou superior a uma chave RSA de **4096 bits**. Além de muito menor (cabe em uma linha curta de terminal), é imune a ataques de temporização e realiza assinaturas de handshake muito mais rápidas.

---

## Módulo 6 — Gerenciamento de Usuários e Grupos

### Atividade 6.1: `useradd` vs `adduser`
- **Resposta Técnica:** `useradd` é o binário nativo padrão POSIX/Linux de baixo nível; por padrão não cria pasta home, não copia arquivos esqueleto (`/etc/skel`) e não define senha a menos que receba flags explícitas (`-m`, `-s`). É a ferramenta ideal para scripts e automações em lote. Já `adduser` é um script interativo de alto nível em Perl (comum em Debian/Ubuntu) voltado para uso humano interativo.

### Atividade 6.3: Risco do `userdel -r` em Produção
- **Resposta:** O parâmetro `-r` exclui permanentemente o diretório home e o spool de emails do usuário. Em auditorias corporativas, apagar sem backup pode destruir evidências ou arquivos de projetos compartilhados. A boa prática corporativa é: bloquear a conta (`usermod -L`), revogar chaves SSH em `authorized_keys`, arquivar a home (`tar -czf /backups/home_usuario.tar.gz /home/usuario`) e somente depois executar a exclusão.

---

## Módulo 7 — Manipulação Avançada com dd

### Atividade 7.1: Block Size e Syscalls
- **Pergunta:** *Por que `bs=1M` é dezenas de vezes mais rápido que `bs=1K`?*
- **Resposta Técnica:** Para transferir 50MB com `bs=1K`, o kernel precisa alternar de contexto entre userspace e kernelspace 51.200 vezes para executar chamadas de sistema `read()` e `write()`. Com `bs=1M`, são necessárias apenas 50 chamadas de sistema, reduzindo a sobrecarga da CPU ao mínimo.

---

## Módulo 8 — Visualização e Criação com cat

### Atividade 8.1: A flag `cat -A` e o problema de CRLF
- **Resposta Técnica:** `cat -A` expõe os finais de linha (`$` para Linux `\n`, `^M$` para Windows `\r\n`) e tabs (`^I`). Scripts Bash escritos ou editados no Windows frequentemente quebram no Linux com o erro `\r: command not found`. O `cat -A` diagnostica essa inconsistência imediatamente.

### Atividade 8.3: UUOC ("Useless Use of Cat")
- **Pergunta:** *`cat arquivo | grep padrao` vs `grep padrao arquivo`*
- **Resposta Técnica:** Invocar `cat arquivo | grep ...` cria dois processos e um pipe intermediário desnecessário no kernel. O comando `grep padrao arquivo` é direto: instancia apenas um processo e lê o arquivo via descritor próprio, sendo mais performático e elegante.

---

## Módulo 9 — Editor de Texto nano

### Atividade 9.1: Quando usar nano vs vi?
- **Resposta Técnica:** `nano` é excelente para edições rápidas de arquivos de configuração pontuais e para operadores em início de curva de aprendizado, pois exibe o mapa de atalhos na tela. No entanto, é limitado para manipulações complexas de bloco, automação com macros e não está presente em imagens de contêineres mínimas (ex: Alpine, Distroless) nem em discos de resgate.

---

## Módulo 10 — Editor vi/vim

### Atividade 10.1: A importância do modo modal
- **Resposta:** A filosofia modal permite que todas as teclas do teclado se transformem em comandos rápidos de navegação e manipulação de texto sem a necessidade de combinações desconfortáveis de teclas (como Ctrl ou Alt contínuos). Pressionar `Esc` é o ato de ancorar o editor de volta ao estado seguro e de comando.



---

## Módulo 11 — Expansão Enterprise: Rocky Linux no WSL

### Atividade 11.1: Geração de Chaves de Host no ecossistema RHEL
- **Pergunta Técnica:** *Por que o Rocky Linux / RHEL pode exigir `ssh-keygen -A` antes de iniciar o daemon SSH?*
- **Resposta Técnica:** Em distribuições derivadas do Red Hat minimalistas (como imagens de contêineres e rootfs para cloud/WSL), as chaves criptográficas de host (`ssh_host_rsa_key`, `ssh_host_ed25519_key`, etc.) não vêm pré-geradas por padrão de fábrica para evitar que múltiplos nós compartilhem os mesmos segredos criptográficos. O comando `ssh-keygen -A` analisa `/etc/ssh/` e gera automaticamente apenas as chaves ausentes.

### Atividade 11.2 e 11.3: Portabilidade e Padrão POSIX
- **Síntese de Infraestrutura:** *Quais comandos mudam entre Ubuntu e Rocky Linux na manipulação de arquivos?*
- **Resposta:** **Nenhum.** Utilitários fundamentais como `ls`, `mkdir`, `cp`, `mv`, `touch`, `find` e `dd` fazem parte do pacote `coreutils`/`findutils` e cumprem a especificação POSIX. A diferença entre famílias de distribuições reside nos subsistemas de governança e ciclo de vida:
  - Gerenciamento de pacotes (`apt` vs `dnf`);
  - Estrutura de logs do sistema (`/var/log/syslog` vs `/var/log/messages`);
  - Módulos de segurança do Kernel (AppArmor no Ubuntu vs SELinux no RHEL/Rocky);
  - Políticas de firewall e crypto-policies corporativas.
