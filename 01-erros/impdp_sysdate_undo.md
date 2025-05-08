Hoje durante a exeução de uma rotina corriqueira de importação via DBLINK, o DBA se deparou com o seguinte erro:

```
impdp '"sys/t#ADMIN#99z@pmmontenegroh as sysdba"' \
directory=DATAPUMP_NFS \
logfile=impdp_owner_teste.log \
network_link=DBLINK_PROD \
schemas=OWNER_TESTE \
flashback_time=\"TO_TIMESTAMP\(SYSDATE, \'DD-MM-YYYY HH24:MI:SS\'\)\"

```
ORA-01555: snapshot too old: rollback segment number % with name "_SYSSMU19_3746788368$" too small





impdp '"sys/t#ADMIN#99z@pmmontenegroh as sysdba"' directory=DATAPUMP_NFS logfile=impdp_owner_teste.log network_link=DBLINK_PROD schemas=OWNER_TESTE FLASHBACK_TIME=\"TO_TIMESTAMP\(SYSDATE, \'DD-MM-YYYY HH24:MI:SS\'\)\"
.
.
.
Processing object type SCHEMA_EXPORT/TABLE/TABLE

ORA-31693: Table data object "OWNER_TESTE"."TABELA1" failed to load/unload and is being skipped due to error:
ORA-02358: error in exporting or importing data
ORA-01555: snapshot too old: rollback segment number 19 with name "_SYSSMU19_3746788368$" too small
ORA-02063: preceding line from DBLINK_PROD

ORA-31693: Table data object "OWNER_TESTE"."TABELA2" failed to load/unload and is being skipped due to error:
ORA-02358: error in exporting or importing data
ORA-01555: snapshot too old: rollback segment number 7 with name "_SYSSMU7_1274479119$" too small
ORA-02063: preceding line from DBLINK_PROD




impdp '"sys/t#ADMIN#99z@pmmontenegroh as sysdba"' directory=DATAPUMP_NFS logfile=impdp_owner_teste.log network_link=DBLINK_PROD schemas=OWNER_TESTE FLASHBACK_TIME=\"TO_TIMESTAMP\(\'08/05/2025 11:05:00\', \'DD-MM-YYYY HH24:MI:SS\'\)\"