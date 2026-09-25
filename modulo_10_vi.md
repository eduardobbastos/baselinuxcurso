# Módulo 10 — Editor `vi` / `vim`

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender a filosofia modal do `vi` e a diferença entre seus modos de operação.
- Navegar, editar, buscar e substituir texto de forma eficiente no `vi`.
- Salvar, sair e recuperar arquivos em cenários de emergência.
- Aplicar o `vi` em situações onde é o único editor disponível.

---

## 13. Editor `vi`/`vim` — O Editor Universal do Unix

### Conceito-Chave

O `vi` (Visual Editor) está presente em **todo** sistema Unix/Linux, inclusive em ambientes mínimos de recuperação onde o `nano` não está disponível. O `vim` (Vi IMproved) é a versão moderna com recursos adicionais.

A característica mais importante do `vi` é ser **modal**: o significado de cada tecla muda dependendo do modo em que você está.

### Os Três Modos Fundamentais

```
                    ┌──────────────────┐
          i/a/o     │                  │     Esc
        ┌──────────▸│  MODO INSERÇÃO   │──────────┐
        │           │  (Digitar texto) │          │
        │           └──────────────────┘          │
        │                                         │
┌───────┴──────────┐                      ┌───────▾──────────┐
│                  │         :            │                  │
│  MODO NORMAL     │─────────────────────▸│  MODO COMANDO    │
│  (Navegação e    │                      │  (:w :q :wq      │
│   comandos de    │◂─────────────────────│   :%s/a/b/)      │
│   edição)        │        Enter/Esc     │                  │
└──────────────────┘                      └──────────────────┘
```

| Modo | Tecla de Entrada | Função |
|------|-----------------|--------|
| **Normal** | `Esc` (a partir de qualquer modo) | Navegação, cópia, deleção, comandos |
| **Inserção** | `i`, `a`, `o`, `I`, `A`, `O` | Digitação de texto |
| **Comando** | `:` (a partir do Normal) | Salvar, sair, substituir, configurar |
| **Visual** | `v`, `V`, `Ctrl+V` | Seleção de texto |

> **Regra de Ouro:** Quando estiver perdido, **pressione `Esc` várias vezes**. Isso sempre retorna ao Modo Normal.

### Referência Rápida — Modo Normal

**Navegação:**

| Tecla | Ação |
|-------|------|
| `h` `j` `k` `l` | Esquerda, Baixo, Cima, Direita |
| `w` | Próxima palavra |
| `b` | Palavra anterior |
| `0` | Início da linha |
| `$` | Final da linha |
| `gg` | Início do arquivo |
| `G` | Final do arquivo |
| `5G` ou `:5` | Ir para a linha 5 |
| `Ctrl+F` | Página para baixo (Forward) |
| `Ctrl+B` | Página para cima (Back) |

**Edição:**

| Tecla | Ação |
|-------|------|
| `i` | Inserir antes do cursor |
| `a` | Inserir depois do cursor |
| `o` | Nova linha abaixo |
| `O` | Nova linha acima |
| `A` | Inserir no final da linha |
| `I` | Inserir no início da linha |
| `x` | Deletar caractere sob o cursor |
| `dd` | Deletar (recortar) linha inteira |
| `5dd` | Deletar 5 linhas |
| `yy` | Copiar (yank) linha inteira |
| `5yy` | Copiar 5 linhas |
| `p` | Colar abaixo |
| `P` | Colar acima |
| `u` | Desfazer (Undo) |
| `Ctrl+R` | Refazer (Redo) |
| `.` | Repetir último comando |

**Busca:**

| Tecla | Ação |
|-------|------|
| `/texto` | Buscar para frente |
| `?texto` | Buscar para trás |
| `n` | Próxima ocorrência |
| `N` | Ocorrência anterior |

### Referência Rápida — Modo Comando

| Comando | Ação |
|---------|------|
| `:w` | Salvar |
| `:q` | Sair |
| `:wq` ou `:x` | Salvar e sair |
| `:q!` | Sair sem salvar (forçar) |
| `:w nome.txt` | Salvar como |
| `:set number` | Mostrar números de linha |
| `:set nonumber` | Ocultar números de linha |
| `:%s/antigo/novo/g` | Substituir todas as ocorrências |
| `:%s/antigo/novo/gc` | Substituir com confirmação |
| `:!comando` | Executar comando do shell |

---

### Atividade 10.1 — Primeiros Passos: Navegar, Inserir e Salvar

