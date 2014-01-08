# pg_stat_statements の使用方法

参考：  
http://www.postgresql.jp/document/9.3/html/pgstatstatements.html  
http://www.postgresql.jp/document/9.3/html/runtime-config-resource.html#GUC-SHARED-PRELOAD-LIBRARIES  


## pg_stat_statements とは

SQL文実行時のI/O回数やCPU使用時間などの実行統計を記録する追加機能。  


## インストール

### 1 postgresql-contribパッケージの導入

PostgreSQLのインストール時にpostgresql-contribもインストールをしておく。  
後からでもインストール可能。  
  
ソースからインストールしている場合は、以下の手順でpg_stat_statementsもビルドされているか確認できる。  

```
$ pg_config --libdir
/usr/lib        ←ライブラリの格納場所の確認

$ find /usr/lib -name pg_stat_statements.so
/usr/lib/postgresql/9.2/lib/pg_stat_statements.so   ←これが見つかればビルド済み
```


### 2 postgres.confの設定

pg_stat_statementsを使用するためには、事前にpostgres.confのshared_preload_librariesに
pg_stat_statementsを追加しておく必要がある。

```
postgres.conf
----------------
shared_preload_libraries = 'pg_stat_statements'

```


### 3 pg_stat_statements用の追加パラメータの設定

必要に応じて、pt_stat_statement用の追加パラメータをpostgres.confへ追加する。

```
custom_variable_classes = 'pg_stat_statements'  # PstgreSQL標準ではないパラメータを追加する設定
pg_stat_statements.max = 10000
pg_stat_statements.track = all
```

参考：  
http://www.postgresql.jp/document/9.3/html/pgstatstatements.html  
http://www.postgresql.jp/document/9.0/html/runtime-config-custom.html#GUC-CUSTOM-VARIABLE-CLASSES  


### 4 pg_stat_statements.sqlインストールスクリプトの実行

手順1の設定によって、DBクラスタ内の全DBにおいて実行統計が記録されるようになるが、pg_stat_statementsビューと、
pg_stat_reset関数を使用するには、各DB毎に明示的なインストールが必要となる。  
インストールスクリプトはPostgreSQLのインストール先のextensionの配下となる。  
Ubuntuの場合は/usr/share/postgresql/9.2/extension/の配下に以下の2つのファイルがあるが、1.1の方だけ実行すればよい。  
  
+ pg_stat_statements--1.0--1.1.sql
+ pg_stat_statements--1.1.sql

```
psql -f /usr/share/postgresql/9.2/extension/pg_stat_statements--1.1.sql -d DB名 -U ユーザ名 -h ホスト名
```


