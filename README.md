# 目次

- [テーブル作成の準備](#table-creation-preparation)

<br>
<br>
<br>


# テーブル作成の準備
<a name="table-creation-preparation"></a>

<br>

 - ## 1. マイグレーションファイル作成
まずはマイグレーションファイルと呼ばれる、データベースに指示を出すファイルを作成する<br>
また、「text」は「長い文字列」を意味しており、contentカラムにどのようなデータが入るか意味している
```
rails g model Post content:text
```

<br>

 - ## 2. ファイルが作成場所確認
アプリ名/　➡　db/　➡　migrate/　➡　20240409120941_create_posts.rb(マイグレーションファイル)

<br>

 - ## 3. テーブルを用意
データベースに変更を反映させるためのコマンドで、3つのカラムが自動で生成される<br>
＜注意＞マイグレーションファイルを作成した場合は必ず「rails db:migrate」を実行する
```
rails db:migrate
```

<br>


