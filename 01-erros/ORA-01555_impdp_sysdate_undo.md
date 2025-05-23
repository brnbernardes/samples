# ORA-01555 com IMPDP via DBLINK e uso de SYSDATE no FLASHBACK\_TIME

Durante a execução de uma rotina corriqueira de importação com o Oracle Data Pump (`impdp`) via `DBLINK`, nos deparamos com o seguinte erro ao utilizar `FLASHBACK_TIME` baseado em `SYSDATE`.

---

## ⚠️ Erro encontrado:

```bash
impdp '"sys/****@pdb1 as sysdba"' \
  directory=DATAPUMP_NFS \
  logfile=impdp_owner_teste.log \
  network_link=DBLINK_PROD \
  schemas=OWNER_TESTE \
  flashback_time=\"TO_TIMESTAMP\(SYSDATE, \'DD-MM-YYYY HH24:MI:SS\'\)\"
```

> ❌ `ORA-01555: snapshot too old: rollback segment number % with name "_SYSSMU19_..." too small`

### 🔍 Trecho do log

```text
ORA-31693: Table data object "OWNER_TESTE"."TABELA1" failed to load/unload and is being skipped due to error:
ORA-01555: snapshot too old: rollback segment number 19 with name "_SYSSMU19_..." too small
ORA-02063: preceding line from DBLINK_PROD

ORA-31693: Table data object "OWNER_TESTE"."TABELA2" failed to load/unload and is being skipped due to error:
ORA-01555: snapshot too old: rollback segment number 7 with name "_SYSSMU7_..." too small
ORA-02063: preceding line from DBLINK_PROD
```

---

## 🔎 Análise:

###  Verificação de UNDO

Inicialmente avaliamos a possibilidade de problema com retenção de UNDO. No entanto, a `UNDO` estava com `RETENTION GUARANTEE` e com baixa utilização.

#### Consulta sobre espaço e retenção:

```sql
SELECT tbm.tablespace_name,
       ROUND(tbm.tablespace_size * tb.block_size / (1024*1024*1024), 2) AS tablespace_size_gb,
       ROUND(tbm.used_space * tb.block_size / (1024*1024*1024), 2) AS used_size_gb,
       ROUND((tbm.tablespace_size - tbm.used_space) * tb.block_size / (1024*1024*1024), 2) AS free_size_gb,
       tbm.used_percent,
       tb.retention,
       (SELECT VALUE FROM v$parameter WHERE name = 'undo_retention') AS undo_retention
  FROM dba_tablespace_usage_metrics tbm
  JOIN dba_tablespaces tb ON tb.tablespace_name = tbm.tablespace_name
 WHERE tb.contents = 'UNDO'
 ORDER BY used_percent DESC;
```

| TABLESPACE\_NAME | SIZE\_GB | USED\_GB | FREE\_GB | USED\_% | RETENTION | UNDO\_RETENTION |
| ---------------- | -------- | -------- | -------- | ------- | --------- | --------------- |
| UNDOTBS1         | 32.00    | 0.59     | 31.41    | 1.84%   | GUARANTEE | 3600            |

####  Histórico de uso:

```sql
SELECT begin_time,
       tuned_undoretention,
       maxquerylen,
       activeblks
  FROM v$undostat
 WHERE begin_time >= TO_DATE('08/05/2025 16:15', 'dd/mm/yyyy hh24:mi')
 ORDER BY begin_time DESC;
```

| BEGIN\_TIME         | TUNED\_UNDORETENTION | MAXQUERYLEN | ACTIVEBLKS |
| ------------------- | -------------------- | ----------- | ---------- |
| 08/05/2025 16:35:38 | 3600                 | 548         | 2304       |
| 08/05/2025 16:25:38 | 3600                 | 247         | 2304       |
| 08/05/2025 16:15:38 | 3600                 | 0           | 2304       |

As estatísticas mostram que a retenção de UNDO estava consistente com a duração das transações. Nenhuma pressão de sobrescrita foi observada.

---

## 🕵️ O problema: FLASHBACK\_TIME com SYSDATE

O parâmetro usado no `impdp` foi:

```bash
flashback_time="TO_TIMESTAMP(SYSDATE, 'DD-MM-YYYY HH24:MI:SS')"
```

