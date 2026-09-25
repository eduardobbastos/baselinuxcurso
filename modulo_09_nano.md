# Módulo 9 — Editor de Texto `nano`

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Utilizar o `nano` como editor de terminal amigável para criação e edição de arquivos.
- Dominar os atalhos essenciais de navegação, busca, recortar/colar e salvar.
- Configurar o `nano` para melhorar a experiência (syntax highlighting, números de linha).
- Editar arquivos de configuração do sistema de forma segura.

---

## 12. Editor `nano` — O Editor Amigável do Terminal

### Conceito-Chave

O `nano` é um editor de texto de terminal projetado para ser **simples e intuitivo**. Diferente do `vi`, os atalhos são exibidos na parte inferior da tela, tornando-o ideal para iniciantes e para edições rápidas.

A notação dos atalhos:
- `^` = Tecla **Ctrl**
- `M-` = Tecla **Alt** (Meta)

### Atalhos Essenciais

| Atalho | Ação |
|--------|------|
| `Ctrl+O` | Salvar (Write Out) |
| `Ctrl+X` | Sair |
| `Ctrl+K` | Recortar linha inteira |
| `Ctrl+U` | Colar linha recortada |
| `Ctrl+W` | Buscar texto |
| `Ctrl+\` | Buscar e substituir |
| `Ctrl+G` | Ajuda |
| `Ctrl+C` | Mostrar posição do cursor (linha/coluna) |
| `Alt+A` | Iniciar seleção de bloco |
| `Ctrl+6` | Copiar seleção (sem recortar) |
| `Alt+U` | Desfazer (Undo) |
| `Alt+E` | Refazer (Redo) |
| `Ctrl+_` | Ir para linha/coluna específica |
| `Alt+N` | Mostrar/ocultar números de linha |

---

### Atividade 9.1 — Criação e Edição Básica com `nano`

**Objetivo:** Criar um arquivo de configuração do zero usando `nano`, praticando salvamento, navegação e edição básica.

**Passos:**

1. Abra o `nano` para criar um novo arquivo:

```bash
nano lab_linux/conf/rede.conf
```

2. Digite o seguinte conteúdo:

```
# Configuração de Rede - Laboratório Linux
# Última atualização: [data de hoje]

[interface_principal]
nome = eth0
ip = 192.168.1.100
mascara = 255.255.255.0
gateway = 192.168.1.1

[dns]
primario = 8.8.8.8
secundario = 8.8.4.4