**Objetivo:** Abrir o `vi`, entender a transição entre modos e realizar operações básicas de criação e salvamento de arquivo.

**Passos:**

1. Abra o `vi` para criar um novo arquivo:

```bash
vi lab_linux/scripts/info_sistema.sh
```

2. Você está no **Modo Normal**. Pressione `i` para entrar no **Modo Inserção** (observe `-- INSERT --` na parte inferior).

3. Digite o seguinte conteúdo:

```bash
#!/bin/bash
# Script criado com vi
# Exibe informações do sistema

echo "================================"
echo " Relatório do Sistema"
echo "================================"
echo ""
echo "Data/Hora: $(date)"
echo "Hostname:  $(hostname)"
echo "Kernel:    $(uname -r)"
echo "Uptime:    $(uptime -p)"
echo "Usuário:   $(whoami)"
echo "Shell:     $SHELL"
echo "Home:      $HOME"
echo ""
echo "Uso de disco:"
df -h / | tail -1
echo ""
echo "Memória:"
free -h | head -2
echo ""
echo "================================"
```

4. Pressione `Esc` para voltar ao **Modo Normal**.

5. Para salvar e sair, digite: `:wq` e pressione `Enter`.

6. Torne executável e teste:

```bash
chmod +x lab_linux/scripts/info_sistema.sh
./lab_linux/scripts/info_sistema.sh
```

7. Reabra o arquivo para editar:

```bash
vi lab_linux/scripts/info_sistema.sh
```

8. Pratique navegação no Modo Normal:
   - `gg` → Início do arquivo
   - `G` → Final do arquivo
   - `5G` → Linha 5
   - `0` → Início da linha
   - `$` → Final da linha
   - `w` → Próxima palavra
   - `b` → Palavra anterior

9. Adicione uma nova linha ao final:
   - Pressione `G` para ir ao final
   - Pressione `o` para abrir nova linha abaixo (entra em Inserção)
   - Digite: `echo "Relatório concluído com sucesso."`
   - Pressione `Esc`
   - Digite `:wq` para salvar e sair

10. Verifique a alteração:

```bash
tail -3 lab_linux/scripts/info_sistema.sh
```

**Pergunta reflexiva:** Quantas vezes você pressionou `Esc` por instinto? Essa é a tecla mais importante do `vi`!

---

### Atividade 10.2 — Edição Avançada: Copiar, Colar, Deletar e Substituir

**Objetivo:** Dominar operações de edição avançadas no `vi`: manipulação de linhas, blocos e substituição de texto.

**Passos:**

1. Crie um arquivo de dados para praticar:

```bash
cat > lab_linux/data/hosts_lab.txt << 'EOF'
# Lista de hosts do laboratório
# Atualizado em janeiro/2024

192.168.1.1     gateway         gateway.lab.local
192.168.1.10    web-server-01   web01.lab.local
192.168.1.11    web-server-02   web02.lab.local
192.168.1.20    db-server-01    db01.lab.local
192.168.1.21    db-server-02    db02.lab.local
192.168.1.30    cache-server    cache.lab.local
192.168.1.40    app-server-01   app01.lab.local
192.168.1.41    app-server-02   app02.lab.local
192.168.1.50    log-server      log.lab.local
192.168.1.60    monitor-server  monitor.lab.local
192.168.1.100   workstation-01  ws01.lab.local
EOF
```

2. Abra no `vi` com números de linha:

```bash
vi lab_linux/data/hosts_lab.txt
```

No Modo Comando, ative números: `:set number` + `Enter`

3. **Deletar linhas:**
   - Vá para a linha 3 (linha vazia): `3G`
   - Delete-a: `dd`

4. **Copiar e colar linhas:**
   - Vá para a linha do `web-server-01`: posicione com `5G` (ou onde estiver)
   - Copie a linha: `yy`
   - Vá para o final do arquivo: `G`
   - Cole abaixo: `p`

5. **Deletar múltiplas linhas:**
   - Vá para a linha do `workstation`: posicione com `/workstation` + `Enter`
   - Delete 1 linha: `dd`

6. **Substituir texto em todo o arquivo:**
   - Pressione `Esc` para garantir Modo Normal
   - Digite: `:%s/lab.local/empresa.com.br/g` + `Enter`
   - Verifique: todas as ocorrências de `lab.local` foram substituídas por `empresa.com.br`

7. **Substituir com confirmação:**
   - Digite: `:%s/server/srv/gc` + `Enter`
   - Para cada ocorrência, responda:
     - `y` = substituir
     - `n` = pular
     - `a` = substituir todas as restantes
     - `q` = parar