Esse valor indica que a exportação deve capturar os dados conforme o estado atual (`SYSDATE`) do banco **remoto** acessado via `DBLINK`.

 **Importante**:
 
 1) o SYSDATE é avaliado **no banco remoto**, que deve estar:
* Em modo `ARCHIVELOG`
* Com UNDO suficiente para garantir a consistência dos blocos lidos a partir daquele timestamp

2) a execução da function SYSDATE para data e hora é no banco local que receberá o impdp
   
---

### 📌 Diferença de horário entre CDB e PDB:

Verificamos o horário no **CDB**:

```
[oracle@srv-oracle-hml ~]$ sqlplus / as sysdba

16:50:12 SYS@CDB$ROOT> select sysdate from dual;

  SYSDATE
  -------------------
  08/05/2025 16:50:17
```

Mas, ao nos conectarmos via TNS diretamente ao `PDB1` (como ocorre no `impdp`):

```
[oracle@srv-oracle-hml ~]$ sqlplus sys/*****@PDB1 as sysdba

16:51:15 SYS@PDB1> SELECT sysdate from dual;

    SYSDATE
   -------------------
⚠️ 08/05/2025 19:51:32
```

🚨 O SYSDATE estava incorreto no PDB por diferença de configuração de timezone entre os componentes do Grid Infrastructure e o banco de dados.

---

## ✅ Soluções:

### 1) Solução paliativa:

Utilizar um `FLASHBACK_TIME` **estático**, especificando manualmente um horário próximo ao esperado:

```bash
flashback_time="TO_TIMESTAMP('08/05/2025 16:50:00', 'DD/MM/YYYY HH24:MI:SS')"
```

### 2) Solução definitiva:
#### Corrigir o **timezone do Grid Infrastructure (GI)** para o mesmo utilizado no server, no meu caso de UTC para AMERICA/SAO_PAULO

Validar timezone atual do server:
```
[root@srv-oracle-hml ~]# timedatectl
      Local time: Fri 2025-05-09 17:54:29 -03
  Universal time: Fri 2025-05-09 20:54:29 UTC
        RTC time: Fri 2025-05-09 20:54:29
       Time zone: America/Sao_Paulo (-03, -0300)
     NTP enabled: yes
NTP synchronized: no
 RTC in local TZ: no
      DST active: n/a
```

Ajustar o arquivo
> $GRID_HOME/crs/install/s_crsconfig_<hostname>_env.txt
```
[grid@srv-oracle-hml ~]$ cat /u01/app/19.0.0.0/grid/crs/install/s_crsconfig_srv-oracle-hml_env.txt |egrep ^TZ
TZ=UTC
```

Editar para
```
[grid@srv-oracle-hml ~]$ cat /u01/app/19.0.0.0/grid/crs/install/s_crsconfig_srv-oracle-hml_env.txt |egrep ^TZ
TZ=America/Sao_Paulo
```

Descubra o database_name
```
[root@srv-oracle-hml ~]# $GRID_HOME/bin/srvctl config database -v
HML01   /u01/app/oracle/product/19.0.0.0/dbhome_1       19.0.0.0.0
```

Altere o valor TZ no nível do banco de dados para o timezone desejado:
> srvctl setenv database -d <database_name> -t "TZ=<new_time_zone>"
```
[root@srv-oracle-hml ~]# $GRID_HOME/bin/srvctl setenv database -d HML01 -t "TZ=America/Sao_Paulo"
```
Valide a alteração:
>  srvctl getenv database -d <database_name>
```
[grid@srv-oracle-hml ~]$ srvctl getenv database -d HML01
HML01:
TZ=America/Sao_Paulo
```
Após o ajuste, o CRS foi reiniciado para aplicar a nova configuração. 


Mas para essa explicação do porque do problema de horários deixo aqui ótimo artigo do Maicon Carneiro e a documentação da Oracle:
* Blog do Maicon Carneiro – [Oracle SYSDATE com horário errado via listener](https://dibiei.blog/2021/06/03/oracle-sysdate-com-horario-errado-quando-conectando-via-listener-devido-ao-time-zone-do-grid-infrastructure/)
* Documentação Oracle - [Time Zone Management](https://docs.oracle.com/en/cloud/paas/base-database/time-zone/index.html#ChangeHostTimeZone)

