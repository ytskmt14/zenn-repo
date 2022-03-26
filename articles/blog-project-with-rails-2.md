---
title: "Railsでブログをつくりたい〜管理画面作成編〜" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["RubyOnRails", "Rspec"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

今回は、管理画面を作っていきたいと思います。

[ActiveAdmin](https://activeadmin.info/index.html)という便利なgemがあるので、それを使っていこうと思います。

# この記事のゴール
* ActiveAdminを使って管理画面を作る
* モデルを作って管理画面で表示する

# 作業環境について
```sh:terminal
→ sw_vers
ProductName:    macOS
ProductVersion: 11.6
BuildVersion:   20G165
→ ruby -v
ruby 3.0.2p107 (2021-07-07 revision 0db68f0233) [arm64-darwin20]
→ rails -v
Rails 7.0.2.3
```

最近RubyMineのライセンスを購入したので、エディタはRubyMineを利用しています。

# 作業ログ

## ActiveAdminを使って管理画面を作る

### Gemfileの修正
↓が参考
https://activeadmin.info/0-installation.html

```diff ruby:Gemfile
  # （略）
+ gem 'activeadmin'
+ gem 'devise'
+ gem 'cancancan'
+ gem 'draper'
+ gem 'pundit'
  # （略）
```

### ActiveAdminのセットアップ
```sh:terminal
# active adminのセットアップ
→ bin/rails g active_admin:install
      invoke  devise
    generate    devise:install
      create    config/initializers/devise.rb
      create    config/locales/devise.en.yml
  ===============================================================================

Depending on your application's configuration some manual setup may be required:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

     * Required for all applications. *

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

     * Not required for API-only Applications *

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

     * Not required for API-only Applications *

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

     * Not required *

===============================================================================
      invoke    active_record
      create      db/migrate/20220325162703_devise_create_admin_users.rb
      create      app/models/admin_user.rb
      invoke      rspec
      create        spec/models/admin_user_spec.rb
      invoke        factory_bot
      create          spec/factories/admin_users.rb
      insert      app/models/admin_user.rb
       route    devise_for :admin_users
        gsub    app/models/admin_user.rb
        gsub    config/routes.rb
      append    db/seeds.rb
      create  config/initializers/active_admin.rb
      create  app/admin
      create  app/admin/dashboard.rb
      create  app/admin/admin_users.rb
      insert  config/routes.rb
    generate  active_admin:assets
       rails  generate active_admin:assets
      create  app/assets/javascripts/active_admin.js
      create  app/assets/stylesheets/active_admin.scss
      create  db/migrate/20220325162705_create_active_admin_comments.rb

# マイグレーションの実行
→ bin/rails db:migrate
== 20220325162703 DeviseCreateAdminUsers: migrating ===========================
-- create_table(:admin_users)
   -> 0.0008s
-- add_index(:admin_users, :email, {:unique=>true})
   -> 0.0003s
-- add_index(:admin_users, :reset_password_token, {:unique=>true})
   -> 0.0002s
== 20220325162703 DeviseCreateAdminUsers: migrated (0.0014s) ==================

== 20220325162705 CreateActiveAdminComments: migrating ========================
-- create_table(:active_admin_comments)
   -> 0.0012s
-- add_index(:active_admin_comments, [:namespace])
   -> 0.0002s
== 20220325162705 CreateActiveAdminComments: migrated (0.0015s) ===============

# シードデータの投入
# 管理画面にログインするためのユーザが作成される
→ bin/rails db:seed
```

これでOK。

、、と思いきや、サーバを起動して管理画面を開こうとするとエラーが。。

```sh:terminal
→ bin/rails s
```

[http://127.0.0.1:3000/admin](http://127.0.0.1:3000/admin/login)にアクセスします。

![](https://storage.googleapis.com/zenn-user-upload/84ffde5a1fcd-20220326.png)

どうやらRails6以降の場合は、デフォルトでwebpacker使ってるので、その設定が必要らしい。

最初にインストールするときにオプションつけてあげておけばよかったみたい。

```sh:terminal
→ rails g active_admin:install --use_webpacker
```

というわけで入れていきます。

```diff ruby:config/initializers/active_admin.rb
  # (略)
  # == Webpacker
  #
  # By default, Active Admin uses Sprocket's asset pipeline.
  # You can switch to using Webpacker here.
  #
- # config.use_webpacker = true
+ config.use_webpacker = true
  # (略)
```

```sh:terminal
→ bin/rails g active_admin:webpacker
      create  app/javascript/packs/active_admin.js
      create  app/javascript/stylesheets/active_admin.scss
      create  app/javascript/packs/active_admin/print.scss
      create  config/webpack/plugins/jquery.js
The file /Users/moc/RubymineProjects/blog/config/webpack/environment.js does not appear to exist
```

なんかエラーっぽいのが出てるけど、とりあえず再チャレンジ！

![](https://storage.googleapis.com/zenn-user-upload/e5ed1720efb7-20220326.png)

エラーなったけど、メッセージが変わりました。

なんかみたことあるなと思ったら、そもそもこのRailsプロジェクトにwebpackerがインストールされていませんでした。。。

[こちら](https://railsguides.jp/webpacker.html)を参考にインストールします。

```diff ruby:Gemfile
  #（略）
+ gem 'webpacker'
  #（略）
```

```sh:terminal
→ bundle install
# webpackerのセットアップ
→ bin/rails webpacker:install
# 再チャレンジ
→ bin/rails g active_admin:webpacker
```

今度こそいけると信じて[http://127.0.0.1:3000/admin](http://127.0.0.1:3000/admin/login)にアクセスします。

![](https://storage.googleapis.com/zenn-user-upload/4879844fdef8-20220326.png)

いけました！！

seedファイルでデータを投入したときに、サンプルユーザが作成されているので、それを使ってログインします。

|Email|Password|
|---|---|
|admin@example.com|password|


![](https://storage.googleapis.com/zenn-user-upload/27e38a94abb6-20220326.png)

ログインできました！

## モデルを作って管理画面で表示する
記事のデータを扱うためのモデルを作ってみます。

作るモデルはとりあえずこんな感じです。

**Postモデル**
|フィールド名|型|
|---|---|
|title|string|
|body|text|

```sh:terminal
# モデルの作成
→ bin/rails g model Post title:string body:text

# マイグレーションを流す
→ bin/rails db:migrate

# active adminに登録
→ bin/rails g active_admin:resource Post
```

たったこれだけです。

それでは、管理画面をもう一度みてみます。

![](https://storage.googleapis.com/zenn-user-upload/02bd7a5f7356-20220326.png)

ちょっとわかりにくいですが、メニューに`Posts`というものが追加されました。

メニューを選択すると、一覧画面が表示されて、ここから追加や編集、削除などができます。

![](https://storage.googleapis.com/zenn-user-upload/47f7ccbf1b10-20220326.png)


# まとめ
今回は、管理画面を作って、モデルを一つ追加してみました！

gemを使うと一から作るより簡単にいろいろできるようになるので、便利ですね。

次回は、`Post`モデルに制約をつけるのと、画面に表示するところあたりをやってみようかなと思います。
