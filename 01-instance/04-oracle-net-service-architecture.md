## 🧱 Oracle Net Services Architecture

Oracle Net Services é o componente que gerencia a comunicação entre os clientes e o banco de dados Oracle através da rede.

Ao estabelecer essa comunicação, dois modelos principais podem ser utilizados:

---

## 🧑‍💻 Dedicated Server (Servidor Dedicado)

### 🔧 Como funciona:
- Para **cada sessão de usuário** conectada ao banco, o Oracle **cria um processo servidor exclusivo** (dedicado).
- O processo dedicado lida com todas as requisições dessa sessão.

### 📊 Características:
- ✅ Boa performance para aplicações com pouca concorrência.
- ✅ Mais simples de configurar.
- ❌ Pode consumir muitos recursos (memória e processos) quando há muitos usuários conectados.
- ❌ Não escala bem em ambientes com milhares de sessões.

### 📌 Fluxo:

```
Cliente 1 ─┬─> Listener ─> Processo dedicado 1
Cliente 2 ─┤
Cliente 3 ─┴─> Listener ─> Processo dedicado 2 ...
```

---

## 🔄 Shared Server (Servidor Compartilhado)

### 🔧 Como funciona:
- Vários usuários compartilham um **conjunto de processos servidores**.
- Ao invés de um processo por sessão, há:
  - 🧵 Dispatcher Process (Dnnn): recebe as requisições dos clientes.
  - 🔁 Shared Server Process (Snnn): executa os comandos das sessões.

### 📊 Características:
- ✅ Economiza memória e processos no sistema operacional.
- ✅ Escala melhor com muitos usuários conectados (ideal para sistemas com muitos acessos leves).
- ❌ Pode ter mais latência e overhead de gerenciamento.
- ❌ Mais complexidade de configuração e tuning.

### 📌 Fluxo:

```
Cliente 1 ─┐
Cliente 2 ─┤
Cliente 3 ─┘
            ↓
         Dispatcher (Dnnn)
            ↓
     Shared Server Pool (S000, S001, ...)
```

---

## ⚖️ Comparativo Rápido

| Característica              | Dedicated Server             | Shared Server                      |
|----------------------------|------------------------------|-------------------------------------|
| Escalabilidade              | Baixa                        | Alta                                |
| Consumo de memória          | Alto                         | Baixo                               |
| Performance por sessão      | Alta (menos latência)        | Boa (pode variar)                   |
| Complexidade de configuração| Baixa                        | Moderada (requer parâmetros extras) |
| Ideal para...               | OLAP, cargas pesadas por sessão | OLTP, muitos acessos leves       |

---

## 🔍 Verificando o modo atual de conexão

Execute a seguinte consulta para verificar se o Shared Server está habilitado:

```sql
SELECT name, value
FROM v$parameter
WHERE name IN ('shared_servers', 'dispatchers');
```

Se:

- shared_servers > 0 e dispatchers está configurado → Shared Server está ativo.
- shared_servers = 0 ou dispatchers não está configurado → Dedicated Server (modo padrão).


## ⚙️ Configurando o modo Shared Server

Se você quiser ativar o modo Shared Server, você pode configurar os seguintes parâmetros no SPFILE ou PFILE:

Exemplo de configuração:

```sql
-- Define que o Oracle usará Shared Server
ALTER SYSTEM SET shared_servers = 5 SCOPE=SPFILE;

-- Configura dispatcher para o protocolo TCP/IP
ALTER SYSTEM SET dispatchers = '(PROTOCOL=TCP)(SERVICE=orclXDB)' SCOPE=SPFILE;

-- (Opcional) Define o número máximo de servidores compartilhados
ALTER SYSTEM SET max_shared_servers = 20 SCOPE=SPFILE;

-- (Opcional) Número máximo de dispatchers
ALTER SYSTEM SET max_dispatchers = 10 SCOPE=SPFILE;
```

📌 Importante:

- Essas alterações exigem que a instância seja reiniciada (se feitas no SPFILE).
- O parâmetro dispatchers define o protocolo e o serviço (por exemplo, `SERVICE=orclXDB`).
- Você pode também configurar shared server no listener.ora e tnsnames.ora (no cliente).

## 🔄 Reverter para Dedicated Server (modo padrão)

Caso deseje desativar o Shared Server:

```sql
ALTER SYSTEM SET shared_servers = 0 SCOPE=SPFILE;
ALTER SYSTEM RESET dispatchers SCOPE=SPFILE SID='*';
```

Depois, reinicie a instância:

```sql
SHUTDOWN IMMEDIATE;
STARTUP;
```