[proxy]
habilitado = false
servidor = proxy.empresa.local
porta = 3128
```

3. Pratique durante a edição:
   - Pressione `Ctrl+C` para ver a posição do cursor
   - Use `Alt+N` para ativar números de linha
   - Navegue com `Ctrl+_` e digite um número de linha para ir diretamente

4. Salve com `Ctrl+O` → confirme o nome → `Enter`

5. Saia com `Ctrl+X`

6. Verifique o resultado:

```bash
cat -n lab_linux/conf/rede.conf
```

7. Reabra o arquivo com números de linha ativados automaticamente:

```bash
nano -l lab_linux/conf/rede.conf
```

8. Pratique a navegação:
   - `Ctrl+_` → Digite `10` → Vai para a linha 10
   - `Ctrl+Home` → Início do arquivo
   - `Ctrl+End` → Final do arquivo
   - Saia com `Ctrl+X`

**Dica:** Para abrir com syntax highlighting (se disponível):

```bash
nano -Y sh lab_linux/scripts/backup_rotina.sh
```

---

### Atividade 9.2 — Busca, Substituição e Manipulação de Blocos

**Objetivo:** Dominar busca/substituição e operações de bloco (copiar, recortar, colar) no `nano`.

**Passos:**

1. Crie um arquivo de dados para praticar:

```bash
cat > lab_linux/data/servidores.txt << 'EOF'
servidor-web-01    192.168.1.10    Apache     ativo
servidor-web-02    192.168.1.11    Nginx      ativo
servidor-db-01     192.168.1.20    MySQL      ativo
servidor-db-02     192.168.1.21    PostgreSQL inativo
servidor-cache-01  192.168.1.30    Redis      ativo
servidor-app-01    192.168.1.40    Tomcat     ativo
servidor-app-02    192.168.1.41    Node.js    inativo
servidor-log-01    192.168.1.50    ELK        ativo
servidor-bkp-01    192.168.1.60    Bacula     ativo
servidor-dns-01    192.168.1.70    BIND       ativo
EOF
```

2. Abra no `nano` com números de linha:

```bash
nano -l lab_linux/data/servidores.txt
```

3. **Buscar:** Pressione `Ctrl+W`, digite `inativo` e pressione `Enter`. Observe o cursor ir para a primeira ocorrência. Pressione `Alt+W` para ir para a próxima.

4. **Substituir:** Pressione `Ctrl+\`:
   - Buscar: `inativo`
   - Substituir por: `MANUTENÇÃO`
   - Responda `A` (All) para substituir todas as ocorrências

5. **Recortar e colar linhas:**
   - Posicione o cursor na linha do `servidor-cache-01`
   - Pressione `Ctrl+K` para recortar a linha
   - Navegue até o final do arquivo
   - Pressione `Ctrl+U` para colar

6. **Copiar bloco:**
   - Posicione na primeira linha de dados
   - Pressione `Alt+A` para iniciar seleção
   - Use setas para selecionar 3 linhas
   - Pressione `Alt+6` para copiar (sem recortar)
   - Vá até o final e `Ctrl+U` para colar

7. **Desfazer:** Pressione `Alt+U` várias vezes para desfazer as alterações. `Alt+E` para refazer.

8. Salve e saia: `Ctrl+O` → `Enter` → `Ctrl+X`

9. Verifique o resultado:

```bash
cat -n lab_linux/data/servidores.txt
```

**Pergunta reflexiva:** Em qual cenário o `nano` é preferível ao `vi`? E quando o `vi` é indispensável?

---

### Atividade 9.3 — Edição de Arquivos do Sistema e Configuração do `nano`

**Objetivo:** Editar arquivos de configuração do sistema com `nano` e personalizar o comportamento do editor.

**Passos:**

1. Personalize o `nano` criando o arquivo de configuração:

```bash
nano ~/.nanorc
```

2. Adicione estas configurações:

```
# Configuração personalizada do nano
set linenumbers        # Sempre mostrar números de linha
set autoindent         # Indentação automática
set tabsize 4          # TAB = 4 espaços
set tabstospaces       # Converte TABs em espaços
set mouse              # Habilita clique do mouse
set smooth             # Rolagem suave
set titlecolor bold,white,blue    # Cor da barra de título
set statuscolor bold,white,green  # Cor da barra de status
```

3. Salve e saia (`Ctrl+O`, `Ctrl+X`).

4. Teste abrindo um script existente — as configurações serão aplicadas:

```bash
nano lab_linux/scripts/backup_rotina.sh
```

Observe: números de linha ativos, indentação automática ao pressionar Enter após uma linha indentada.

5. **Edite um arquivo do sistema com `sudo`** (simulação segura):

```bash
# Crie uma cópia local do hosts para praticar
cp /etc/hosts lab_linux/conf/hosts_copia
nano lab_linux/conf/hosts_copia
```

6. Adicione uma entrada de teste no final:

```
# Entradas de laboratório
192.168.1.100   lab-server-01   lab-server-01.local
192.168.1.200   lab-server-02   lab-server-02.local
```

7. Salve e verifique:

```bash
cat lab_linux/conf/hosts_copia
```

8. **Abra um arquivo como somente leitura** (proteção contra edição acidental):

```bash
nano -v lab_linux/conf/servidor.conf
```

Tente editar — o nano não permitirá. Saia com `Ctrl+X`.

9. **Edite múltiplos arquivos** em sequência:

```bash
nano lab_linux/conf/servidor.conf lab_linux/conf/rede.conf lab_linux/conf/hosts_copia
```

Navegue entre os arquivos com `Alt+>` (próximo) e `Alt+<` (anterior).

**Cenário real:** Em servidores sem interface gráfica, `nano` é frequentemente o editor padrão. Conhecer o `.nanorc` permite configurar rapidamente qualquer servidor novo.

---

> **💡 Dica MobaXterm:** Compare a experiência: edite o mesmo arquivo pelo MobaTextEditor (duplo-clique no SFTP) e pelo `nano` no terminal. O MobaTextEditor oferece syntax highlighting mais rico, mas o `nano` está disponível em qualquer servidor Linux, mesmo sem acesso gráfico.

---

[⬅ Anterior: Módulo 8 — Visualização com cat](modulo_08_cat.md)

[➡ Próximo: Módulo 10 — Editor vi/vim](modulo_10_vi.md)

[⬅ Voltar ao Índice](curso_linux.md)
