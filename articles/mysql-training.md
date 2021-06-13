---
title: " MySQLのトレーニングに使えるDockerイメージ"
emoji: "🎩"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["MySQL", "Docker"]
published: true
---

[mysql-employees](https://hub.docker.com/r/genschsa/mysql-employees)というイメージが MySQL の練習にとても便利なので、
そちらを使い始めるまでの手順を紹介します。
実際に使い始めるまでに、自分は「あれ？MySQL にログインするときのパスワードは？』など意外と詰まってしまったので、
同じような方の役に立ったら嬉しいです。

## 環境

macOS Big Sur
バージョン 11.2.3

docker desktop
バージョン 3.1.0

## MySQL を使えるようにする

イメージを pull します。

```
$ docker pull genschsa/mysql-employees
```

※当たり前ですが、DockerDeskTop を起動してくださいね

イメージを動かします。

```
 docker run -d \
  --name mysql-employees \
  -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=college \
  -v $PWD/data:/var/lib/mysql \
  genschsa/mysql-employees
```

起動してるか確認します。

```
$ docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
f656a298255f genschsa/mysql-employees "docker-entrypoint.s…" 2 minutes ago Up 2 minutes 0.0.0.0:3306->3306/tcp, 33060/tcp mysql-employees
```

コンテナに入ります。

```
$ docker exec -i -t mysql-employees bash
```

`mysql-employees` はイメージの名前です。

MySQL にログイン

```
root@f656a298255f:/# mysql -u root -p
Enter password:
```

パスワードは `college`です。
docker run コマンドをしたときに指定してました。
`MYSQL_ROOT_PASSWORD=college`のところです。

## MySQL で実際に操作してみる

データベースを見ます。

```sql
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| employees          |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
```

employess のデータベースを使います。

```sql
mysql> USE employees
```

テーブルを確認します。

```sql
mysql> SHOW TABLES;
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

emloyess テーブルのカラムを確認します。

```sql
mysql> SHOW COLUMNS FROM employees;
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

employee の数を集計します。

```sql
mysql> SELECT COUNT(*) FROM employees;
+----------+
| COUNT(*) |
+----------+
|   300024 |
+----------+
1 row in set (0.04 sec)
```

データも元々多く入っているので、トレーニングで便利ですね。

gender が M の employee の数を集計します。

```sql
mysql> SELECT COUNT(*) FROM employees WHERE gender = 'M';
+----------+
| COUNT(*) |
+----------+
|   179973 |
+----------+
1 row in set (0.03 sec)
```

## 最後に

mysql-employees を使うことで、手軽に MySQL のトレーニング環境を手に入れることができました。
データも元々入っているので、データを投入する手間なども削減されるでしょう。
元に戻したい時にもコンテナを削除して再び立ち上げればいいので、
気持ち的に楽にトレーニングできるのではないでしょうか。

この記事が誰かの役に立ったら嬉しいです。
