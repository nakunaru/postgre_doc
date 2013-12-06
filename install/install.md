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


### インストール

    sudo apt-get install postgresql-9.2 postgresql-client-9.2 postgresql-contrib-9.2

※パフォーマンス調査関係でcontribパッケージが必要なので入れておく



