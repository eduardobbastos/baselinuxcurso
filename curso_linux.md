# 🐧 Curso Prático de Terminal Linux com MobaXterm

> **Capacitação prática em administração Linux via terminal, utilizando o MobaXterm como ambiente integrado de apoio.**

---

## 📋 Informações Gerais

| Item | Detalhe |
|------|--------|
| **Público-alvo** | Profissionais de TI com noções básicas de Linux |
| **Pré-requisito** | Conhecimento básico de linha de comando |
| **Ambiente** | MobaXterm (Windows) + WSL ou servidor Linux |
| **Foco pedagógico** | Comportamento dos utilitários, impactos no filesystem, flags essenciais e automação |
| **Metodologia** | 3 atividades práticas por tópico com validação visual via MobaXterm |

---

## 🎯 Por que o MobaXterm?

O MobaXterm é a ferramenta ideal para este treinamento porque permite:

- ✅ **Terminal + SFTP integrados** — Execute comandos e veja os resultados refletidos no navegador de arquivos à esquerda
- ✅ **Editor embutido** — Duplo-clique em qualquer arquivo no SFTP para editar com syntax highlighting
- ✅ **Gerenciamento de sessões SSH** — Salve conexões para reconexão com um clique
- ✅ **Multi-execution** — Rode comandos em múltiplas abas simultaneamente
- ✅ **Propriedades visuais** — Inspecione permissões, timestamps e donos via interface gráfica

---

## 📚 Estrutura do Curso

### [Módulo 0 — Fundamentos do Terminal e Anatomia do Linux](modulo_00_fundamentos_terminal.md)

Comandos, opções e argumentos, senhas silenciosas, atalhos de console, `sudo` e a árvore do FHS.

| # | Tópico | Atividades |
|---|--------|------------|
| 0 | Fundamentos da Shell & FHS | 0.1 Sobrevivência no Console: Navegação Espacial e Autocompletar |
| | | 0.2 A anatomia do `sudo`, senhas silenciosas e controle de processos |
| | | 0.3 Explorando o FHS: Inspeção Prática das Pastas Fundamentais |

---

### [Módulo 1 — Navegação, Inspeção e Manipulação de Arquivos](modulo_01_navegacao_inspecao.md)

Domínio dos comandos fundamentais para explorar, criar e organizar o filesystem.

| # | Tópico | Atividades |
|---|--------|------------|
| 1 | Inspeção com `ls` | 1.1 Reconhecimento de Metadados |
| | | 1.2 Ordenação por Tempo e Tamanho |
| | | 1.3 Listagem Recursiva e Exploração de Árvore |
| 2 | Criação com `mkdir` e `touch` | 1.4 Construção de Árvore de Projeto |
| | | 1.5 Manipulação de Timestamps |
| | | 1.6 Criação em Massa com Brace Expansion |
| 3 | Cópia e Movimentação com `cp` e `mv` | 1.7 Cópia Simples vs. Preservação de Atributos |
| | | 1.8 Movimentação, Renomeação e Flag `-u` |
| | | 1.9 Cópia Recursiva de Diretórios |

---

### [Módulo 2 — Busca Avançada e Manipulação de Baixo Nível](modulo_02_busca_manipulacao.md)

Localização precisa de arquivos e manipulação direta de blocos de dados.

| # | Tópico | Atividades |
|---|--------|------------|
| 4 | Localização com `find` | 2.1 Busca por Nome e Tipo |
| | | 2.2 Busca por Tamanho e Tempo |
| | | 2.3 Execução de Ações com `-exec` e `-delete` |
| 5 | Manipulação com `dd` | 2.4 Geração de Arquivos com Tamanho Controlado |
| | | 2.5 Dados Aleatórios e Teste de I/O |
| | | 2.6 Cópia Parcial e Offset |

---

### [Módulo 3 — Permissões, Posse e Execução](modulo_03_permissoes.md)

Modelo Unix de permissões, notação octal/simbólica e gestão de proprietários.

| # | Tópico | Atividades |
|---|--------|------------|
| 6 | `chmod` e `chown` | 3.1 Entendendo "Permission Denied" e `chmod` |
| | | 3.2 Notação Octal vs. Simbólica |
| | | 3.3 Alteração de Proprietário com `chown` |

---

### [Módulo 4 — Automação e Scripts Shell](modulo_04_automacao_scripts.md)

Construção de scripts Bash para automação de tarefas administrativas.

| # | Tópico | Atividades |
|---|--------|------------|
| 7 | Scripts Shell | 4.1 Script de Backup com Logging |
| | | 4.2 Script com Loop e Argumentos |
| | | 4.3 Script de Monitoramento com `while` |

---

### [Módulo 5 — Conexão SSH e Acesso Remoto via WSL](modulo_05_ssh_wsl.md)

Configuração de SSH no WSL e acesso remoto seguro via MobaXterm.

| # | Tópico | Atividades |
|---|--------|------------|
| 8 | SSH e Acesso Remoto | 5.1 Configuração do SSH no WSL e Conexão |
| | | 5.2 Transferência de Arquivos (SCP/SFTP) |
| | | 5.3 Autenticação por Chave Pública |

---

### [Módulo 6 — Gerenciamento de Usuários e Grupos](modulo_06_usuarios.md)

Criação, administração de privilégios, grupos e políticas de segurança de contas.

| # | Tópico | Atividades |
|---|--------|------------|
| 9 | Criação e Gestão de Usuários | 6.1 Criação de Usuários com `useradd` e `adduser` |
| | | 6.2 Modificação de Usuários e Gestão de Grupos |
| | | 6.3 Remoção, Auditoria e Políticas de Senha |

