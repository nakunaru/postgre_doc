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


### 

