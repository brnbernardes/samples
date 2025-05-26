
# ⚙️ Opções de Configuração de Memória no Oracle

### 1. 🧠 Automatic Shared Memory Management (ASMM)
- Você define os limites de memória para a SGA e a PGA separadamente.
- O Oracle gerencia automaticamente a distribuição interna dos componentes da SGA.
- Parâmetros principais:
  - sga_target
  - sga_max_size
  - pga_aggregate_target
- Boa escolha para a maioria dos ambientes, com equilíbrio entre controle e automação.

### 2. 🔄 Automatic Memory Management (AMM)
- Você define apenas um limite total de memória.
- Oracle distribui automaticamente entre SGA e PGA conforme necessário.
- Parâmetros principais:
  - memory_target
  - memory_max_target
- Simples de configurar, mas com menos controle. Ideal para ambientes pequenos ou não críticos.

### 3. 🧩 Manual Shared Memory Management
- Você define explicitamente os tamanhos dos pools de memória (shared pool, buffer cache, etc).
- Parâmetros como:
  - shared_pool_size
  - db_cache_size
  - large_pool_size, etc.
- Indicado para DBAs experientes que conhecem profundamente o comportamento da aplicação.

## 📊 Comparativo: Modos de Gerenciamento de Memória no Oracle

| Característica                      | AMM (Automatic Memory Management)           | ASMM (Automatic Shared Memory Management)   | Manual Shared Memory Management             |
|------------------------------------|---------------------------------------------|---------------------------------------------|---------------------------------------------|
| 🎯 Controle de Memória             | Oracle gerencia tudo (SGA + PGA)            | Oracle gerencia componentes da SGA          | DBA define tudo manualmente                 |
| 📦 Parâmetro principal              | memory_target / memory_max_target           | sga_target / sga_max_size / pga_aggregate_target | shared_pool_size, db_cache_size, etc. |
| ⚙️ Ajuste dinâmico                 | Sim                                          | Sim                                          | Não (ou com restrições)                     |
| 🧠 Complexidade de configuração    | Baixíssima                                   | Moderada                                     | Alta                                         |
| 🕹️ Nível de controle do DBA        | Muito baixo                                  | Médio                                        | Alto                                         |
| 🧪 Ideal para                      | Ambientes pequenos, não críticos            | Ambientes OLTP ou mistos                     | Ambientes com comportamento previsível      |
| ❗ Compatibilidade com HugePages    | ❌ Não compatível                           | ✅ Compatível                                | ✅ Compatível                                |


💡 Dica:  
- Use ASMM em ambientes OLTP que exigem performance com flexibilidade.  
- Use AMM se você quer simplicidade acima de tudo (ex: em DEV ou testes).  
- Use gerenciamento MANUAL quando conhecer muito bem a carga e precisar de controle fino (ex: bancos críticos, tuning avançado, RAC com HugePages).

---

### Utilização do `ASMM + PG Automática`:
OBS: sga_max_size e memory_max_target não são dinâmicos, portanto, se for necessário alterá-los você precisará reiniciar a instância.
```sql
-- Zerar parâmetros do AMM:
alter system set  memory_max_target = 0 scope=spfile;
alter  system set memory_target = 0 scope=spfile;

-- ASMM + PGA automática (x = valor em GB):
alter system set sga_target = xG scope=spfile; --  tamanho desejado da SGA
alter system set sga_max_size = xG scope=spfile;   -- tamanho máximo da SGA
alter system set pga_aggregate_target = xG scope=spfile; -- tam.  desejado da PGA
```

### Verificações:
```sql
-- verificar PGA, se "ESTD_OVERALLOC_COUNT" > 0 considerar aumentár a área de memória  
-- escolha o menor valor que tenha cache_hit_perc > 90% e overalloc_count = 0 
SELECT round(pga_target_for_estimate / 1024 / 1024) target_mb,
       estd_pga_cache_hit_percentage cache_hit_perc,
       estd_overalloc_count
  FROM v$pga_target_advice
 WHERE ROUND(pga_target_for_estimate / 1024 / 1024) IN
       (SELECT VALUE / 1024 / 1024 parameter
          FROM v$parameter
         WHERE NAME IN ('pga_aggregate_limit', 'pga_aggregate_target'))
 AND estd_overalloc_count = 0
/ 

-- verificar se na SGA estão ocorrendo muitas operações de redimensionamento de memórias automáticas, se sim, considerar aumentar a área de memória
SELECT component,
       oper_type,
       oper_mode,
       parameter,
       initial_size,
       final_size,
       to_char(start_time, 'dd/mm/yyyy  hh24:mi:ss') start_time,
       to_char(end_time, 'dd/mm/yyyy  hh24:mi:ss') end_time
  FROM v$memory_resize_ops;

SELECT component,
       current_size/1024/1024 AS current_size_mb,
       min_size/1024/1024     AS min_size_mb,
       max_size/1024/1024     AS max_size_mb,
       user_specified_size/1024/1024 AS user_specified_size_mb,
       last_oper_type
  FROM v$sga_dynamic_components
ORDER BY current_size DESC;

-- Se LAST_OPER_TYPE mostra muitas operações (GROW/SHRINK), pode indicar que o Oracle está ajustando constantemente esse componente por necessidade de memória.
-- Se o shared pool ou buffer cache estiver com muito SHRINK, talvez estejam superdimensionados.
-- Se o large pool ou java pool estiver muito pequeno e crescendo, talvez estejam subdimensionados.

```

---

## 💡 Diretriz Geral para Dimensionamento de Memória no Oracle

### 🧮 Fórmula Base (para servidores dedicados ao Oracle)

Se o servidor for dedicado ao Oracle Database, uma prática comum é:

- 🔸 80% da memória física total para Oracle
- 🔸 Divida essa memória entre SGA e PGA da seguinte forma:

```
Total Oracle Memória = RAM do servidor × 0.8

SGA Target           = Total Oracle Memória × 0.70
PGA Aggregate Target = Total Oracle Memória × 0.30
```

## 🔍 Ajustes baseados em observações

### Para SGA:
- Verifique se está usando Automatic Shared Memory Management (ASMM) ou Manual.
- Use v$sga_dynamic_components para ver o que está sendo bem ou mal utilizado.
- Use AWR ou estatísticas do ADDM para ver se há contenção em buffer cache, shared pool etc.

### Para PGA:
- Consulte v$pga_target_advice para entender o impacto de aumentar a PGA.
- Monitore v$process_memory e v$pgastat para ver se há muitos overallocations.