---

### [Módulo 7 — Manipulação Avançada de Dados com `dd`](modulo_07_dd.md)

Operações cirúrgicas de baixo nível, blocos, testes de I/O e extração com offset.

| # | Tópico | Atividades |
|---|--------|------------|
| 10 | Comando `dd` (Dataset Definition) | 7.1 Criação com Tamanho Exato e Comparação de Block Size |
| | | 7.2 Dados Aleatórios, Teste de I/O e `/dev/null` |
| | | 7.3 Cópia Parcial com `skip`, `seek` e Extração Cirúrgica |

---

### [Módulo 8 — Visualização e Criação de Conteúdo com `cat`](modulo_08_cat.md)

Inspeção rápida, concatenação, criação sem editores e pipelines de fluxo de texto.

| # | Tópico | Atividades |
|---|--------|------------|
| 11 | Comando `cat` e Concatenação | 8.1 Visualização, Numeração e Inspeção de Conteúdo |
| | | 8.2 Criação de Arquivos com `cat` e Here Document |
| | | 8.3 Concatenação, Pipelines e Processamento |

---

### [Módulo 9 — Editor de Texto `nano`](modulo_09_nano.md)

Edição rápida e acessível em terminal, atalhos produtivos, busca e customização.

| # | Tópico | Atividades |
|---|--------|------------|
| 12 | Editor `nano` no Terminal | 9.1 Criação e Edição Básica com `nano` |
| | | 9.2 Busca, Substituição e Manipulação de Blocos |
| | | 9.3 Edição de Arquivos do Sistema e Configuração (`.nanorc`) |

---

### [Módulo 10 — Editor `vi` / `vim`](modulo_10_vi.md)

O padrão universal Unix: modos de operação, eficiência cirúrgica e recuperação emergencial.

| # | Tópico | Atividades |
|---|--------|------------|
| 13 | Editor `vi`/`vim` Modal | 10.1 Primeiros Passos: Navegar, Inserir e Salvar |
| | | 10.2 Edição Avançada: Copiar, Colar, Deletar e Substituir |
| | | 10.3 Cenários Reais: Recuperação, Modo Visual e Comandos Shell |

---

### [Módulo 11 — Expansão Enterprise: Rocky Linux no WSL](modulo_11_rocky_linux_wsl.md)

Ecossistema RHEL/CentOS/Rocky, SSH empresarial e repetição cruzada dos Módulos 1 e 2.

| # | Tópico | Atividades |
|---|--------|------------|
| 14 | Rocky Linux & Portabilidade RHEL | 11.1 Instalação do Rocky Linux no WSL e Configuração do SSH |
| | | 11.2 Repetição Prática do Módulo 1 no Rocky Linux (`ls`, `mkdir`, `cp -a`, `touch`) |
| | | 11.3 Repetição Prática do Módulo 2 no Rocky Linux (`find -exec`, `dd`, I/O) |

---

## 🗺️ Mapeamento MobaXterm × Atividades

| Ação no Terminal | Recurso do MobaXterm para Inspeção Visual |
|-----------------|-------------------------------------------|
| `ls -la`, `mkdir -p` | **Painel SFTP Lateral:** Acompanhar criação de pastas e arquivos ocultos em tempo real com auto-refresh |
| `touch`, `chmod`, `chown` | **Propriedades SFTP:** Botão direito → Properties para inspecionar permissões octais e gráficos |
| Edição de scripts | **MobaTextEditor:** Duplo-clique no arquivo pelo SFTP para edição rápida com syntax highlighting |
| Comandos em múltiplos nós | **Multi-Execution Mode:** Ativar para rodar comandos simultaneamente em múltiplas abas |
| Conexão SSH ao WSL | **Session → SSH:** Configurar host, porta e usuário para conexão persistente |
| Transferência de arquivos | **Drag & Drop SFTP** ou **SCP/SFTP** via linha de comando |
| Gerenciamento de chaves | **Tools → MobaKeyGen:** Interface gráfica para geração e gestão de chaves SSH |
| Usuários e Diretórios Home | **Navegação SFTP:** Inspecionar permissões de `/home/` e arquivos de configuração |
| Arquivos binários/dd | **Painel SFTP & Text Editor:** Conferir tamanhos exatos e tentar abrir streams |

---

## 📖 Material Complementar

- **[Gabarito Comentado e Respostas de Cenários (gabarito_exercicios.md)](gabarito_exercicios.md)** — Respostas analíticas comentadas, comandos recomendados e justificativas de segurança para todas as perguntas reflexivas dos 10 módulos.

---

## 🏁 Pré-requisitos de Ambiente

### No Windows
1. Instalar o [MobaXterm](https://mobaxterm.mobatek.net/) (Home Edition gratuita é suficiente)
2. Instalar o [WSL](https://learn.microsoft.com/pt-br/windows/wsl/install) com Ubuntu ou Debian

### No WSL
```bash
# Atualizar pacotes
sudo apt update && sudo apt upgrade -y

# Instalar ferramentas essenciais
sudo apt install openssh-server tree nano vim bsdmainutils -y

# Iniciar o serviço SSH
sudo service ssh start
```

---

> 💡 **Dica:** Comece pelo Módulo 1 e siga a ordem. Cada módulo constrói sobre os artefatos criados no anterior. A estrutura `lab_linux/` é utilizada ao longo de todo o curso.
