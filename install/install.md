# Install

OS: Ubuntu 12.04 on CloudCore  
PostgreSQL: 9.2  


## debパッケージリポジトリの追加

参考：http://siguniang.wordpress.com/2013/02/02/install-postgresql-9-2-on-ubuntu-12-04/  

### apt-getで検索対象となるリポジトリに apt.postgresq.org を追加する。  

    sudo vi /etc/apt/sources.list.d/pgdg.list

### 以下を入力し保存

    deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main

### リポジトリの公開鍵を確認するために、いったん apt-get update する

    sudo apt-get update

    W: GPG error: http://apt.postgresql.org precise-pgdg Release: The following signatures couldn''t be verified because the public key is not available: NO_PUBKEY 7FCC7D46ACCC4CF8
    OK


### 警告メッセージに表示された公開鍵を追加する

    wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -


### もう一度 apt-get update

    sudo apt-get update


### 9.2がリポジトリにあることを確認

    sudo apt-cache search postgresql | grep 9.2

    postgresql-9.2 - object-relational SQL database, version 9.2 server
    postgresql-9.2-dbg - debug symbols for postgresql-9.2
    postgresql-9.2-debversion - Debian version number type for PostgreSQL
    postgresql-9.2-ip4r - IPv4 and IPv6 types for PostgreSQL 9.2
    postgresql-9.2-pgmemcache - PostgreSQL interface to memcached
    postgresql-9.2-pgmp - arbitrary precision integers and rationals for PostgreSQL 9.2
    postgresql-9.2-pgpool2 - connection pool server and replication proxy for PostgreSQL - modules
    postgresql-9.2-pgq3 - Skype tools for PostgreSQL replication, londiste and PGQ
    postgresql-9.2-pgstatplans - pg_stat_plans is a variant of the standard Postgres contrib module
    postgresql-9.2-pllua - Lua procedural language for PostgreSQL 9.2
    postgresql-9.2-plproxy - database partitioning system for PostgreSQL 9.2
    postgresql-9.2-plr - Procedural language interface between PostgreSQL and R
    postgresql-9.2-plsh - PL/sh procedural language for PostgreSQL 9.2
    postgresql-9.2-plv8 - Procedural language interface between PostgreSQL and JavaScript
    postgresql-9.2-postgis-2.0 - Geographic objects support for PostgreSQL 9.2
    postgresql-9.2-postgis-2.0-scripts - PostGIS 2.0 for PostgreSQL 9.2 -- scripts -- dummy package
    postgresql-9.2-postgis-2.1 - Geographic objects support for PostgreSQL 9.2
    postgresql-9.2-postgis-2.1-scripts - PostGIS for PostgreSQL 9.2 -- scripts -- dummy package
    postgresql-9.2-postgis-scripts - Geographic objects support for PostgreSQL 9.2 -- scripts
    postgresql-9.2-prefix - Prefix Range module for PostgreSQL
    postgresql-9.2-repmgr - replication manager support modules for PostgreSQL 9.2
    postgresql-9.2-slony1-2 - replication system for PostgreSQL: PostgreSQL 9.2 server plug-in
    postgresql-client-9.2 - front-end programs for PostgreSQL 9.2
    postgresql-contrib-9.2 - additional facilities for PostgreSQL
    postgresql-doc-9.2 - documentation for the PostgreSQL database management system
    postgresql-plperl-9.2 - PL/Perl procedural language for PostgreSQL 9.2
    postgresql-plpython-9.2 - PL/Python procedural language for PostgreSQL 9.2
    postgresql-plpython3-9.2 - PL/Python 3 procedural language for PostgreSQL 9.2
    postgresql-pltcl-9.2 - PL/Tcl procedural language for PostgreSQL 9.2
    postgresql-server-dev-9.2 - development files for PostgreSQL 9.2 server-side programming
    skytools-modules-9.2 - PostgreSQL 9.2 modules for skytools





### インストール

    sudo apt-get install postgresql-9.2 postgresql-client-9.2 postgresql-contrib-9.2

※パフォーマンス調査関係でcontribパッケージが必要なので入れておく



