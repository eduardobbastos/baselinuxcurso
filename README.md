# Capacitação Prática em Terminal Linux com MobaXterm

> **Do básico à automação: raciocínio operacional, diagnóstico de baixo nível e segurança no ambiente Linux.**

---

## 📌 Acesso Rápido ao Curso

👉 **[Acesse o Índice Completo do Treinamento Aqui: curso_linux.md](curso_linux.md)**

O curso conta com **12 módulos estruturados**, totalizando **45 atividades práticas com hands-on direto no terminal e validação visual no MobaXterm**:

- [Módulo 0 — Fundamentos do Terminal e Anatomia do Linux (Sintaxe, sudo, senhas e FHS)](modulo_00_fundamentos_terminal.md)
- [Módulo 1 — Navegação, Inspeção e Manipulação de Arquivos (`ls`, `mkdir`, `touch`, `cp`, `mv`)](modulo_01_navegacao_inspecao.md)
- [Módulo 2 — Busca Avançada e Manipulação de Baixo Nível (`find`, `locate`, `dd`)](modulo_02_busca_manipulacao.md)
- [Módulo 3 — Permissões, Posse e Execução (`chmod`, `chown`)](modulo_03_permissoes.md)
- [Módulo 4 — Automação e Scripts Shell (Shebang, condicionais, logs e pipes)](modulo_04_automacao_scripts.md)
- [Módulo 5 — Conexão SSH e Acesso Remoto via WSL (SCP, SFTP, Chaves)](modulo_05_ssh_wsl.md)
- [Módulo 6 — Gerenciamento de Usuários e Grupos (`useradd`, `usermod`, `passwd`)](modulo_06_usuarios.md)
- [Módulo 7 — Manipulação Avançada de Dados com `dd` (Block size, I/O, `skip`/`seek`)](modulo_07_dd.md)
- [Módulo 8 — Visualização e Criação de Conteúdo com `cat` (Here Documents, `tac`, pipelines)](modulo_08_cat.md)
- [Módulo 9 — Editor de Texto `nano` (Navegação, atalhos e configuração)](modulo_09_nano.md)
- [Módulo 10 — Editor `vi`/`vim` (Filosofia modal, substituição avançada e emergências)](modulo_10_vi.md)
- [Módulo 11 — Expansão Enterprise: Rocky Linux no WSL (Portabilidade RHEL e repetição prática)](modulo_11_rocky_linux_wsl.md)
- [📖 Gabarito Comentado e Cenários de Infraestrutura (gabarito_exercicios.md)](gabarito_exercicios.md)

---

## 👨‍🏫 Apresentação do Instrutor

Olá! Sou **Eduardo Bastos**, Administrador de Sistemas Linux Sênior com mais de duas décadas de atuação na gestão, segurança e sustentação de infraestruturas computacionais e ambientes críticos. Minha formação em Análise e Desenvolvimento de Sistemas e especialização em DevOps me permitiram liderar projetos estruturantes — desde operações complexas na administração pública até o suporte a soluções de ponta em órgãos reguladores federais, passando por automação com Python, orquestração de contêineres e integração com tecnologias emergentes de IA.

Ao longo dessa trajetória, percebi que dominar o terminal não é sobre decorar comandos isolados, mas sim sobre entender o raciocínio operacional do sistema operacional: como os arquivos interagem com o hardware, como as permissões protegem o ambiente e como transformar rotinas manuais em automações confiáveis. Com essa visão técnica e o compromisso contínuo com a formação e a transmissão didática do conhecimento, desenvolvi este treinamento focado no que o mercado e a rotina de produção realmente exigem.

---

## 🚀 Sobre o Curso: Terminal Linux & MobaXterm na Prática

### Objetivo:
Levar quem já possui noções básicas de informática e Linux ao próximo nível, transformando comandos soltos em fluência administrativa, capacidade analítica de diagnóstico e desenvolvimento de automações para o dia a dia.

### Metodologia e Abordagem:
Este treinamento é essencialmente prático (*hands-on*). Em vez de simulações abstratas, utilizamos o **MobaXterm** como interface central de trabalho. Essa ferramenta integra cliente SSH de alto desempenho, sessões múltiplas e um navegador gráfico de sistema de arquivos (SFTP embutido), permitindo uma ponte visual imediata: você executa comandos no terminal e valida em tempo real as mudanças na árvore de diretórios, nós de permissão e arquivos de configuração.

### O que o conteúdo cobre:
- **Operação Eficiente do Sistema de Arquivos:** Manipulação estruturada de diretórios, inspeção avançada de metadados (`ls`), cópias com preservação de atributos (`cp -a`) e criação determinística de diretórios (`mkdir -p`).
- **Busca e Diagnóstico com `find`:** Filtragem granular por tipo, tamanho e modificação, associando automações de rotina via `-exec`.
- **Manipulação de Baixo Nível com `dd`:** Geração controlada de dados, testes de desempenho e criação de volumes e arquivos de preenchimento.
- **Segurança e Posse:** Gestão minuciosa do modelo de permissões Unix (`chmod` octal e simbólico) e gerenciamento de proprietários e grupos (`chown`).
- **Automação e Scripts Shell:** Construção de rotinas completas com verificação de código de retorno (*exit status*), logs auditáveis e parametrização segura.
- **Conectividade Remota e SSH:** Configuração e administração de nós remotos e instâncias WSL via SSH, transferência via SCP/SFTP e autenticação segura com chaves públicas.
- **Gestão de Identidades e Contas:** Criação e manutenção de usuários, grupos secundários e políticas de expiração de senhas.
- **Edição em Modo Terminal:** Fluência em editores essenciais de console (`nano` e `vi`/`vim`), garantindo autonomia mesmo em ambientes mínimos de recuperação.

---

## 🛠️ Ambiente Recomendado

- **Sistema Operacional do Host:** Windows 10/11 ou Linux.
- **Terminal & SFTP:** [MobaXterm Home/Professional Edition](https://mobaxterm.mobatek.net/).
- **Subsistema / Servidor:** [WSL 2](https://learn.microsoft.com/pt-br/windows/wsl/install) com Ubuntu ou Debian.

---

*Bons estudos e excelente prática no terminal!*
