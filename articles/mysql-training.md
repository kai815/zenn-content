---
title: " MySQLのトレーニングにいいDockerイメージ"
emoji: "🎩"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["MySQL", "Docker"]
published: false
---

## 環境

macOS Big Sur
バージョン 11.2.3

docker desktop
バージョン 3.1.0

##

https://hub.docker.com/r/genschsa/mysql-employees

```
$ docker pull genschsa/mysql-employees
```

当たり前ですが、DockerDeskTop を起動してくださいね

```
 docker run -d \
  --name mysql-employees \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=college \
  -v $PWD/data:/var/lib/mysql \
  genschsa/mysql-employees
```

起動してるか確認

```
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
f656a298255f genschsa/mysql-employees "docker-entrypoint.s…" 2 minutes ago Up 2 minutes 0.0.0.0:3306->3306/tcp, 33060/tcp mysql-employees
```

コンテナに入る

```
$ docker exec -i -t mysql-employees bash
```

MySQL にログイン

```
root@f656a298255f:/# mysql -u root -p
Enter password:
```

パスワードは college

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| employees          |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

```
mysql> use employees
```

```
mysql> show tables;
+----------------------+
| Tables_in_employees  |
+----------------------+
| current_dept_emp     |
| departments          |
| dept_emp             |
| dept_emp_latest_date |
| dept_manager         |
| employees            |
| salaries             |
| titles               |
| v_full_departments   |
| v_full_employees     |
+----------------------+
10 rows in set (0.00 sec)
```

```
mysql> show columns from employees;
+------------+---------------+------+-----+---------+-------+
| Field      | Type          | Null | Key | Default | Extra |
+------------+---------------+------+-----+---------+-------+
| emp_no     | int(11)       | NO   | PRI | NULL    |       |
| birth_date | date          | NO   |     | NULL    |       |
| first_name | varchar(14)   | NO   |     | NULL    |       |
| last_name  | varchar(16)   | NO   |     | NULL    |       |
| gender     | enum('M','F') | NO   |     | NULL    |       |
| hire_date  | date          | NO   |     | NULL    |       |
+------------+---------------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```
