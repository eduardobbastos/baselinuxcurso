# Módulo 6 — Gerenciamento de Usuários e Grupos

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Compreender a estrutura de contas de usuário no Linux (`/etc/passwd`, `/etc/shadow`, `/etc/group`).
- Criar, modificar e remover usuários com `useradd`, `usermod` e `userdel`.
- Gerenciar senhas com `passwd` e políticas de expiração.
- Criar e gerenciar grupos com `groupadd` e `gpasswd`.
- Auditar informações de usuários com `id`, `whoami`, `finger` e `last`.

---

## 9. Criação e Gestão de Usuários

### Conceito-Chave

No Linux, cada usuário é identificado por um **UID** (User ID) numérico. As informações de contas ficam distribuídas em três arquivos fundamentais:

| Arquivo | Conteúdo | Permissão |
|---------|----------|-----------|
| `/etc/passwd` | Nome, UID, GID, home, shell | Leitura para todos |
| `/etc/shadow` | Hash da senha, expiração | Apenas root |
| `/etc/group` | Grupos e seus membros | Leitura para todos |

### Anatomia de `/etc/passwd`

```
aluno:x:1001:1001:Aluno do Curso:/home/aluno:/bin/bash
  │   │  │    │        │              │          └─ Shell padrão
  │   │  │    │        │              └─ Diretório home
  │   │  │    │        └─ Comentário (nome completo)
  │   │  │    └─ GID (Group ID principal)
  │   │  └─ UID (User ID)
  │   └─ Placeholder de senha (hash real em /etc/shadow)
  └─ Nome de login
```

### Comandos Essenciais

| Comando | Descrição |
|---------|-----------|
| `useradd` | Cria usuário (baixo nível, sem interação) |
| `adduser` | Cria usuário (interativo, mais amigável — Debian/Ubuntu) |
| `usermod` | Modifica propriedades de um usuário existente |
| `userdel` | Remove um usuário |
| `passwd` | Define/altera senha de um usuário |
| `groupadd` | Cria um novo grupo |
| `gpasswd` | Administra membros de um grupo |
| `id` | Mostra UID, GID e grupos de um usuário |
| `whoami` | Exibe o nome do usuário atual |
| `su` | Troca de usuário (Switch User) |

---

### Atividade 6.1 — Criação de Usuários com `useradd` e `adduser`

**Objetivo:** Criar contas de usuário utilizando os dois métodos disponíveis e entender as diferenças entre eles.

**Passos:**

1. Verifique o usuário atual e seus detalhes:

```bash
whoami
id
cat /etc/passwd | tail -5
```

2. Crie um usuário com `useradd` (método de baixo nível):

```bash
sudo useradd -m -s /bin/bash -c "Usuário de Teste" usuario_teste1
```

Flags utilizadas:
- `-m` → Cria o diretório home automaticamente
- `-s /bin/bash` → Define o shell padrão
- `-c "Usuário de Teste"` → Comentário/nome completo

3. Defina uma senha para o novo usuário:

```bash
sudo passwd usuario_teste1
```

4. Verifique que o usuário foi criado:

```bash
id usuario_teste1
grep usuario_teste1 /etc/passwd
ls -la /home/usuario_teste1/
```

5. Agora crie um usuário com `adduser` (método interativo — Debian/Ubuntu):

```bash
sudo adduser usuario_teste2
```

O sistema pedirá senha, nome completo e outras informações interativamente.

6. Compare os dois usuários criados:

```bash
grep "usuario_teste" /etc/passwd
ls -la /home/ | grep usuario_teste
```

7. Teste trocar para o novo usuário:

```bash
su - usuario_teste1
whoami
pwd
exit
```

**Pergunta reflexiva:** Qual a diferença prática entre `useradd` e `adduser`? Em qual cenário cada um é mais adequado?

**No MobaXterm:** Navegue pelo SFTP até `/home/` e observe os diretórios dos novos usuários criados.

---

### Atividade 6.2 — Modificação de Usuários e Gestão de Grupos

**Objetivo:** Modificar propriedades de usuários existentes e gerenciar a associação a grupos.

**Passos:**

