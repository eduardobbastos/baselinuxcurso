# Módulo 1 — Navegação, Inspeção e Manipulação de Arquivos

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Dominar a listagem detalhada de arquivos e diretórios com `ls` e suas flags essenciais.
- Criar estruturas de diretórios de forma eficiente com `mkdir -p`.
- Compreender o duplo papel do `touch` (criação de arquivos vazios e alteração de timestamps).
- Realizar cópias e movimentações preservando atributos com `cp` e `mv`.
- Utilizar o painel SFTP do MobaXterm para validação visual.

---

## 1. Inspeção com `ls`

### Conceito-Chave

O comando `ls` lista o conteúdo de diretórios. Com as flags adequadas, ele expõe **metadados completos**: tipo de arquivo, permissões, links físicos, proprietário, grupo, tamanho e carimbo de data/hora. É a principal ferramenta de reconhecimento rápido do estado de um diretório.

### Flags Essenciais

| Flag | Descrição |
|------|-------------------------------------------|
| `-l` | Formato longo (long listing) |
| `-a` | Exibe arquivos ocultos (iniciados por `.`) |
| `-h` | Tamanhos em formato legível (KB, MB, GB) |
| `-t` | Ordena por data de modificação (mais recente primeiro) |
| `-R` | Listagem recursiva de subdiretórios |
| `-S` | Ordena por tamanho (maior primeiro) |
| `-r` | Inverte a ordem de classificação |

### Anatomia da Saída de `ls -l`

```
-rw-r--r-- 1 ecosta grupo 4096 Jan 15 10:30 arquivo.txt
│           │ │      │     │    │              └─ Nome
│           │ │      │     │    └─ Data/hora de modificação
│           │ │      │     └─ Tamanho em bytes
│           │ │      └─ Grupo proprietário
│           │ └─ Usuário proprietário
│           └─ Número de hard links
└─ Tipo + Permissões (rwx para user/group/others)
```

### Atividade 1.1 — Reconhecimento de Metadados em `/var/log`

**Objetivo:** Identificar cada campo da saída longa do `ls` e compreender o significado de cada coluna.

**Passos:**

1. Execute o comando abaixo para listar todos os arquivos (incluindo ocultos) com detalhes legíveis:

```bash
ls -lah /var/log
```

2. Analise a saída e identifique para pelo menos 3 arquivos:
   - Tipo de arquivo (primeiro caractere: `-` arquivo regular, `d` diretório, `l` link simbólico)
   - Permissões do proprietário, grupo e outros
   - Proprietário e grupo
   - Tamanho em formato legível
   - Data da última modificação

3. Compare com a listagem simples:

```bash
ls /var/log
```

**Validação no MobaXterm:** No painel SFTP, navegue até `/var/log` e confira se os arquivos ocultos aparecem (ative a exibição de arquivos ocultos nas configurações do SFTP).

**Pergunta reflexiva:** Qual a diferença entre `-h` e sem `-h` na coluna de tamanho? Por que `4.0K` é mais útil que `4096` para um administrador?

---

### Atividade 1.2 — Ordenação por Tempo e Tamanho

**Objetivo:** Utilizar critérios de ordenação para identificar rapidamente os arquivos mais recentes e os maiores de um diretório.

**Passos:**

1. Liste os 10 arquivos mais recentemente modificados em `/var/log`:

```bash
ls -laht /var/log | head -n 10
```

2. Agora liste os 10 maiores arquivos:

```bash
ls -lahS /var/log | head -n 10
```

3. Inverta a ordenação para ver os menores/mais antigos:

```bash
ls -lahtr /var/log | head -n 10
ls -lahSr /var/log | head -n 10
```

4. Combine com `grep` para filtrar apenas arquivos `.log`:

```bash
ls -laht /var/log | grep "\.log"
```

**Cenário real:** "O disco está cheio e preciso encontrar rapidamente o maior log para rotacionar." — Qual comando você usaria?

---

### Atividade 1.3 — Listagem Recursiva e Exploração de Árvore

**Objetivo:** Visualizar a estrutura completa de diretórios de forma recursiva e comparar com o comando `tree`.

**Passos:**

1. Crie uma estrutura de teste:

```bash
mkdir -p /tmp/teste_ls/{nivel1/{nivel2a,nivel2b},nivel1b}
touch /tmp/teste_ls/nivel1/nivel2a/arq1.txt
touch /tmp/teste_ls/nivel1/nivel2b/arq2.txt
touch /tmp/teste_ls/nivel1b/arq3.txt
```

2. Liste recursivamente:

```bash
ls -laR /tmp/teste_ls/
```

3. Se o comando `tree` estiver disponível, compare:

```bash
tree /tmp/teste_ls/
```

4. Conte quantos arquivos regulares existem na árvore:

