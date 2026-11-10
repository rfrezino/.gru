---
type: "Script Group"
title: "Coolblue Oracle/AWS tooling"
description: "Python scripts in coolblue/ for Oracle DBA tasks, KMS and RDS reminders"
---

Python scripts under `coolblue/`; most import an external `env.get_db_con` (not in repo).

- `oracle_test_utils.py`: cx_Oracle helpers (`data_exists`, `grant_exists`, `query`, `query_named`, `execute`...).
- `get_expensive_queries_for_user.py <user> <today|yesterday>`: reports expensive queries via SQL on Oracle.
- `recalculate_statistics.py`: finds stale stats in `DBA_TAB_STATISTICS` (owners OMS, DEVVANESSA) and recalculates.
- `sql_ta.py`: `SqlTunningAdvisor` wrapper printing tuning recommendations for a `sql_id`.
- `list_rds_in_dev.py`: lists RDS instances in the AWS `development` profile and builds a Slack reminder message.
- `kms_gen`: encrypts values for environments testing/development/acceptance/production.
