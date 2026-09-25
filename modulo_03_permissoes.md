# Módulo 3 — Permissões, Posse e Execução

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender o modelo Unix de permissões (User, Group, Others).
- Dominar a notação octal (4-2-1) e simbólica (u+x, g-w, o=r).
- Aplicar permissões com `chmod` em cenários reais.
- Alterar proprietário e grupo de arquivos com `chown`.
- Diagnosticar erros de "Permission denied" e corrigi-los adequadamente.

---

## 6. Gestão de Acessos com `chmod` e `chown`

### Conceito-Chave

Todo arquivo e diretório no Linux possui três conjuntos de permissões:

| Conjunto | Letra | Descrição |
|----------|-------|-----------|
| **User (u)** | `u` | O proprietário do arquivo |
| **Group (g)** | `g` | O grupo associado ao arquivo |
| **Others (o)** | `o` | Todos os demais usuários |

Cada conjunto pode ter três tipos de acesso:

| Permissão | Letra | Valor Octal | Efeito em Arquivo | Efeito em Diretório |
|-----------|-------|-------------|-------------------|--------------------|
| Leitura | `r` | 4 | Ler conteúdo | Listar conteúdo (`ls`) |
| Escrita | `w` | 2 | Modificar conteúdo | Criar/remover arquivos dentro |
| Execução | `x` | 1 | Executar como programa | Entrar no diretório (`cd`) |

### Tabela de Referência Octal

| Octal | Binário | Permissões |
|-------|---------|------------|
| `0` | `000` | `---` |
| `1` | `001` | `--x` |
| `2` | `010` | `-w-` |
| `3` | `011` | `-wx` |
| `4` | `100` | `r--` |
| `5` | `101` | `r-x` |
| `6` | `110` | `rw-` |
| `7` | `111` | `rwx` |

### Exemplos Comuns

| Octal | Significado |
|-------|-------------|
| `755` | `rwxr-xr-x` — Dono faz tudo; grupo e outros leem e executam |
| `644` | `rw-r--r--` — Dono lê e escreve; grupo e outros apenas leem |
| `700` | `rwx------` — Apenas o dono tem acesso |
| `750` | `rwxr-x---` — Dono faz tudo; grupo lê e executa; outros sem acesso |

---

### Atividade 3.1 — Entendendo "Permission Denied" e Aplicando `chmod`

**Objetivo:** Criar um script, enfrentar o erro de permissão e corrigi-lo com `chmod` usando notação simbólica.

**Passos:**

1. Crie um script de teste:

```bash
echo '#!/bin/bash' > lab_linux/scripts/sysinfo.sh
echo 'echo "=== Informações do Sistema ==="' >> lab_linux/scripts/sysinfo.sh
echo 'echo "Hostname: $(hostname)"' >> lab_linux/scripts/sysinfo.sh
echo 'echo "Kernel: $(uname -r)"' >> lab_linux/scripts/sysinfo.sh
echo 'echo "Uptime: $(uptime -p)"' >> lab_linux/scripts/sysinfo.sh
echo 'echo "Usuário: $(whoami)"' >> lab_linux/scripts/sysinfo.sh
echo 'echo "Data: $(date)"' >> lab_linux/scripts/sysinfo.sh
```

2. Verifique as permissões atuais:

```bash
ls -l lab_linux/scripts/sysinfo.sh
```

3. Tente executar (vai falhar):

```bash
./lab_linux/scripts/sysinfo.sh
```

O terminal retornará: `Permission denied`

4. Aplique permissão de execução ao dono:

```bash
chmod u+x lab_linux/scripts/sysinfo.sh
```

5. Execute novamente:

```bash
./lab_linux/scripts/sysinfo.sh
```

6. Verifique a mudança:

```bash
ls -l lab_linux/scripts/sysinfo.sh
```

**No MobaXterm:** Clique direito no arquivo pelo SFTP → Properties e observe a representação gráfica das permissões.

---

### Atividade 3.2 — Notação Octal vs. Simbólica

**Objetivo:** Praticar as duas notações de `chmod` e entender quando cada uma é mais prática.

**Passos:**

1. Configure permissões usando notação **octal** (dono lê/escreve/executa, grupo lê/executa, outros nada):

```bash
chmod 750 lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh
```

2. Configure usando notação **simbólica** equivalente:

```bash
chmod u=rwx,g=rx,o= lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh
```

3. Teste diferentes combinações e preveja o resultado antes de verificar:

```bash
# Apenas dono tem acesso total
chmod 700 lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh

# Todos leem, apenas dono escreve
chmod 644 lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh

# Adicione execução para todos
chmod a+x lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh

# Remova escrita do grupo
chmod g-w lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh
```

4. Aplique permissões recursivamente a um diretório:

```bash
chmod -R 755 lab_linux/scripts/
ls -lR lab_linux/scripts/
```

**Exercício mental:** Sem executar, diga qual será a saída de `ls -l` após:
- `chmod 421 arquivo.txt` → ?
- `chmod u=r,g=w,o=x arquivo.txt` → ?

---

### Atividade 3.3 — Alteração de Proprietário com `chown`

**Objetivo:** Alterar o proprietário e grupo de arquivos e entender por que isso requer privilégios elevados.

**Passos:**

1. Verifique o proprietário atual:

```bash
ls -l lab_linux/scripts/sysinfo.sh
```

2. Tente mudar o proprietário sem sudo (vai falhar):

```bash
chown root:root lab_linux/scripts/sysinfo.sh
```

3. Use `sudo` para alterar:

```bash
sudo chown root:root lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh
```

4. Tente executar o script agora:

```bash
./lab_linux/scripts/sysinfo.sh
```

5. Observe que mesmo sendo de `root`, se as permissões de "others" incluírem `x`, qualquer usuário pode executar.

6. Restaure o proprietário para o aluno:

```bash
sudo chown $(whoami):$(id -gn) lab_linux/scripts/sysinfo.sh
ls -l lab_linux/scripts/sysinfo.sh
```

7. Altere apenas o grupo de todo o diretório de scripts:

```bash
sudo chown -R :$(id -gn) lab_linux/scripts/
ls -l lab_linux/scripts/
```

**Cenário real:** Um deploy automatizado copia arquivos como `root`. Os serviços que rodam como usuário `www-data` não conseguem ler. Solução: `chown -R www-data:www-data /var/www/html`.

**Pergunta reflexiva:** Por que `chown` requer `sudo` mas `chmod` nem sempre? Pense na diferença entre alterar permissões de algo que você possui vs. transferir a posse.

---

> **💡 Dica MobaXterm:** No painel SFTP, clique com botão direito em qualquer arquivo → Properties. A janela mostra as permissões em formato gráfico (checkboxes) e octal. Altere diretamente pela interface e observe o efeito com `ls -l` no terminal.

---

[⬅ Anterior: Módulo 2 — Busca e Manipulação](modulo_02_busca_manipulacao.md)

[➡ Próximo: Módulo 4 — Automação e Scripts Shell](modulo_04_automacao_scripts.md)

[⬅ Voltar ao Índice](curso_linux.md)
