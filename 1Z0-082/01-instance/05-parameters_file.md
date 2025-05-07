O **SPFILE** (Server Parameter File) é um arquivo binário utilizado pelo Oracle Database para armazenar parâmetros de inicialização do banco de dados de forma **persistente e dinâmica**.

## 🔍 **SPFILE vs. PFILE (init.ora)**

| Característica        | SPFILE                           | PFILE (init.ora)                |
|------------------------|-----------------------------------|----------------------------------|
| Formato               | Binário                          | Texto                           |
| Editável diretamente? | ❌ Não (use comandos SQL)         | ✅ Sim (em um editor de texto)   |
| Suporta alteração em tempo real? | ✅ Sim (ALTER SYSTEM SET ...) | ❌ Não                           |
| Armazenamento         | Geralmente em `$ORACLE_HOME/dbs` ou +ASM | `$ORACLE_HOME/dbs`            |


## ✅ Vantagens do SPFILE

- Permite alterações dinâmicas sem reiniciar o banco.
- Mantém histórico de parâmetros modificados.
- Pode ser usado com Oracle RAC (parâmetros por instância).
- Recomendado para ambientes **em produção**.


## 🔧 Comandos úteis

### Verificar se o banco está usando SPFILE:
```sql
SHOW PARAMETER spfile;
```

### Criar SPFILE a partir do PFILE:
```sql
CREATE SPFILE FROM PFILE='/caminho/para/init.ora';
```

### Criar PFILE a partir do SPFILE:
```sql
CREATE PFILE='/caminho/init_backup.ora' FROM SPFILE;
```

### Alterar parâmetro e salvar no SPFILE:
```sql
ALTER SYSTEM SET sga_target = 2G SCOPE=SPFILE;
```
> ⚠️ Exige restart do banco para valer.


## 🧠 Dicas
 - Sempre mantenha um backup do PFILE com os parâmetros principais do seu banco. Em casos de problemas no SPFILE, ele será essencial para startup.
 - Sempre criar um PFILE antes de mudar parâmetros no SPFILE que possam causar erro para subir a instância, exemplo:
     - alteração do tamanho da SGA

