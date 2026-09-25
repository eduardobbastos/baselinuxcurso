# Módulo 2 — Busca Avançada e Manipulação de Baixo Nível

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Dominar o `find` para localização avançada de arquivos com filtros por tipo, nome, tamanho e tempo.
- Compreender a diferença entre busca indexada (`locate`) e varredura em tempo real (`find`).
- Utilizar `-exec` para encadear ações nos resultados do `find`.
- Entender o `dd` como ferramenta de manipulação de blocos de dados.
- Gerar arquivos de teste com tamanhos controlados para validações e simulações.

---

## 4. Localização Avançada com `find`

### Conceito-Chave

| Ferramenta | Método | Velocidade | Precisão |
|------------|--------|-----------|----------|
| `locate` | Busca em índice pré-construído (`updatedb`) | Muito rápida | Pode estar desatualizada |
| `find` | Varredura real do filesystem em tempo real | Mais lenta | Sempre precisa |

O `find` é a ferramenta definitiva quando você precisa de **precisão**, **filtros compostos** e **ações automáticas** sobre os resultados.

### Critérios Essenciais

| Critério | Descrição | Exemplo |
|----------|-------------------------------------------|---------|
| `-type f` | Apenas arquivos regulares | `find . -type f` |
| `-type d` | Apenas diretórios | `find . -type d` |
| `-name` | Nome exato (case-sensitive) | `find . -name "*.log"` |
| `-iname` | Nome (case-insensitive) | `find . -iname "*.LOG"` |
| `-size` | Tamanho (`+10M` maior que, `-1k` menor que) | `find . -size +5M` |
| `-mtime` | Modificado há N dias (`+7` = mais de 7 dias) | `find . -mtime +30` |
| `-mmin` | Modificado há N minutos | `find . -mmin -60` |
| `-perm` | Permissões específicas | `find . -perm 755` |
| `-exec` | Executa comando em cada resultado | `find . -exec ls -l {} \;` |
| `-delete` | Remove os arquivos encontrados | `find . -name "*.tmp" -delete` |

### Atividade 2.1 — Busca por Nome e Tipo

**Objetivo:** Localizar arquivos e diretórios específicos dentro da árvore `lab_linux` usando critérios de nome e tipo.

**Passos:**

1. Crie arquivos de teste com diferentes extensões:

```bash
touch lab_linux/data/raw/dados_{01..05}.csv
touch lab_linux/data/raw/planilha_{01..03}.xlsx
touch lab_linux/data/raw/nota_{01..03}.txt
touch lab_linux/logs/sistema.log
touch lab_linux/logs/erro.log
touch lab_linux/logs/acesso.log
```

2. Busque apenas arquivos `.csv`:

```bash
find lab_linux/ -type f -name "*.csv"
```

3. Busque todos os arquivos de log (`.log`):

```bash
find lab_linux/ -type f -name "*.log"
```

4. Busque apenas diretórios:

```bash
find lab_linux/ -type d
```

5. Busque usando padrão case-insensitive:

```bash
touch lab_linux/data/raw/RELATORIO.CSV
find lab_linux/ -type f -iname "*.csv"
```

6. Combine critérios com operadores lógicos (arquivos `.csv` OU `.txt`):

```bash
find lab_linux/ -type f \( -name "*.csv" -o -name "*.txt" \)
```

**Pergunta reflexiva:** Qual a diferença entre `-name` e `-iname`? Em qual cenário a busca case-insensitive é essencial?

---

### Atividade 2.2 — Busca por Tamanho e Tempo de Modificação

**Objetivo:** Filtrar arquivos por tamanho e idade para cenários administrativos reais.

**Passos:**

1. Crie arquivos de tamanhos variados:

```bash
dd if=/dev/zero of=lab_linux/data/raw/pequeno.dat bs=1K count=10 2>/dev/null
dd if=/dev/zero of=lab_linux/data/raw/medio.dat bs=1K count=500 2>/dev/null
dd if=/dev/zero of=lab_linux/data/raw/grande.dat bs=1M count=5 2>/dev/null
```

2. Encontre arquivos maiores que 100KB:

```bash
find lab_linux/ -type f -size +100k
```

3. Encontre arquivos menores que 1KB:

```bash
find lab_linux/ -type f -size -1k
```

4. Encontre arquivos dentro de uma faixa de tamanho (entre 100KB e 1MB):

```bash
find lab_linux/ -type f -size +100k -size -1M
```

5. Encontre arquivos modificados nos últimos 60 minutos:

```bash
find lab_linux/ -type f -mmin -60
```

6. Encontre arquivos com timestamp antigo (do exercício de `touch -d`):

```bash
find lab_linux/ -type f -mtime +365
```

**Cenário real:** "Preciso encontrar todos os arquivos de log com mais de 30 dias e maiores que 10MB para liberar espaço." — Monte o comando.

---

### Atividade 2.3 — Execução de Ações com `-exec` e `-delete`

**Objetivo:** Encadear comandos nos resultados do `find` para automatizar tarefas administrativas.

**Passos:**

1. Liste detalhes de cada arquivo `.csv` encontrado:

```bash
find lab_linux/ -type f -name "*.csv" -exec ls -lh {} \;
```

2. Renomeie todos os `.csv` para `.csv.bkp` usando `-exec`:

```bash
find lab_linux/data/raw/ -type f -name "*.csv" -exec mv {} {}.bkp \;
ls -l lab_linux/data/raw/
```

3. Copie todos os `.bkp` para o diretório de backup:

```bash
find lab_linux/data/raw/ -type f -name "*.bkp" -exec cp {} lab_linux/backup/ \;
ls lab_linux/backup/
```

4. Use `-exec` com confirmação interativa (substituto seguro):

```bash
find lab_linux/tmp/ -type f -name "*.txt" -ok rm {} \;
```

5. Demonstração de `-delete` (cuidado!):

```bash
# Primeiro, SEMPRE faça um dry-run (sem -delete) para confirmar os alvos:
find lab_linux/tmp/ -type f -name "versao_*"

# Só então execute a remoção:
find lab_linux/tmp/ -type f -name "versao_*" -delete
```

> ⚠️ **ATENÇÃO:** Sempre execute o `find` sem `-delete` primeiro para visualizar os alvos. A flag `-delete` é irreversível.

**Pergunta reflexiva:** Qual a diferença entre `\;` e `+` no final de `-exec`? Teste:

```bash
find lab_linux/ -type f -name "*.log" -exec echo {} \;
find lab_linux/ -type f -name "*.log" -exec echo {} +
```

---

## 5. Manipulação de Baixo Nível com `dd`

### Conceito-Chave

O `dd` (Dataset Definition) opera no nível de **blocos de dados**, lendo de uma entrada e gravando em uma saída com controle preciso de tamanho. É utilizado para:

- Criação de arquivos de tamanho exato
- Teste de desempenho de I/O (leitura/escrita de disco)
- Criação de arquivos de swap
- Geração de imagens de disco
- Backup de baixo nível (setor a setor)

### Parâmetros Essenciais

| Parâmetro | Descrição |
|-----------|-------------------------------------------|
| `if=` | Input File (origem dos dados) |
| `of=` | Output File (destino dos dados) |
| `bs=` | Block Size (tamanho de cada bloco) |
| `count=` | Número de blocos a copiar |
| `status=progress` | Exibe progresso em tempo real |
| `conv=fsync` | Garante flush para disco antes de finalizar |

### Fontes Especiais

| Dispositivo | Conteúdo |
|-------------|----------|
| `/dev/zero` | Gera bytes zero (0x00) |
| `/dev/urandom` | Gera bytes pseudoaleatórios |
| `/dev/null` | "Buraco negro" — descarta tudo que recebe |

> ⚠️ **CUIDADO EXTREMO:** O parâmetro `of=` pode sobrescrever qualquer dispositivo, incluindo discos inteiros (`/dev/sda`). **Nunca** aponte `of=` para um dispositivo de bloco sem absoluta certeza do que está fazendo.

### Atividade 2.4 — Geração de Arquivos com Tamanho Controlado

**Objetivo:** Criar arquivos com tamanho exato usando `dd` para testes de cota, transferência e armazenamento.

**Passos:**

1. Gere um arquivo de exatamente 50 MB preenchido com zeros:

```bash
dd if=/dev/zero of=lab_linux/data/arquivo_50mb.img bs=1M count=50 status=progress
```

2. Valide o tamanho:

```bash
ls -lh lab_linux/data/arquivo_50mb.img
stat lab_linux/data/arquivo_50mb.img
```

3. Gere um arquivo de 1 MB com blocos menores (observe a diferença de velocidade):

```bash
dd if=/dev/zero of=lab_linux/data/arquivo_1mb_bs1k.img bs=1K count=1024 status=progress
```

4. Compare os dois métodos para 1 MB:

```bash
dd if=/dev/zero of=lab_linux/data/arquivo_1mb_bs1m.img bs=1M count=1 status=progress
ls -lh lab_linux/data/arquivo_1mb_bs*.img
```

**Pergunta reflexiva:** Se `bs=1M count=50` gera 50 MB, quanto gera `bs=512 count=2048`?

---

### Atividade 2.5 — Dados Aleatórios e Teste de I/O

**Objetivo:** Gerar arquivos com dados aleatórios para simular payloads e realizar teste básico de velocidade de escrita.

**Passos:**

1. Gere 10 MB de dados aleatórios:

```bash
dd if=/dev/urandom of=lab_linux/data/dados_random.bin bs=1M count=10 status=progress
```

2. Verifique que o conteúdo é realmente aleatório (exiba os primeiros bytes em hexadecimal):

```bash
hexdump -C lab_linux/data/dados_random.bin | head -n 20
```

3. Compare visualmente com o arquivo de zeros:

```bash
hexdump -C lab_linux/data/arquivo_50mb.img | head -n 20
```

4. Teste de velocidade de escrita no diretório atual:

```bash
dd if=/dev/zero of=lab_linux/data/speed_test.tmp bs=1M count=100 conv=fsync status=progress
```

5. Anote a velocidade reportada (ex: `104857600 bytes copied, 0.5 s, 200 MB/s`) e remova o arquivo de teste:

```bash
rm lab_linux/data/speed_test.tmp
```

**Cenário real:** DevOps usam `dd` com `/dev/urandom` para testar compressão — dados aleatórios não comprimem. Dados de `/dev/zero` comprimem quase 100%.

---

### Atividade 2.6 — Cópia Parcial e Offset com `dd`

**Objetivo:** Extrair trechos de um arquivo usando `skip` e `count` para manipulação cirúrgica de dados.

**Passos:**

1. Crie um arquivo de referência de 10 MB:

```bash
dd if=/dev/urandom of=lab_linux/data/original_10mb.bin bs=1M count=10 status=progress
```

2. Extraia apenas os primeiros 2 MB:

```bash
dd if=lab_linux/data/original_10mb.bin of=lab_linux/data/primeiros_2mb.bin bs=1M count=2
ls -lh lab_linux/data/primeiros_2mb.bin
```

3. Extraia 2 MB a partir do offset de 5 MB (pula os primeiros 5 blocos de 1M):

```bash
dd if=lab_linux/data/original_10mb.bin of=lab_linux/data/trecho_5a7mb.bin bs=1M skip=5 count=2
ls -lh lab_linux/data/trecho_5a7mb.bin
```

4. Valide que os tamanhos estão corretos:

```bash
ls -lh lab_linux/data/original_10mb.bin lab_linux/data/primeiros_2mb.bin lab_linux/data/trecho_5a7mb.bin
```

5. Verifique que os conteúdos são diferentes:

```bash
md5sum lab_linux/data/primeiros_2mb.bin lab_linux/data/trecho_5a7mb.bin
```

**Pergunta reflexiva:** Como você usaria `dd` com `skip` para recuperar dados de um disco com setores corrompidos no início?

---

> **💡 Dica MobaXterm:** Após criar os arquivos grandes com `dd`, vá ao painel SFTP e observe os tamanhos reportados. Use a visualização de propriedades para confirmar os bytes exatos.

---

[⬅ Anterior: Módulo 1 — Navegação e Manipulação](modulo_01_navegacao_inspecao.md)

[➡ Próximo: Módulo 3 — Permissões, Posse e Execução](modulo_03_permissoes.md)

[⬅ Voltar ao Índice](curso_linux.md)