```bash
ls -laR /tmp/teste_ls/ | grep "^-" | wc -l
```

**Pergunta reflexiva:** Em qual cenário o `ls -R` é preferível ao `find`? E vice-versa?

---

## 2. Criação de Estruturas com `mkdir` e `touch`

### Conceito-Chave

- **`mkdir`** cria diretórios. Com `-p`, cria toda a cadeia de diretórios pai sem erro se já existirem.
- **`touch`** tem duplo papel:
  - Se o arquivo **não existe**: cria um arquivo vazio (0 bytes).
  - Se o arquivo **já existe**: atualiza os timestamps (mtime e atime) sem alterar o conteúdo.

### Flags Essenciais

| Comando | Flag | Descrição |
|---------|------|-------------------------------------------|
| `mkdir` | `-p` | Cria diretórios pai intermediários |
| `mkdir` | `-v` | Modo verboso (exibe cada diretório criado) |
| `touch` | `-t` | Define timestamp no formato `[[CC]YY]MMDDhhmm[.ss]` |
| `touch` | `-d` | Define timestamp com string de data legível |
| `touch` | `-r` | Copia o timestamp de um arquivo de referência |

### Atividade 1.4 — Construção de Árvore de Projeto

**Objetivo:** Criar uma estrutura completa de diretórios para um projeto de laboratório em um único comando.

**Passos:**

1. Crie a árvore completa do projeto:

```bash
mkdir -pv lab_linux/{conf,data/raw,data/processed,scripts,logs,backup,tmp}
```

2. Verifique a estrutura criada:

```bash
ls -R lab_linux/
```

3. No painel SFTP do MobaXterm, clique em *Refresh* e navegue pela árvore criada. Confirme que todas as pastas existem.

4. Crie arquivos de marcação em cada diretório:

```bash
touch lab_linux/conf/app.conf
touch lab_linux/conf/database.ini
touch lab_linux/scripts/deploy.sh
touch lab_linux/scripts/monitor.sh
touch lab_linux/scripts/cleanup.sh
```

5. Valide:

```bash
find lab_linux/ -type f
```

**Pergunta reflexiva:** O que acontece se você executar `mkdir lab_linux/conf` novamente (sem `-p`)? E com `-p`?

---

### Atividade 1.5 — Manipulação de Timestamps com `touch`

**Objetivo:** Compreender e manipular deliberadamente os timestamps de arquivos.

**Passos:**

1. Verifique o timestamp atual do arquivo:

```bash
ls -l lab_linux/conf/app.conf
stat lab_linux/conf/app.conf
```

2. Altere a data de modificação para uma data específica no passado:

```bash
touch -d "2024-01-01 10:00:00" lab_linux/conf/app.conf
ls -l lab_linux/conf/app.conf
```

3. Altere usando o formato numérico (`-t`):

```bash
touch -t 202306151430.00 lab_linux/conf/database.ini
ls -l lab_linux/conf/database.ini
```

4. Copie o timestamp de um arquivo para outro usando `-r`:

```bash
touch -r lab_linux/conf/app.conf lab_linux/conf/database.ini
ls -l lab_linux/conf/
```

5. Confirme com `stat` que ambos possuem o mesmo timestamp:

```bash
stat lab_linux/conf/app.conf lab_linux/conf/database.ini
```

**Cenário real:** Por que um administrador alteraria deliberadamente o timestamp de um arquivo? (Exemplo: restaurar backups com datas originais, testes de rotação de logs por antiguidade.)

---

### Atividade 1.6 — Criação em Massa com Brace Expansion

**Objetivo:** Utilizar a expansão de chaves do Bash para criar múltiplos arquivos e diretórios de forma eficiente.

**Passos:**

1. Crie 12 diretórios de meses:

```bash
mkdir -p lab_linux/logs/{jan,fev,mar,abr,mai,jun,jul,ago,set,out,nov,dez}
```

2. Crie arquivos de log numerados:

```bash
touch lab_linux/logs/jan/evento_{001..010}.log
```

3. Verifique a expansão:

```bash
echo lab_linux/logs/jan/evento_{001..010}.log
ls -l lab_linux/logs/jan/
```

4. Crie uma série de arquivos com prefixos variados:

```bash
touch lab_linux/data/raw/relatorio_{2024..2026}_Q{1..4}.csv
ls lab_linux/data/raw/
```

5. Conte quantos arquivos foram criados:

```bash
ls lab_linux/data/raw/*.csv | wc -l
```

**Pergunta reflexiva:** Qual a diferença entre `{01..10}` e `{1..10}` na expansão? Teste ambos com `echo`.

---

## 3. Cópia e Movimentação com `cp` e `mv`

### Conceito-Chave