8. **Desfazer todas as alterações:**
   - Pressione `u` repetidamente para desfazer cada alteração
   - Ou `:e!` para recarregar o arquivo original do disco

9. Salve e saia: `:wq`

10. Verifique:

```bash
cat -n lab_linux/data/hosts_lab.txt
```

**Cenário real:** Um administrador precisa alterar o domínio de 200 hosts em um arquivo de configuração. `:%s/dominio_antigo/dominio_novo/g` faz isso em 1 segundo.

---

### Atividade 10.3 — Cenários Reais: Recuperação, Modo Visual e Comandos do Shell

**Objetivo:** Lidar com situações reais como recuperação de arquivos, seleção visual de blocos e execução de comandos do shell de dentro do `vi`.

**Passos:**

1. Abra um arquivo e simule uma emergência:

```bash
vi lab_linux/conf/rede.conf
```

2. Faça várias edições (mude valores, adicione linhas) **sem salvar**.

3. **Simulação: sair sem salvar** — quando você percebe que fez alterações erradas:
   - Pressione `Esc`
   - Digite `:q!` + `Enter` (sai descartando tudo)

4. Verifique que o arquivo está intacto:

```bash
cat lab_linux/conf/rede.conf
```

5. **Modo Visual — Seleção de blocos:**

```bash
vi lab_linux/data/hosts_lab.txt
```

   - Ative números: `:set number`
   - Vá para a linha 4: `4G`
   - Entre no Modo Visual: `V` (seleção de linhas)
   - Selecione até a linha 7 com as setas (ou `3j`)
   - Copie o bloco selecionado: `y`
   - Vá para o final: `G`
   - Cole: `p`
   - Desfaça: `u`

6. **Modo Visual de Bloco (colunas):**
   - Pressione `Ctrl+V` para seleção retangular (bloco)
   - Selecione uma coluna de IPs (use setas)
   - Pressione `d` para deletar o bloco selecionado
   - Desfaça: `u`

7. **Executar comandos do shell de dentro do `vi`:**
   - No Modo Normal, digite `:!ls -la` + `Enter` para listar arquivos
   - Pressione `Enter` para voltar ao `vi`
   - Digite `:!date` para ver a data
   - Digite `:r !hostname` para **inserir** a saída do comando no arquivo
   - Desfaça: `u`

8. **Abrir múltiplos arquivos:**

```bash
vi lab_linux/conf/servidor.conf lab_linux/conf/rede.conf
```

   - `:n` → Próximo arquivo
   - `:N` → Arquivo anterior
   - `:files` → Lista todos os arquivos abertos

9. **Comparação com split screen** (vim):

```bash
vim lab_linux/conf/servidor.conf
```

   - `:split lab_linux/conf/rede.conf` → Divide a tela horizontalmente
   - `Ctrl+W` + `↑` ou `↓` → Navega entre painéis
   - `:q` → Fecha o painel ativo

10. Saia de tudo: `:qa` (quit all)

**Cenário real:** Você está em um servidor de emergência via console serial. Não há `nano`, não há interface gráfica. O `vi` é sua única opção. Saber o básico — `i` para inserir, `Esc` para sair do modo, `:wq` para salvar — pode salvar o dia.

---

## Comparação: `nano` vs `vi`

| Aspecto | `nano` | `vi`/`vim` |
|---------|--------|------------|
| Curva de aprendizado | Baixa | Alta |
| Disponibilidade | Quase todos os Linux | **Todos** os Unix/Linux |
| Velocidade de edição (para experts) | Moderada | Muito alta |
| Atalhos | Exibidos na tela | Precisam ser memorizados |
| Busca/substituição | Básica | Regex poderosa |
| Plugins/extensões | Limitado | Vasto ecossistema (vim) |
| Recomendação | Edições rápidas, iniciantes | Administradores avançados, emergências |

---

> **💡 Dica MobaXterm:** Para edições do dia-a-dia, o MobaTextEditor (duplo-clique no SFTP) é o mais produtivo. Reserve o `nano` para edições rápidas no terminal e o `vi` para quando estiver em servidores sem alternativas.

---

[⬅ Anterior: Módulo 9 — Editor nano](modulo_09_nano.md)

[➡ Próximo: Módulo 11 — Expansão Enterprise: Rocky Linux no WSL](modulo_11_rocky_linux_wsl.md)

[⬅ Voltar ao Índice](curso_linux.md)
