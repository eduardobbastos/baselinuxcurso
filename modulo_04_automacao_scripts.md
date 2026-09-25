# Módulo 4 — Automação e Scripts Shell

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender a estrutura de um script Bash: shebang, variáveis, código de saída.
- Utilizar condicionais (`if`), loops (`for`, `while`) e redirecionamento de saída.
- Construir scripts funcionais para automação de tarefas administrativas.
- Implementar logging em scripts para rastreabilidade.

---

## 7. Construção de Scripts Shell

### Conceito-Chave

#### Shebang

A primeira linha de todo script deve ser o **shebang** (`#!`), que indica ao sistema qual interpretador usar:

```bash
#!/bin/bash
```

#### Variáveis

```bash
NOME="valor"          # Atribuição (sem espaços ao redor do =)
echo "$NOME"           # Uso com expansão
echo "${NOME}_sufixo"  # Expansão com delimitador explícito
```

#### Código de Saída (Exit Status)

| Código | Significado |
|--------|-------------|
| `0` | Sucesso |
| `1-255` | Erro (convenções variam) |
| `$?` | Variável que contém o exit status do último comando |

#### Redirecionamento

| Operador | Descrição |
|----------|-----------|
| `>` | Redireciona saída (sobrescreve) |
| `>>` | Redireciona saída (append) |
| `2>` | Redireciona erro padrão |
| `2>&1` | Redireciona erro para o mesmo destino da saída |
| `\|` | Pipe: saída de um comando → entrada de outro |

---

### Atividade 4.1 — Script de Backup com Logging

**Objetivo:** Construir um script completo que localiza e arquiva determinados arquivos, registrando cada ação em um log.

**Passos:**

1. Crie o script `lab_linux/scripts/backup_rotina.sh` utilizando o editor do MobaXterm (duplo-clique pelo SFTP) ou `nano`:

```bash
nano lab_linux/scripts/backup_rotina.sh
```

2. Insira o conteúdo:

```bash
#!/bin/bash
# ============================================
# Script: backup_rotina.sh
# Descrição: Coleta arquivos .bkp e .img e
#            os copia para o diretório de backup
# ============================================

# Variáveis de controle
ORIGEM="$HOME/lab_linux/data"
DESTINO="$HOME/lab_linux/backup"
LOG_ARQUIVO="$HOME/lab_linux/logs/execucao.log"
DATA_EXEC=$(date +"%Y-%m-%d_%H-%M-%S")

# Garante diretório de destino
mkdir -p "$DESTINO"

echo "[$DATA_EXEC] Iniciando rotina de coleta..." >> "$LOG_ARQUIVO"

# Conta arquivos encontrados
TOTAL=$(find "$ORIGEM" -type f \( -name "*.bkp" -o -name "*.img" \) | wc -l)
echo "[$DATA_EXEC] Arquivos encontrados: $TOTAL" >> "$LOG_ARQUIVO"

# Localiza e arquiva
find "$ORIGEM" -type f \( -name "*.bkp" -o -name "*.img" \) -exec cp -v {} "$DESTINO/" \; >> "$LOG_ARQUIVO" 2>&1

if [ $? -eq 0 ]; then
    echo "[$DATA_EXEC] Coleta realizada com sucesso em $DESTINO." >> "$LOG_ARQUIVO"
    echo "[$DATA_EXEC] Total copiado: $TOTAL arquivo(s)." >> "$LOG_ARQUIVO"
else
    echo "[$DATA_EXEC] ERRO durante a cópia dos arquivos." >> "$LOG_ARQUIVO"
    exit 1
fi

echo "[$DATA_EXEC] Rotina finalizada." >> "$LOG_ARQUIVO"
echo "--- Backup concluído. Verifique o log em $LOG_ARQUIVO ---"
```

3. Ajuste permissões e execute:

```bash
chmod +x lab_linux/scripts/backup_rotina.sh
./lab_linux/scripts/backup_rotina.sh
```

4. Valide o log e os arquivos copiados:

```bash
cat lab_linux/logs/execucao.log
ls -la lab_linux/backup/
```

**Pergunta reflexiva:** O que acontece se `$ORIGEM` não existir? Como você adicionaria uma verificação no script?

---

### Atividade 4.2 — Script com Loop e Processamento de Argumentos

**Objetivo:** Criar um script que aceita argumentos de linha de comando e processa múltiplos arquivos em loop.

**Passos:**

1. Crie o script `lab_linux/scripts/analisa_diretorio.sh`:

```bash
nano lab_linux/scripts/analisa_diretorio.sh
```

2. Insira o conteúdo:

```bash
#!/bin/bash
# ============================================
# Script: analisa_diretorio.sh
# Uso: ./analisa_diretorio.sh <diretório>
# Descrição: Analisa um diretório e gera
#            relatório de conteúdo
# ============================================

# Verifica se recebeu argumento
if [ $# -eq 0 ]; then
    echo "Uso: $0 <diretório>"
    echo "Exemplo: $0 /var/log"
    exit 1
fi

DIRETORIO="$1"

# Verifica se o diretório existe
if [ ! -d "$DIRETORIO" ]; then
    echo "ERRO: '$DIRETORIO' não é um diretório válido."
    exit 1
fi

echo "========================================"
echo " Relatório de Análise de Diretório"
echo " Alvo: $DIRETORIO"
echo " Data: $(date)"
echo "========================================"
echo ""

# Contadores
TOTAL_ARQUIVOS=$(find "$DIRETORIO" -type f | wc -l)
TOTAL_DIRS=$(find "$DIRETORIO" -type d | wc -l)
TAMANHO_TOTAL=$(du -sh "$DIRETORIO" 2>/dev/null | cut -f1)

echo "📊 Resumo Geral:"
echo "   Arquivos: $TOTAL_ARQUIVOS"
echo "   Diretórios: $TOTAL_DIRS"
echo "   Tamanho total: $TAMANHO_TOTAL"
echo ""

# Extensões encontradas
echo "📋 Extensões encontradas:"
find "$DIRETORIO" -type f -name "*.*" | sed 's/.*\.//' | sort | uniq -c | sort -rn | head -10
echo ""

# Top 5 maiores arquivos
echo "📦 Top 5 maiores arquivos:"
find "$DIRETORIO" -type f -exec ls -lhS {} + 2>/dev/null | head -5
echo ""

# Arquivos modificados nas últimas 24h
RECENTES=$(find "$DIRETORIO" -type f -mmin -1440 | wc -l)
echo "🕐 Arquivos modificados nas últimas 24h: $RECENTES"

echo ""
echo "========================================"
echo " Relatório concluído."
echo "========================================"
```

