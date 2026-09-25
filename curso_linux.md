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
sudo apt install openssh-server tree hexdump -y

# Iniciar o serviço SSH
sudo service ssh start
```

---

## 📊 Progresso do Aluno

| Módulo | Status |
|--------|--------|
| Módulo 1 — Navegação e Manipulação | ⬜ Pendente |
| Módulo 2 — Busca e Manipulação | ⬜ Pendente |
| Módulo 3 — Permissões | ⬜ Pendente |
| Módulo 4 — Automação | ⬜ Pendente |
| Módulo 5 — SSH e WSL | ⬜ Pendente |

---

> 💡 **Dica:** Comece pelo Módulo 1 e siga a ordem. Cada módulo constrói sobre os artefatos criados no anterior. A estrutura `lab_linux/` é utilizada ao longo de todo o curso.
