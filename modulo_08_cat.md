# Módulo 8 — Visualização e Criação de Conteúdo com `cat`

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Dominar o `cat` como ferramenta de visualização, concatenação e criação de arquivos.
- Utilizar `cat` com redirecionamento para criar arquivos sem editor.
- Conhecer comandos complementares: `tac`, `head`, `tail`, `less`, `more`.
- Aplicar here documents (`<<EOF`) para criação de conteúdo inline.
- Numerar linhas, concatenar múltiplos arquivos e criar pipelines.

---

## 11. O Comando `cat` — Concatenate and Print

### Conceito-Chave

O nome `cat` vem de *concatenate*. Seu papel original é concatenar (juntar) o conteúdo de múltiplos arquivos e enviá-lo para a saída padrão (stdout). Na prática, é a ferramenta mais usada para **visualizar rapidamente** o conteúdo de arquivos de texto.

### Flags Essenciais

| Flag | Descrição |
|------|-----------|
| `-n` | Numera todas as linhas |
| `-b` | Numera apenas linhas não-vazias |
| `-s` | Suprime linhas vazias consecutivas |
| `-E` | Mostra `$` no final de cada linha |
| `-T` | Exibe TABs como `^I` |
| `-A` | Equivale a `-vET` (mostra tudo: tabs, fins de linha, caracteres especiais) |

### Comandos Complementares

| Comando | Descrição |
|---------|-----------|
| `tac` | Exibe o arquivo de trás para frente (inverso de `cat`) |
| `head` | Mostra as primeiras N linhas (padrão: 10) |
| `tail` | Mostra as últimas N linhas (padrão: 10) |
| `tail -f` | Acompanha o arquivo em tempo real (follow) |
| `less` | Paginador interativo (navegar com setas, buscar com `/`) |
| `more` | Paginador simples (avança com espaço) |
| `wc` | Conta linhas, palavras e bytes |

---

### Atividade 8.1 — Visualização, Numeração e Inspeção de Conteúdo

**Objetivo:** Utilizar `cat` e seus complementos para visualizar e inspecionar arquivos de diferentes formas.

**Passos:**

1. Crie um arquivo de teste com conteúdo multilinha:

```bash
cat > lab_linux/data/poema.txt << 'EOF'
No meio do caminho tinha uma pedra
tinha uma pedra no meio do caminho

tinha uma pedra

no meio do caminho tinha uma pedra.



Nunca me esquecerei desse acontecimento
na vida de minhas retinas tão fatigadas.
EOF
```

2. Visualize o conteúdo:

```bash
cat lab_linux/data/poema.txt
```

3. Numere todas as linhas (incluindo vazias):

```bash
cat -n lab_linux/data/poema.txt
```

4. Numere apenas linhas com conteúdo:

```bash
cat -b lab_linux/data/poema.txt
```

5. Suprima linhas vazias consecutivas:

```bash
cat -s lab_linux/data/poema.txt
```

6. Mostre caracteres invisíveis (finais de linha e tabs):

```bash
cat -A lab_linux/data/poema.txt
```

7. Visualize de trás para frente com `tac`:

```bash
tac lab_linux/data/poema.txt
```

8. Compare `head` e `tail`:

```bash
head -3 lab_linux/data/poema.txt
tail -3 lab_linux/data/poema.txt
```

9. Conte linhas, palavras e caracteres:

```bash
wc lab_linux/data/poema.txt
wc -l lab_linux/data/poema.txt
wc -w lab_linux/data/poema.txt
```

**Pergunta reflexiva:** Em qual situação `cat -A` é essencial? (Dica: diagnóstico de arquivos com caracteres invisíveis, como `\r\n` do Windows vs `\n` do Linux.)

---

### Atividade 8.2 — Criação de Arquivos com `cat` e Here Document

**Objetivo:** Usar `cat` com redirecionamento e here documents para criar arquivos de configuração e scripts sem precisar de um editor.

**Passos:**

1. Crie um arquivo simples com redirecionamento `>` (sobrescreve):

```bash
cat > lab_linux/conf/servidor.conf << 'EOF'
# Configuração do Servidor
# Gerado automaticamente

[general]
hostname = lab-server-01
porta = 8080
ambiente = desenvolvimento

[database]
host = localhost
porta = 5432
nome = app_db

[logging]
nivel = INFO
arquivo = /var/log/app.log
rotacao = diaria
EOF
```

2. Verifique o conteúdo:

```bash
cat -n lab_linux/conf/servidor.conf
```

3. Adicione conteúdo ao final com `>>` (append):

```bash
cat >> lab_linux/conf/servidor.conf << 'EOF'

[seguranca]
ssl = true
certificado = /etc/ssl/cert.pem
timeout = 300
EOF
```

4. Confirme que o conteúdo foi adicionado (não sobrescrito):

```bash
cat -n lab_linux/conf/servidor.conf
```

5. Crie um script usando `cat` (método muito usado em automação):

```bash
cat > lab_linux/scripts/hello_cat.sh << 'SCRIPT'
#!/bin/bash
# Script criado via cat + here document
echo "Olá! Este script foi criado com cat."
echo "Data de execução: $(date)"
echo "Usuário: $(whoami)"
echo "Diretório atual: $(pwd)"
SCRIPT

chmod +x lab_linux/scripts/hello_cat.sh
./lab_linux/scripts/hello_cat.sh
```