3. Torne executável e teste:

```bash
chmod +x lab_linux/scripts/analisa_diretorio.sh
./lab_linux/scripts/analisa_diretorio.sh lab_linux/
```

4. Teste com argumentos inválidos:

```bash
# Sem argumento
./lab_linux/scripts/analisa_diretorio.sh

# Diretório inexistente
./lab_linux/scripts/analisa_diretorio.sh /caminho/inexistente
```

5. Salve a saída em um arquivo:

```bash
./lab_linux/scripts/analisa_diretorio.sh lab_linux/ > lab_linux/logs/relatorio_lab.txt
cat lab_linux/logs/relatorio_lab.txt
```

**Pergunta reflexiva:** Como você modificaria o script para aceitar múltiplos diretórios como argumento e processar cada um?

---

### Atividade 4.3 — Script de Monitoramento com Loop `while`

**Objetivo:** Criar um script que monitora continuamente um diretório e registra mudanças.

**Passos:**

1. Crie o script `lab_linux/scripts/monitor_dir.sh`:

```bash
nano lab_linux/scripts/monitor_dir.sh
```

2. Insira o conteúdo:

```bash
#!/bin/bash
# ============================================
# Script: monitor_dir.sh
# Uso: ./monitor_dir.sh <diretório> [intervalo]
# Descrição: Monitora um diretório e reporta
#            mudanças a cada N segundos
# ============================================

DIRETORIO="${1:-$HOME/lab_linux/data}"
INTERVALO="${2:-5}"
LOG="$HOME/lab_linux/logs/monitor.log"
CONTADOR=0
MAX_ITERACOES=10

if [ ! -d "$DIRETORIO" ]; then
    echo "ERRO: Diretório '$DIRETORIO' não encontrado."
    exit 1
fi

echo "Monitorando: $DIRETORIO"
echo "Intervalo: ${INTERVALO}s"
echo "Iterações: $MAX_ITERACOES"
echo "Log: $LOG"
echo "Pressione Ctrl+C para interromper."
echo "---"

# Snapshot inicial
ANTERIOR=$(find "$DIRETORIO" -type f | wc -l)

while [ $CONTADOR -lt $MAX_ITERACOES ]; do
    CONTADOR=$((CONTADOR + 1))
    AGORA=$(date +"%Y-%m-%d %H:%M:%S")
    ATUAL=$(find "$DIRETORIO" -type f | wc -l)
    TAMANHO=$(du -sh "$DIRETORIO" 2>/dev/null | cut -f1)
    
    if [ "$ATUAL" -ne "$ANTERIOR" ]; then
        DIFF=$((ATUAL - ANTERIOR))
        MENSAGEM="[$AGORA] MUDANÇA DETECTADA: $ANTERIOR → $ATUAL arquivos (diferença: $DIFF) | Tamanho: $TAMANHO"
        echo "$MENSAGEM"
        echo "$MENSAGEM" >> "$LOG"
        ANTERIOR=$ATUAL
    else
        echo "[$AGORA] Sem alterações | Arquivos: $ATUAL | Tamanho: $TAMANHO"
    fi
    
    sleep "$INTERVALO"
done

echo "--- Monitoramento encerrado após $MAX_ITERACOES iterações ---"
```

3. Torne executável:

```bash
chmod +x lab_linux/scripts/monitor_dir.sh
```

4. Execute em uma aba do MobaXterm:

```bash
./lab_linux/scripts/monitor_dir.sh lab_linux/data/ 3
```

5. **Em outra aba** do MobaXterm, crie e remova arquivos para ver o monitor reagir:

```bash
touch ~/lab_linux/data/novo_arquivo_teste.txt
touch ~/lab_linux/data/outro_arquivo.log
rm ~/lab_linux/data/novo_arquivo_teste.txt
```

6. Após encerrar, confira o log de mudanças:

```bash
cat lab_linux/logs/monitor.log
```

**Cenário real:** Scripts de monitoramento são a base de ferramentas como `inotifywait`. Esse padrão é usado em deploy pipelines para detectar quando novos artefatos chegam.

---

> **💡 Dica MobaXterm:** Use o **Multi-Execution Mode** para abrir duas abas sincronizadas. Na aba 1, rode o script de monitoramento. Na aba 2, faça alterações no diretório. Observe as duas abas reagindo em tempo real.

---

[⬅ Anterior: Módulo 3 — Permissões](modulo_03_permissoes.md)

[➡ Próximo: Módulo 5 — Conexão SSH e Acesso Remoto (WSL)](modulo_05_ssh_wsl.md)

[⬅ Voltar ao Índice](curso_linux.md)
