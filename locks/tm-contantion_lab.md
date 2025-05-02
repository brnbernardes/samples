# Exemplo prático de TM Contention

### 1. Criação do ambiente:

```sql
-- Tabela pai (com PK)
CREATE TABLE pai (
  id NUMBER PRIMARY KEY,
  nome VARCHAR2(100)
);

-- Tabela filha com constraint de foreign key, mas sem índice
CREATE TABLE filha (
  id NUMBER PRIMARY KEY,
  pai_id NUMBER,
  FOREIGN KEY (pai_id) REFERENCES pai(id)
);
```
>Note que a coluna `pai_id` na tabela filha não tem índice.

### 2. Inserção de dados:

```sql
-- Inserindo dados na tabela pai
INSERT INTO pai VALUES (1, 'João');
COMMIT;

-- Inserindo dados na tabela filha
INSERT INTO filha VALUES (1, 1);
COMMIT;
```

### 3. Simulando a contenção (em duas sessões separadas):

* **Sessão 1:** realiza um DELETE na tabela pai e não faz commit.

```sql
DELETE FROM pai WHERE id = 1;
-- (não dá commit ainda!)
```

* **Sessão 2:** tenta inserir um novo registro na tabela filha com o mesmo pai\_id:

```sql
INSERT INTO filha VALUES (2, 1);
```

> Essa segunda sessão irá **ficar esperando** (TM enqueue – contention), porque o Oracle precisa garantir integridade referencial, e como não há índice na coluna filha.fk, ele precisa escanear toda a tabela filha para verificar possíveis filhos antes de permitir o DELETE da sessão 1.

---

### Diagnóstico:

Na view V\$SESSION da Sessão 2, você verá algo como:

```sql
SELECT sid, event, p1, wait_class
FROM v$session
WHERE state = 'WAITING';
```

Resultado:

| SID | EVENT                | P1        | WAIT\_CLASS |
| --- | -------------------- | --------- | ----------- |
| 123 | enq: TM - contention | 141433241 | Concurrency |

---

### 4. Solução:

Crie um índice na coluna da foreign key:

```sql
CREATE INDEX idx_filha_pai_id ON filha(pai_id);
```

Agora, o Oracle pode verificar a existência de filhos de forma eficiente, sem precisar escanear a tabela, evitando o bloqueio TM.