1. Crie dois grupos para o laboratório:

```bash
sudo groupadd desenvolvedores
sudo groupadd operacoes
```

2. Verifique os grupos criados:

```bash
grep -E "desenvolvedores|operacoes" /etc/group
```

3. Adicione `usuario_teste1` ao grupo `desenvolvedores`:

```bash
sudo usermod -aG desenvolvedores usuario_teste1
```

> ⚠️ **IMPORTANTE:** Sempre use `-aG` (append + Group). Sem o `-a`, o comando **substitui** todos os grupos secundários!

4. Adicione `usuario_teste2` a ambos os grupos:

```bash
sudo usermod -aG desenvolvedores,operacoes usuario_teste2
```

5. Verifique a associação:

```bash
id usuario_teste1
id usuario_teste2
groups usuario_teste1
groups usuario_teste2
```

6. Altere o shell padrão de um usuário:

```bash
sudo usermod -s /bin/sh usuario_teste1
grep usuario_teste1 /etc/passwd
```

7. Altere o comentário (nome completo):

```bash
sudo usermod -c "Desenvolvedor Junior" usuario_teste1
grep usuario_teste1 /etc/passwd
```

8. Bloqueie temporariamente um usuário (sem deletar):

```bash
sudo usermod -L usuario_teste1
sudo passwd -S usuario_teste1
```

9. Desbloqueie:

```bash
sudo usermod -U usuario_teste1
sudo passwd -S usuario_teste1
```

10. Liste todos os membros de um grupo:

```bash
getent group desenvolvedores
getent group operacoes
```

**Cenário real:** Em um servidor de produção, um funcionário muda de equipe. Você precisa removê-lo do grupo `dev` e adicioná-lo ao grupo `ops` — como faria isso sem afetar outros grupos?

---

### Atividade 6.3 — Remoção, Auditoria e Políticas de Senha

**Objetivo:** Remover usuários com segurança, auditar sessões e configurar políticas de expiração de senha.

**Passos:**

1. Configure política de expiração de senha:

```bash
# Senha expira em 90 dias, aviso 7 dias antes
sudo chage -M 90 -W 7 usuario_teste2
```

2. Verifique as políticas aplicadas:

```bash
sudo chage -l usuario_teste2
```

3. Force o usuário a trocar a senha no próximo login:

```bash
sudo chage -d 0 usuario_teste2
```

4. Audite os últimos logins no sistema:

```bash
last | head -20
```

5. Verifique quem está logado agora:

```bash
who
w
```

6. Remova o `usuario_teste1` preservando o diretório home (segurança):

```bash
sudo userdel usuario_teste1
ls -la /home/usuario_teste1/
```

O diretório home ainda existe como backup.

7. Remova o `usuario_teste2` junto com seu diretório home:

```bash
sudo userdel -r usuario_teste2
ls -la /home/ | grep usuario_teste
```

8. Limpe os grupos criados:

```bash
sudo groupdel desenvolvedores
sudo groupdel operacoes
```

9. Confirme a limpeza:

```bash
grep -E "usuario_teste|desenvolvedores|operacoes" /etc/passwd /etc/group
```

10. Verifique se restaram arquivos órfãos (sem dono válido):

```bash
find /home/ -nouser 2>/dev/null
find /tmp/ -nouser 2>/dev/null
```

**Cenário real:** Um funcionário saiu da empresa. Checklist: bloquear conta → backup do home → remoção da conta → verificar arquivos órfãos → revogar chaves SSH.

**Pergunta reflexiva:** Por que `userdel` sem `-r` é mais seguro em ambientes de produção? O que acontece com os arquivos de um usuário deletado?

---

> **💡 Dica MobaXterm:** Use o painel SFTP para navegar até `/home/` e observar os diretórios home sendo criados e removidos em tempo real. Clique direito → Properties para verificar o proprietário (UID/GID) de cada pasta.

---

[⬅ Anterior: Módulo 5 — SSH e WSL](modulo_05_ssh_wsl.md)

[➡ Próximo: Módulo 7 — Manipulação de Dados com dd](modulo_07_dd.md)

[⬅ Voltar ao Índice](curso_linux.md)
