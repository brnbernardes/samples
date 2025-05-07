# **LGWR é acionado mesmo sem um COMMIT**, ou seja, durante um **ROLLBACK** que gera redo.

---

### 📘 Cenário:  
Você inicia uma transação, altera um dado, e depois faz `ROLLBACK`.  
Mesmo sem commit, **LGWR pode escrever no redo log**. Veja:

```sql
-- Início da transação
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;

UPDATE contas SET saldo = saldo + 100 WHERE id = 2;

-- Agora, por algum motivo, você decide reverter a transação
ROLLBACK;
```

---

### 📂 O que acontece internamente?

1. O Oracle **modifica os blocos em memória (buffer cache)**.
2. Essas alterações geram **redo entries** para permitir **refazer**, se necessário.
3. Também são geradas **undo entries**, para permitir o **rollback**.

🔄 Agora entra a parte interessante:

> Durante o `ROLLBACK`, o Oracle **usa o undo** para desfazer as alterações nos buffers.
>
> **Esse undo também gera redo!**

---

### 🔥 Por quê?  
Porque o **rollback modifica blocos de undo** e potencialmente **blocos de dados** (para reverter), e **toda modificação no Oracle precisa de redo**, para garantir que em caso de crash a operação de rollback possa ser refeita no recovery.

Portanto, durante o `ROLLBACK`, o **LGWR escreve no redo log** para registrar:
- As alterações feitas nos segmentos de undo.
- As alterações feitas nos blocos de dados restaurados ao valor anterior.

---

### 💡 Conclusão:

Mesmo sem `COMMIT`, o `ROLLBACK` **pode acionar o LGWR**, porque:
- Blocos estão sendo modificados (undo sendo usado).
- E o Oracle **nunca permite alteração de bloco sem gerar redo** (a menos que esteja em modo `NOLOGGING`, o que é outra história).