- **`cp`** duplica arquivos ou diretórios. O original permanece intacto.
- **`mv`** move ou renomeia. No mesmo filesystem, é instantâneo (apenas atualiza o ponteiro no inode). Entre filesystems diferentes, realiza cópia + remoção.
- A flag `-a` (archive) do `cp` preserva tudo: permissões, timestamps, links simbólicos e proprietários.

### Flags Essenciais

| Comando | Flag | Descrição |
|---------|------|-------------------------------------------|
| `cp` | `-r` | Cópia recursiva de diretórios |
| `cp` | `-a` | Archive: recursivo + preserva tudo |
| `cp` | `-i` | Pergunta antes de sobrescrever |
| `cp` | `-v` | Modo verboso |
| `mv` | `-i` | Pergunta antes de sobrescrever |
| `mv` | `-u` | Move apenas se origem for mais recente |
| `mv` | `-v` | Modo verboso |

### Atividade 1.7 — Cópia Simples vs. Cópia com Preservação de Atributos

**Objetivo:** Comparar o comportamento de `cp` com e sem a flag `-a` e entender a preservação de metadados.

**Passos:**

1. Garanta que o `app.conf` tem um timestamp alterado (do exercício anterior):

```bash
ls -l lab_linux/conf/app.conf
```

2. Faça uma cópia simples:

```bash
cp lab_linux/conf/app.conf lab_linux/conf/app.conf.bak
```

3. Faça uma cópia preservando atributos:

```bash
cp -a lab_linux/conf/app.conf lab_linux/conf/app.conf.archive
```

4. Compare os três arquivos:

```bash
ls -l lab_linux/conf/app.conf*
```

5. Observe que:
   - `app.conf.bak` → timestamp **atual** (momento da cópia)
   - `app.conf.archive` → timestamp **original** de 2024-01-01 (preservado pelo `-a`)

**No MobaXterm:** Clique com botão direito em cada arquivo no SFTP → Properties e compare as datas.

---

### Atividade 1.8 — Movimentação, Renomeação e Flag `-u`

**Objetivo:** Diferenciar entre mover e renomear, e usar a flag `-u` para atualizações condicionais.

**Passos:**

1. Mova o backup para outro diretório (isso é **mover**):

```bash
mv -v lab_linux/conf/app.conf.bak lab_linux/data/raw/old_config.txt
```

2. Renomeie um arquivo no mesmo diretório (isso é **renomear**):

```bash
mv -v lab_linux/scripts/cleanup.sh lab_linux/scripts/limpar_temp.sh
```

3. Teste a flag `-u` (move apenas se a origem for mais recente):

```bash
touch lab_linux/tmp/versao_nova.txt
touch -d "2020-01-01" lab_linux/tmp/versao_antiga.txt

# Este NÃO irá substituir (a origem é mais antiga que o destino)
cp lab_linux/tmp/versao_nova.txt lab_linux/tmp/destino.txt
mv -u lab_linux/tmp/versao_antiga.txt lab_linux/tmp/destino.txt
ls -l lab_linux/tmp/destino.txt
```

4. Confirme que o `destino.txt` manteve o timestamp mais recente.

**Pergunta reflexiva:** Em um cenário de deploy, por que a flag `-u` pode ser útil?

---

### Atividade 1.9 — Cópia Recursiva de Diretórios Inteiros

**Objetivo:** Copiar árvores de diretórios completas e entender a diferença entre `-r` e `-a`.

**Passos:**

1. Copie toda a estrutura `data/` para um backup:

```bash
cp -rv lab_linux/data/ lab_linux/backup/data_copia/
```

2. Agora faça uma cópia com preservação total:

```bash
cp -av lab_linux/data/ lab_linux/backup/data_archive/
```

3. Compare os timestamps dos arquivos nas duas cópias:

```bash
ls -lR lab_linux/backup/data_copia/
ls -lR lab_linux/backup/data_archive/
```

4. Verifique que a cópia com `-a` manteve os timestamps originais.

5. Conte o total de arquivos em cada cópia:

```bash
find lab_linux/backup/data_copia/ -type f | wc -l
find lab_linux/backup/data_archive/ -type f | wc -l
```

**Cenário real:** "Preciso fazer uma cópia fiel de um diretório de configuração antes de aplicar uma atualização." — Qual flag usar?

---

> **💡 Dica MobaXterm:** Após cada atividade, clique em *Refresh* no painel SFTP para ver as alterações refletidas visualmente. Use o clique direito → Properties para inspecionar permissões e timestamps.

---

[⬅ Anterior: Módulo 0 — Fundamentos do Terminal](modulo_00_fundamentos_terminal.md)

[➡ Próximo: Módulo 2 — Busca e Manipulação de Baixo Nível](modulo_02_busca_manipulacao.md)

[⬅ Voltar ao Índice](curso_linux.md)
