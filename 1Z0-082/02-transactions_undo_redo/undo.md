# UNDO
---

## 📦 1. **Rollback de transações do usuário**
- Ao executar um `ROLLBACK`, o Oracle usa os dados salvos nos undo segments para **reverter** as alterações da transação.

---

## 🔎 2. **Consistência de leitura (Read Consistency)**
Esse é um dos usos **mais importantes**:

- Quando você faz uma **consulta (SELECT)**, o Oracle garante que você veja os dados **como estavam no início da consulta**, mesmo que outra sessão esteja modificando-os.
- Para isso, ele usa os **undo segments para reconstruir a versão antiga** do dado que estava sendo alterado.
  
📌 **Exemplo prático:**
```sql
-- Sessão 1
UPDATE clientes SET nome = 'João' WHERE id = 10;

-- Sessão 2
SELECT * FROM clientes WHERE id = 10;
```
A sessão 2 verá o valor **antes da alteração** feita pela sessão 1, pois o Oracle reconstrói esse estado usando os dados da undo.

---

## 💥 3. **Recuperação do banco (Crash Recovery)**
Após um crash ou `shutdown abort`, o Oracle:
- Usa os **redologs** para refazer (REDO) alterações não gravadas nos datafiles.
- E usa os **undo segments** para fazer o **undo de transações não comitadas**.

---

## 🔧 4. **Flashback Query e Flashback Table**
Quando você usa recursos como:
```sql
SELECT * FROM tabela AS OF TIMESTAMP(SYSTIMESTAMP - INTERVAL '10' MINUTE);
```
Ou:
```sql
FLASHBACK TABLE tabela TO TIMESTAMP ...
```
O Oracle **acessa os undo segments** para reconstruir os dados como estavam no passado.

---

## 🧹 5. **Garantia de atomicidade de transações**
Mesmo operações internas ou falhas parciais precisam ser revertidas.  
O Oracle garante isso com UNDO — inclusive para DDLs que envolvem modificações (ex: `CREATE TABLE AS SELECT`, etc.).

---

## ✅ Conclusão
**UNDO serve para**, mas também para:
- ⬅️ **Rollback (reversão)**
- 📖 **Leitura consistente**
- 🔁 **Recuperação após falha**
- 🕓 **Consultas temporais (flashback)**
- 🔄 **Garantia de atomicidade**



---

### 🧾 **Resumo dos Usos da UNDO no Oracle**

| **#** | **Uso da UNDO**                         | **Descrição**                                                                 |
|------:|------------------------------------------|-------------------------------------------------------------------------------|
| 1️⃣   | Rollback de Transações                  | Permite reverter alterações de transações não comitadas.                     |
| 2️⃣   | Consistência de Leitura (*Read Consistency*) | Garante que consultas vejam os dados como estavam no início da execução.     |
| 3️⃣   | Recuperação após falha (*Crash Recovery*) | Reverte transações inacabadas usando UNDO após falhas ou `shutdown abort`.   |
| 4️⃣   | Flashback Query / Table                 | Reconstrói o estado de uma tabela no passado com base nos undo segments.     |
| 5️⃣   | Atomicidade de Transações Internas      | Garante que o Oracle possa desfazer alterações parciais em operações internas. |

---
