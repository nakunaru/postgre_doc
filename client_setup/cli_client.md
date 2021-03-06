# CLIクライアントの設定

PostgreSQLの標準CLIクライアントであるpsqlコマンドを快適に使用する設定  


### psqlの起動

参考：http://www.postgresql.jp/document/9.2/html/app-psql.html  

#### 構文

    psql -d データベース名 -U ユーザー名 -h ホスト名 -p ポート番号 -E 

ローカル接続であれば、psql -d データベース名 -U ユーザー名 だけでOK。  


+ -d データベース名
    + 省略時にはOSユーザー名がデフォルト値として使われる
+ -U ユーザー名
    + 省略時にはOSユーザー名がデフォルト値として使われる
+ -h ホスト名
    + リモートサーバに接続する場合に指定
+ -p ポート番号
    + リモートサーバに接続する場合に指定。デフォルト：5432
+ -E
    + \l や \db などのpsqlコマンドを使用すると、サーバに送信されるSQLを標準出力へ吐き出す



### .psqlrcファイル

参考：http://www.postgresql.jp/document/9.2/html/app-psql.html  


psqlコマンドを起動すると、$HOME/.psqlrc が読み込まれる。  
以下を設定しておくと扱いやすい。  

    \pset pager off         # psqlコマンドやSELECT結果の表示にページャを使用しない
    \conninfo               # 接続情報を表示する
    select current_user;    # 現在のユーザを表示
    \l                      # データベース一覧を表示
    \timing on              # コマンドの実行に要した時間を表示
    \set PROMPT1 %/:%n%x'='%#' '
        # プロンプトの変更
        # %/    現在のDB名
        # %n    現在のユーザー名
        # %x    トランザクション内なら*、外なら空白を表示
        # %#    一般ユーザーは>、管理ユーザーなら#を表示

サンプル  
[サンプルの.psql](https://github.com/nakunaru/postgre_doc/blob/master/client_setup/.psqlrc)  


### その他のメタコマンド

+ \?
    + メタコマンドのヘルプ
+ \h
    + SQLコマンドのヘルプ
+ 情報参照系
    + \conninfo
        + 接続情報を表示
    + \dt
        + 自分で作成したテーブル一覧を表示
        + \dt *キーワード* で絞込可能
        + 注意：\dT とするとテーブルではなくデータ型を表示する
    + \dtS
        + システムテーブルも含めたテーブル一覧を表示する
        + 絞込などは\dtと同様
    + \d (絞込なし)
        + 自分で作成したテーブルとビューの一覧を表示
    + \dS (絞込なし)
        + システムテーブル、システムビューを含むテーブルとビューの一覧を表示
    + \d 絞込
        + 絞込条件に一致するテーブル、ビュー、インデックス、シーケンス、外部テーブルを表示
    + \dS 絞込
        + システムのものも含む
    + \db+
        + テーブルスペースの表示
+ ファイル操作系
    + \i ファイル名
        + ファイルを読み取り実行する
    + \o ファイル名
        + 以降の結果をファイルに保存する
        + 引数なしの\oを実行することでファイル出力が停止する
    + \e ファイル名
        + ファイルの編集
        + ファイル名がなければバッファの編集
        + 初回起動時じ環境変数$PSQL_EDITORが設定されていなければ、エディタ選択のダイアログを表示
        + 選択された結果は$HOME/.selected_editorファイルに記録される
        + $HOME/.selected_editorファイルを変更することで、エディタも変更可能
        + または、.selected_editorファイルを削除し、$PSQL_EDITORを設定することで変更する
    + \w ファイル名
        + 現在のバッファをファイルに保存する
+ その他
    + \x [on | off]
        + 拡張表示を切り替える。
        + 横に長いSELECT結果を参照するのに便利


