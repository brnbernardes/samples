# Script: redos.sql
```sql
set feed off
prompt -----------------------------------------;
prompt | Histórico de geração de REDOS por HORA|
prompt -----------------------------------------;
SELECT to_char(first_time, 'DD/MM/YYYY') data,
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '00', 1,0)), '999') "00",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '01', 1,0)), '999') "01",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '02', 1,0)), '999') "02",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '03', 1,0)), '999') "03",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '04', 1,0)), '999') "04",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '05', 1,0)), '999') "05",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '06', 1,0)), '999') "06",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '07', 1,0)), '999') "07",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '08', 1,0)), '999') "08",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '09', 1,0)), '999') "09",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '10', 1,0)), '999') "10",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '11', 1,0)), '999') "11",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '12', 1,0)), '999') "12",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '13', 1,0)), '999') "13",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '14', 1,0)), '999') "14",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '15', 1,0)), '999') "15",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '16', 1,0)), '999') "16",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '17', 1,0)), '999') "17",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '18', 1,0)), '999') "18",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '19', 1,0)), '999') "19",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '20', 1,0)), '999') "20",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '21', 1,0)), '999') "21",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '22', 1,0)), '999') "22",
       to_char(SUM(decode(substr(to_char(first_time, 'HH24'), 1, 2), '23', 1,0)), '999') "23",
       SUM(1) "TOTAL_IN_DAY"
  FROM v$log_history
WHERE first_time >= trunc(sysdate)-30
 GROUP BY to_char(first_time, 'DD/MM/YYYY')
 ORDER BY data;
prompt ;
prompt ;
prompt ;
prompt --------------------------------------------------------------------;
prompt |Verificar média de switches do dia anterios                      |
prompt --------------------------------------------------------------------;
SELECT thread#,
       COUNT(1) AS total_switches,
       COUNT(distinct to_char(first_time, 'hh24')) quantity_hours,
       ROUND(COUNT(1) / COUNT(distinct to_char(first_time, 'hh24'))) avg_switches_per_hour
  FROM v$log_history
 WHERE trunc(first_time) = trunc(sysdate-1)
 GROUP BY thread#
 ORDER BY thread#;

prompt ;
prompt ;
prompt -----------------------------------------;
prompt | Info REDOS                            |
prompt -----------------------------------------;
col GROUP_MEMBER for a60
SELECT l.group# group_number,
       l.thread#,
       l.sequence#,
       l.bytes / 1024 / 1024 AS size_mb,
       l.status group_status,
       f.member group_member,
       l.archived
  FROM v$log     l,
       v$logfile f
 WHERE l.group# = f.group#
 ORDER BY  l.thread#, l.sequence#;
prompt ;
```

### Cálculo para tamnho de redos

> Novo_Tamanho = (Volume médio de redo por hora / Nº de grupos) / switches desejados por hora

```
Exemlo de Cáculo por Thread (instância) em um ambiente RAC: 
 -> Se você está gerando 34 redos de 20MB cada por hora na THREAD 1 e tem 3 grupos:
    680 GB / 3 grupos = 226MB por grupo
 -> Se deseja 4 switches/hora 
    226 MB / 4 
    => 60MB por grupo
```

## Remoção de redos


## Adição de redos

```sql
-- Com OMF ativo:
ALTER DATABASE DROP LOGFILE GROUP 107;
ALTER DATABASE ADD LOGFILE GROUP 107 ('+DATA', '+RECO') SIZE 6144M;
```