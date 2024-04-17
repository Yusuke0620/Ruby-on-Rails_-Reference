# 目次

- [テーブル作成の準備](#table-creation-preparation)
- [日付・時刻を日本時間に変換](#Formatting-date-and-time)
- [エラーメッセージ表示＜.errors.full_messages＞](#errors_full_messages)

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

# 日付・時刻を日本時間に変換
<a name="Formatting-date-and-time"></a>

<br>
日本時間に変換するには、Active Supportライブラリのin_time_zoneメソッドを使用。<br>
created_at 属性の日時が日本時間に変換され、指定された形式で表示されます。<br>
同様に、updated_at属性についても同じ方法で変換できます。<br>
in_time_zone メソッドには、任意のタイムゾーンを指定できます。ここでは "Tokyo" を指定していますが、他のタイムゾーンを指定することもできます。<br>

```ruby
post.created_at.in_time_zone("Tokyo").strftime("%Y/%m/%d %H:%M")
```
![screenshot_01](https://github.com/Yusuke0620/Ruby-on-Rails_-Reference/assets/134079967/e9973f18-8fcb-4f24-988a-d8c4ed4f3aa5)


<br>
<br>

# エラーメッセージ表示＜.errors.full_messages＞
<a name="errors_full_messages"></a>

<br>
SNSなどの文章投稿機能で文字数オーバーや空投稿をした時に<br>
Railsではエラーメッセージを表示することができます。<br>



```ruby
post = Post.new            # 空の投稿
post.save                  # 保存
post.errors.full_messages  # エラーメッセージのリストを取得

```
