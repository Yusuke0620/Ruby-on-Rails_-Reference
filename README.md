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
rails generate model モデル名 [属性名:データ型 ...]
```
ポストの例
```
rails g model Post content:text
```
例えば、Userという名前のモデルを生成し、そのモデルにnameとemailという属性を持たせる場合、次のようになる
```
rails g model User name:string email:string
```

<br>
<br>

 - ## 2. ファイルが作成場所確認
アプリ名/　➡　db/　➡　migrate/　➡　20240409120941_create_posts.rb(マイグレーションファイル)

<br>
<br>

 - ## 3. テーブルを用意
データベースに変更を反映させるためのコマンドで、3つのカラムが自動で生成される<br>
＜注意＞マイグレーションファイルを作成した場合は必ず「rails db:migrate」を実行する
```
rails db:migrate
```

<br>
<br>

 - ## 4. モデル
「rails g model」コマンドによってPostモデルが定義されたファイル「post.rb」が、app/modelsフォルダの中に作成されている。
ファイルの中には、`ApplicationRecordクラス`を継承する`Postクラス`が定義されている。
このようにApplicationRecordを継承したクラスをモデルと呼ぶ。<br>
```ruby
class Post < ApplicationRecord
  
end
```

<br>
<br>

 - ## 5. rails consoleを使う
Rubyのコードを手軽に実行できる環境のことで、データベースに追加していく
<br>
終了したい時は`quit`
```
rails console
```

<br>
<br>

 - ## 6. テーブルにデータを保存・newメソッド・saveメソッド
データを追加する方法は<br>
① new メソッドで Post モデルのインスタンスを作成<br>
② posts テーブルに保存
```
# モデルのインスタンスを作成
user = User.new

# 属性を指定してインスタンスを作成
user = User.new(name: "John", email: "john@example.com")

# インスタンスの保存
user.save
```

<br>
<br>
