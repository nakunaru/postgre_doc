# 分離レベルの実験

参考：
http://www.postgresql.jp/document/9.3/html/transaction-iso.html  
http://www.postgresql.jp/document/9.3/html/sql-set-transaction.html  


## サポートしている分離レベル

| 分離レベル                 | ダーティリード | 反復不能読み取り   |ファントムリード  |
|:---------------------------|:---------------|:-------------------|:-----------------|
| READ UNCOMMITED            | なし ※1       | あり               | あり             |
| READ COMMITED (デフォルト) | なし           | あり               | あり             |
| REPEATABLE READ            | 安全           | 安全               | あり             |
| SERIALIZABLE               | 安全           | 安全               | 安全             |

※1 READ UNCOMMITEDは実質READ COMMITEDと同じ動作となる


## 分離レベルによる同時実行性の実験

### サンプルテーブル用意

    ```
    DROP TABLE IF EXISTS isotab;
    
    CREATE TABLE isotab
    (no int,
     name varchar(10));
    
    INSERT INTO isotab VALUES(10, 'aaa');
    INSERT INTO isotab VALUES(20, 'bbb');
    INSERT INTO isotab VALUES(30, 'ccc');
    INSERT INTO isotab VALUES(40, 'ddd');
    INSERT INTO isotab VALUES(50, 'eee');
    INSERT INTO isotab VALUES(60, 'fff');
    INSERT INTO isotab VALUES(70, 'ggg');
    INSERT INTO isotab VALUES(80, 'hhh');
    INSERT INTO isotab VALUES(90, 'jjj');
    ```

### READ COMMITED + UPDATE

1. session A:  

    ```
    BEGIN;
    SELECT * FROM isotab;
    
    no | name
    ---+------
    10 | aaa
    20 | bbb
    30 | ccc
    40 | ddd
    50 | eee
    60 | fff
    70 | ggg
    80 | hhh
    90 | jjj
    ```

2. session B:  

    ```
    BEGIN;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    
    UPDATE isotab SET name = 'EEE' WHERE no = 50;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- 変更したセッションは変更後の値を取得
    ```

3. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <- READ COMMITEDのセッションは変更前のデータを取得
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    ```

4. session B:  

    ```
    COMMIT;
    ```

5. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- コミットされればREAD COMMITEDのセッションから変更後のデータが取得できる
    
    COMMIT;
    ```

+ READ COMMITEDはロックを取らない


### REPEATABLE READ + UPDATE

1. session A:  

    ```
    BEGIN;
    SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
    
    SELECT * FROM isotab;
    ```

2. session B:  

    ```
    BEGIN;
    UPDATE isotab SET name = 'EEE' WHERE no = 50;
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- 変更後の値
    ```

3. session A:  

    ```
    SELECT * FROM isotab;

     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <- 変更前の値
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    ```

4. session B:  

    ```
    COMMIT;
    ```

5. session A:  

    ```
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     50 | eee   <-変更前の値
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
    
    COMMIT;
    
    SELECT * FROM isotab;
    
     no | name
    ----+------
     10 | aaa
     20 | bbb
     30 | ccc
     40 | ddd
     60 | fff
     70 | ggg
     80 | hhh
     90 | jjj
     50 | EEE   <- 変更後の値
    ```

※REPEATABLE READのSELECT文は、トランザクション開始時点のコミット済みスナップショットを見続ける  
※REPEATABLE READのUPDATEおよびDELETEは、トランザクション開始時点のコミット済みスナップショットに対し更新を試みる。  
　更新時に、他のセッションによる更新でロックされていた場合、ロック解除を待ち、ロールバックされた場合は更新を続行、コミットされた場合は
　「同時更新のため直列化アクセスができませんでした」  
　エラーを出力し、更新を失敗させる。  

参考：http://www.postgresql.jp/document/9.3/html/transaction-iso.html  