6. Crie um arquivo CSV com dados de exemplo:

```bash
cat > lab_linux/data/funcionarios.csv << 'EOF'
id,nome,departamento,salario
1,Ana Silva,TI,8500
2,Bruno Costa,RH,6200
3,Carla Santos,Financeiro,7800
4,Diego Lima,TI,9100
5,Eva Rocha,Operações,5900
EOF

cat -n lab_linux/data/funcionarios.csv
```

7. Demonstre a diferença entre `>` (sobrescreve) e `>>` (append):

```bash
echo "Linha 1" > lab_linux/tmp/teste_redir.txt
echo "Linha 2" > lab_linux/tmp/teste_redir.txt
cat lab_linux/tmp/teste_redir.txt
# Resultado: apenas "Linha 2" (> sobrescreveu)

echo "Linha 1" > lab_linux/tmp/teste_append.txt
echo "Linha 2" >> lab_linux/tmp/teste_append.txt
cat lab_linux/tmp/teste_append.txt
# Resultado: ambas as linhas (>> adicionou)
```

**Cenário real:** Em scripts de deploy, `cat << EOF > arquivo` é a forma padrão de gerar arquivos de configuração dinamicamente, sem depender de um editor instalado no servidor.

---

### Atividade 8.3 — Concatenação, Pipelines e Processamento

**Objetivo:** Concatenar múltiplos arquivos, combinar `cat` com pipes e processar dados em fluxo.

**Passos:**

1. Crie três arquivos de log simulados:

```bash
cat > lab_linux/logs/app_seg.log << 'EOF'
2024-01-15 08:00:01 INFO Servidor iniciado
2024-01-15 08:15:32 WARN Memória acima de 80%
2024-01-15 09:00:00 INFO Backup automático concluído
EOF

cat > lab_linux/logs/app_ter.log << 'EOF'
2024-01-16 08:00:03 INFO Servidor iniciado
2024-01-16 10:22:15 ERROR Conexão com banco perdida
2024-01-16 10:22:45 INFO Reconexão estabelecida
2024-01-16 14:00:00 INFO Deploy versão 2.1.0
EOF

cat > lab_linux/logs/app_qua.log << 'EOF'
2024-01-17 08:00:02 INFO Servidor iniciado
2024-01-17 11:45:00 ERROR Timeout na API externa
2024-01-17 11:45:30 WARN Retry 1/3 para API externa
2024-01-17 11:46:00 INFO API externa respondeu
EOF
```

2. Concatene todos os logs em um único arquivo:

```bash
cat lab_linux/logs/app_seg.log lab_linux/logs/app_ter.log lab_linux/logs/app_qua.log > lab_linux/logs/app_semana.log
cat -n lab_linux/logs/app_semana.log
```

3. Use wildcard para concatenar:

```bash
cat lab_linux/logs/app_*.log > lab_linux/logs/app_consolidado.log
wc -l lab_linux/logs/app_consolidado.log
```

4. Filtre apenas linhas de ERRO com pipeline `cat | grep`:

```bash
cat lab_linux/logs/app_semana.log | grep "ERROR"
```

5. Conte ocorrências por nível de log:

```bash
cat lab_linux/logs/app_semana.log | grep -c "INFO"
cat lab_linux/logs/app_semana.log | grep -c "WARN"
cat lab_linux/logs/app_semana.log | grep -c "ERROR"
```

6. Extraia e ordene os horários de erro:

```bash
cat lab_linux/logs/app_semana.log | grep "ERROR" | cut -d' ' -f1,2
```

7. Acompanhe um log em tempo real com `tail -f` (abra outra aba do MobaXterm para gerar dados):

```bash
# Aba 1: Monitorar
tail -f lab_linux/logs/app_semana.log

# Aba 2: Gerar novas entradas
echo "$(date '+%Y-%m-%d %H:%M:%S') ERROR Novo erro simulado" >> ~/lab_linux/logs/app_semana.log
```

8. Processe o CSV criado anteriormente:

```bash
# Listar todos exceto o cabeçalho
cat lab_linux/data/funcionarios.csv | tail -n +2

# Filtrar apenas o departamento de TI
cat lab_linux/data/funcionarios.csv | grep "TI"

# Contar funcionários por departamento
cat lab_linux/data/funcionarios.csv | tail -n +2 | cut -d',' -f3 | sort | uniq -c
```

**Cenário real:** Administradores consolidam logs de múltiplos servidores com `cat` + pipes para análise centralizada antes de implementar ferramentas como ELK Stack ou Grafana Loki.

**Pergunta reflexiva:** `cat arquivo | grep padrão` vs `grep padrão arquivo` — qual é mais eficiente e por quê? (Pesquise sobre "Useless Use of Cat".)

---

> **💡 Dica MobaXterm:** Use o MobaTextEditor (duplo-clique no SFTP) para visualizar os arquivos criados com `cat`. Compare o conteúdo visual com a saída de `cat -A` no terminal para identificar caracteres invisíveis.

---

[⬅ Anterior: Módulo 7 — Manipulação com dd](modulo_07_dd.md)

[➡ Próximo: Módulo 9 — Editor nano](modulo_09_nano.md)

[⬅ Voltar ao Índice](curso_linux.md)
