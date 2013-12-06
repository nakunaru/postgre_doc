# ネットワーク設定

PostgreSQLはデフォルトではローカルでのみ接続可能な状態となっている。  
リモートから接続する必要がある場合には、インストール後に設定を行う。  

参考：http://rina.jpn.ph/~rance/linux/postgresql/connect.html  


### postgresql.conf の設定

    sudo vi /etc/postgresql/9.2/main/postgresql.conf

    # listen_addresses = '*'        ←ここのコメントをはずす
    　・・・
    # port = 5432                   ←ここのコメントをはずす


### 接続可能ホストの制限をはずす

    sudo vi /etc/postgresql/9.2/main/pg_aba.conf

    host    all             all             ::1/128                 md5
    # Allow replication connections from localhost, by a user with the
    # replication privilege.
    #local   replication     postgres                                peer
    #host    replication     postgres        127.0.0.1/32            md5
    #host    replication     postgres        ::1/128                 md5
    host     all             all             <IPアドレス>/32         md5    ←こんなんを追加


### PostgreSQLの再起動

    sudo /etc/init.d/postgresql restart


### その他

/etc/hosts.allowや/etc/hosts.deny、denyhostsなどでアクセス制御を行っている場合はそこでもリモートクライアントからのアクセスを許可しておくこと。





