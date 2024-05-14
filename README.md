# 目次

- [テーブル作成の準備](#table-creation-preparation)
- [日付・時刻を日本時間に変換](#Formatting-date-and-time)
- [エラーメッセージ表示＜.errors.full_messages＞](#errors_full_messages)
- [簡単なメッセージを画面に表示＜flash＞](#flash)
- [ユーザー画像を表示](#display-user-images)
- [配列の要素数・テーブルのデータ数を取得＜count＞](#count-method)
- [Renderでデプロイ＞](#render-deploy)

# Renderでデプロイ
<a name="render-deploy"></a>

<br>


 - ## 1. sqlite3を切り取りPostgreSQL設定

1）12＆13行目のsqlite3を切り取り、55行目から始まる*group :development do*の中にペースト
```ruby
# Use sqlite3 as the database for Active Record
gem "sqlite3", "~> 1.4"
```
<br>

 - ## 2. PostgreSQLのGemの設定
2）一番下にスクロールし次のように記述
※これで本番環境でのみPGという名のGemがインストールされる、pgはRailsからPostgreSQLを操作する時に使うGem
```ruby
group :production do
  gem 'pg', '~> 1.2.3'
end
```
<br>
3）開発環境で使うGemのインストール※ターミナルに入力

```
bundle install --without production
```
確認方法
```
less .bundle/cmonfig
```
もしくはWindowsで使えない場合
```
type .bundle\config
```

<br>
<br>

 - ## 3. 
4）config配下のdatabase.ymlファイルを開く
<br><br>
5）25行目のsqliteの行を削除し、次を入力
※ymlはインデントが重要な役割を果たすので、必ずTABキーで設定する
``` ruby
  adapter:  postgresql # PostgreSQLのデータベースに接続するという設定
  encoding: unicode    # 日本語でも扱える文字コード
  url:      <%= ENV['DATABASE_URL'] %> # データベースのURLは環境変巣から取得するように設定
```
<br>
6）config ➡ environments ➡ production.rbを開く<br>
※Railsの本番環境用の設定ができる
<br><br>
7）31行目のconfig.assets.compileをtrueにする
※assets.compileとはCSSやJavaScriptを連結して高速化すること

```ruby
config.assets.compile = true
```

<br>
<br>


8）25行目の最後に「スペース|| ENV['RENDER'].resent?」
```ruby
config.public_file_server.enabled = ENV["RENDER"].present?
```

<br>

9）ルーティングに記述
```ruby
  root 'questions#index'
  resources :questions do
    resources :answers
    end
  end
```

<br>


10）config ➡ puma.rbを開く
<br>
11）33行目付近の# workers ENV.fetch("WEB_CONCURRENCY") { 2 }のコメントアウトを削除してこの行を有効にする
<br>
12）{ 2 }　を  { 4 }に変更する
<br>
13）40行目付近の# preload_app!ののコメントアウトを削除してこの行を有効にする

<br>
<br>

 - ## 4.ビルドするためのscriptを指定する

14）bin ➡ 新規ファイル作成 ➡ 「render-build.sh」
13）https://gist.github.com/ynakayu/a48392adc6bce5d9c75064ade6b314a6
※テキストをコピーする
13）Git Bash ➡ 該当アプリフォルダ ➡ 作成スクリプト全てのユーザーに実効権限を与えるコマンド入力
```
chmod a+x bin/render-build.sh
```
13）

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

<br>
<br>

# 簡単なメッセージを画面に表示＜flash＞
<a name="flash"></a>

<br>
一度のHTTPリクエストでのみ有効な一時的なメッセージを保存するためのRailsの仕組み<br>
通常、ユーザーに対する成功メッセージやエラーメッセージを表示する際に使用されます。<br>
一度表示されると、その後のリクエストではメッセージがクリアされます<br>



```ruby
# コントローラーのアクション内で成功メッセージを設定
def create
  @post = Post.new(post_params)
  if @post.save
    flash[:success] = "Post successfully created!"
    redirect_to @post
  else
    flash[:error] = "Failed to create post."
    render :new
  end
end
```

```ruby
# ビュー内でflashメッセージを表示
<% if flash[:success] %>
  <div class="alert alert-success">
    <%= flash[:success] %>
  </div>
<% end %>

<% if flash[:error] %>
  <div class="alert alert-danger">
    <%= flash[:error] %>
  </div>
<% end %>




- [ユーザー画像を表示](#display-user-images)

```
<br>
<br>
<br>

# ユーザー画像を表示
<a name="display-user-images"></a>


 - ## 〇. 画像選択ボタン
```ruby
<h1>画像</h1>
<input name="image" type="file"> # 「type="file"」で画像選択できる
```

![screenshot_22](https://github.com/Yusuke0620/Ruby-on-Rails_-Reference/assets/134079967/c7dd6073-a786-4f59-88ab-6ad3acfd6907)

<br>

 - ## 〇. 画像の送信＜multipart : true＞
```ruby
<%= form_tag("...", {multipart: true}) do %> # 画像を送信したい時などにつける必要がある
```

<br>

以上がファイルをアップロードする準備
ここからは
①ファイル名をデータベースに保存
②publicフォルダに画像ファイルを保存

<br>

 - ## 〇. データベースにファイル名保存
```ruby
def アクション名
 if params[:image] # 画像が送信されているか判定する
  @変数 = モデル名からid取得
  @変数.画像カラム名 = 
  image = params[:image] # 送信されたファイルが入っている
  File.binwrite("public/#{@変数.画像カラム名}", image.read) # ファイルの場所
 end
end
```
※`if params[:image]`は画像データが送信された時だけ画像を更新するように判定する処理<br>
※画像データは特殊なテキストファイルなので`File.binwrite`を使う<br>
※`readメソッド`を用いることで画像データを取得できる


<br>
<br>
<br>

# 配列の要素数・テーブルのデータ数を取得＜count＞
<a name="count-method"></a>

likeテーブルの全データ数を取得する方法:
```ruby
Like.all.count
```

<br>

likeテーブルで、特定の条件（例えば write_id が2のレコード）のデータ数を取得する方法:
```ruby
Like.where(write_id: 2).count
```

ここで使用される .where メソッドは、指定された条件に合致するレコードをフィルタリングし、その結果に対して .count を適用することで、該当するレコードの数を取得します。
