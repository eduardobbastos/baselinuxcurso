# Módulo 7 — Manipulação Avançada de Dados com `dd`

[⬅ Voltar ao Índice](curso_linux.md)

---

## Objetivos do Módulo

- Dominar o `dd` como ferramenta de cópia e conversão de blocos de dados.
- Criar arquivos de tamanho controlado para testes e simulações.
- Realizar backups de baixo nível e testes de desempenho de I/O.
- Compreender os parâmetros `if`, `of`, `bs`, `count`, `skip`, `seek` e `conv`.
- Reconhecer os riscos e adotar práticas seguras ao utilizar `dd`.

---

## 10. O Comando `dd` — Dataset Definition

### Conceito-Chave

O `dd` opera no nível mais baixo de abstração de dados no userspace Linux. Ele lê blocos de uma origem (`if`) e grava em um destino (`of`), com controle preciso sobre tamanho, offset e conversão.

```
┌─────────────┐     ┌─────────┐     ┌──────────────┐
│  Input File │────▸│   dd    │────▸│  Output File │
│  (if=)      │     │ bs/count│     │  (of=)       │
│             │     │ skip/   │     │              │
│ /dev/zero   │     │ seek/   │     │ arquivo.img  │
│ /dev/urandom│     │ conv    │     │ /dev/sdX     │
│ arquivo.bin │     └─────────┘     │ stdout       │
└─────────────┘                     └──────────────┘
```

### Parâmetros Completos

| Parâmetro | Descrição | Exemplo |
|-----------|-----------|--------|
| `if=` | Arquivo/dispositivo de entrada | `if=/dev/zero` |
| `of=` | Arquivo/dispositivo de saída | `of=teste.img` |
| `bs=` | Tamanho de cada bloco (leitura e escrita) | `bs=1M` |
| `ibs=` | Tamanho do bloco de leitura | `ibs=512` |
| `obs=` | Tamanho do bloco de escrita | `obs=4K` |
| `count=` | Número de blocos a copiar | `count=100` |
| `skip=` | Pula N blocos na ENTRADA | `skip=10` |
| `seek=` | Pula N blocos na SAÍDA | `seek=5` |
| `conv=` | Conversões durante a cópia | `conv=fsync,ucase` |
| `status=` | Nível de informação exibida | `status=progress` |

### Fontes e Destinos Especiais

| Dispositivo | Descrição | Uso Típico |
|-------------|-----------|------------|
| `/dev/zero` | Gera bytes zero (0x00) | Arquivos de preenchimento, limpar dados |
| `/dev/urandom` | Gera bytes pseudoaleatórios | Simulação de payloads, teste de compressão |
| `/dev/null` | Descarta tudo que recebe | Teste de velocidade de leitura |
| `/dev/sdX` | Dispositivo de bloco (disco) | Backup/restauração de discos |

> ⚠️ **REGRA DE OURO:** Antes de executar qualquer `dd` com `of=`, **leia o comando três vezes**. Um `of=/dev/sda` errado pode destruir todo o disco em segundos, sem confirmação.

---

### Atividade 7.1 — Criação de Arquivos com Tamanho Exato e Comparação de Block Size

**Objetivo:** Gerar arquivos com tamanhos precisos e entender como o `bs` afeta a performance.

**Passos:**

1. Crie a pasta de trabalho:

```bash
mkdir -p lab_linux/dd_lab
```

2. Gere um arquivo de exatamente 50 MB com zeros:

```bash
dd if=/dev/zero of=lab_linux/dd_lab/zeros_50mb.img bs=1M count=50 status=progress
```

3. Gere o mesmo tamanho com block size menor (observe a diferença de velocidade):

```bash
dd if=/dev/zero of=lab_linux/dd_lab/zeros_50mb_bs1k.img bs=1K count=51200 status=progress
```

4. Compare os tempos reportados e os tamanhos resultantes:

```bash
ls -lh lab_linux/dd_lab/zeros_50mb*.img
```

5. Gere um arquivo de tamanho fracionado (512 bytes × 100 blocos = 50 KB):

```bash
dd if=/dev/zero of=lab_linux/dd_lab/pequeno_50kb.img bs=512 count=100
ls -lh lab_linux/dd_lab/pequeno_50kb.img
```

6. Verifique que o conteúdo é realmente zeros:

```bash
hexdump -C lab_linux/dd_lab/zeros_50mb.img | head -5
hexdump -C lab_linux/dd_lab/zeros_50mb.img | tail -5
```

**Cálculo fundamental:** `Tamanho final = bs × count`
- `bs=1M count=50` → 1 MB × 50 = 50 MB
- `bs=1K count=51200` → 1 KB × 51200 = 50 MB
- `bs=512 count=100` → 512 B × 100 = 50 KB

**Pergunta reflexiva:** Por que `bs=1M` é muito mais rápido que `bs=1K` para gerar o mesmo arquivo? (Dica: pense em system calls.)

---

### Atividade 7.2 — Dados Aleatórios, Teste de I/O e `/dev/null`

**Objetivo:** Gerar dados aleatórios, medir velocidade de disco e entender o uso de `/dev/null`.

**Passos:**

1. Gere 10 MB de dados aleatórios:

```bash
dd if=/dev/urandom of=lab_linux/dd_lab/random_10mb.bin bs=1M count=10 status=progress
```

2. Compare o conteúdo aleatório com o de zeros usando `hexdump`:

```bash
echo "=== Dados aleatórios ==="
hexdump -C lab_linux/dd_lab/random_10mb.bin | head -10

echo "=== Dados zerados ==="
hexdump -C lab_linux/dd_lab/zeros_50mb.img | head -10
```

3. **Teste de velocidade de ESCRITA:**

```bash
dd if=/dev/zero of=lab_linux/dd_lab/speed_write.tmp bs=1M count=200 conv=fsync status=progress
```

Anote a velocidade (ex: `209715200 bytes copied, 0.8 s, 250 MB/s`).

4. **Teste de velocidade de LEITURA** (usando `/dev/null` como destino):

```bash
dd if=lab_linux/dd_lab/speed_write.tmp of=/dev/null bs=1M status=progress
```

5. Limpe os arquivos de teste de velocidade:

```bash
rm lab_linux/dd_lab/speed_write.tmp
```

6. Demonstre que dados aleatórios NÃO comprimem:

```bash
# Comprima o arquivo de zeros
gzip -k lab_linux/dd_lab/zeros_50mb.img
ls -lh lab_linux/dd_lab/zeros_50mb.img*

# Comprima o arquivo aleatório
gzip -k lab_linux/dd_lab/random_10mb.bin
ls -lh lab_linux/dd_lab/random_10mb.bin*
```

Observe: os zeros comprimem para quase nada; os dados aleatórios ficam do mesmo tamanho (ou maiores).

7. Limpe os arquivos comprimidos:

```bash
rm lab_linux/dd_lab/*.gz
```

**Cenário real:** Equipes de storage usam `dd` com `/dev/zero` e `/dev/urandom` para benchmarks de disco antes de colocar um storage em produção.

---

### Atividade 7.3 — Cópia Parcial com `skip`, `seek` e Extração Cirúrgica

**Objetivo:** Extrair trechos específicos de um arquivo e reconstruir dados usando offsets.

**Passos:**

1. Crie um arquivo de referência de 10 MB:

```bash
dd if=/dev/urandom of=lab_linux/dd_lab/original_10mb.bin bs=1M count=10 status=progress
```

2. Extraia apenas os primeiros 2 MB:

```bash
dd if=lab_linux/dd_lab/original_10mb.bin of=lab_linux/dd_lab/parte1_0a2mb.bin bs=1M count=2
ls -lh lab_linux/dd_lab/parte1_0a2mb.bin
```

3. Extraia 3 MB a partir do offset de 5 MB (pula os 5 primeiros blocos de 1M):

```bash
dd if=lab_linux/dd_lab/original_10mb.bin of=lab_linux/dd_lab/parte2_5a8mb.bin bs=1M skip=5 count=3
ls -lh lab_linux/dd_lab/parte2_5a8mb.bin
```

4. Extraia os últimos 2 MB:

```bash
dd if=lab_linux/dd_lab/original_10mb.bin of=lab_linux/dd_lab/parte3_8a10mb.bin bs=1M skip=8 count=2
ls -lh lab_linux/dd_lab/parte3_8a10mb.bin
```

5. Verifique que cada parte tem conteúdo diferente:

```bash
md5sum lab_linux/dd_lab/parte*.bin
```

6. Reconstrua o original a partir das partes (usando `seek` para posicionar na saída):

```bash
# Parte 1: bytes 0-2M
dd if=lab_linux/dd_lab/parte1_0a2mb.bin of=lab_linux/dd_lab/reconstruido.bin bs=1M seek=0

# Parte 2: bytes 5-8M
dd if=lab_linux/dd_lab/parte2_5a8mb.bin of=lab_linux/dd_lab/reconstruido.bin bs=1M seek=5 conv=notrunc

# Parte 3: bytes 8-10M
dd if=lab_linux/dd_lab/parte3_8a10mb.bin of=lab_linux/dd_lab/reconstruido.bin bs=1M seek=8 conv=notrunc
```

> ⚠️ **Nota:** `conv=notrunc` impede que o `dd` trunque o arquivo de saída ao escrever no meio dele.

7. Compare com o hash do original (as partes 0-2, 5-8 e 8-10 devem bater):

```bash
dd if=lab_linux/dd_lab/original_10mb.bin bs=1M skip=0 count=2 2>/dev/null | md5sum
dd if=lab_linux/dd_lab/reconstruido.bin bs=1M skip=0 count=2 2>/dev/null | md5sum
```

8. Demonstre conversão de maiúsculas com `conv=ucase`:

```bash
echo "texto em minusculas para teste" | dd conv=ucase 2>/dev/null
```

**Pergunta reflexiva:** Em um cenário de forense digital, como `dd` com `skip` seria usado para pular setores corrompidos e recuperar dados parcialmente?

---

> **💡 Dica MobaXterm:** Após criar arquivos com `dd`, vá ao painel SFTP, clique em Refresh e observe os tamanhos. Clique direito → Properties para verificar o tamanho em bytes exatos.

---

[⬅ Anterior: Módulo 6 — Gerenciamento de Usuários](modulo_06_usuarios.md)

[➡ Próximo: Módulo 8 — Visualização e Criação de Conteúdo com cat](modulo_08_cat.md)

[⬅ Voltar ao Índice](curso_linux.md)
