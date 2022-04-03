---
title: "Railsでブログをつくりたい〜プロジェクト作成編〜" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["RubyOnRails", "Rspec"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

railsをお仕事で使い始めて、9ヶ月くらいになります。

そろそろ自分でもサービスを作ってみたい！

ポートフォリオ的にブログ作ってみるか！

というノリで始めていこうと思います。

果たして無事リリースまで辿り着けるのか、、、

というわけで今回は、プロジェクト作成編になります。

# この記事でわかること
* Rails7 x Ruby3 でのプロジェクト作成方法
* Rspec&factory_botの導入方法
* Git/Githubで管理する

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

## Railsのプロジェクトを作成する
今回は、いろいろオプションを指定せずにプロジェクトを作ってみようと思います。

```sh:terminal
# RubyMineで先にプロジェクトを作成済み
→ pwd
/Users/moc/RubymineProjects/blog
# 今いるところをアプリケーションルートにしたいので、
# カレントディレクトリにアプリケーションを作成
→ rails new .
```

ちなみに、オプションはいっぱいあります。
詳しくは[こちら](https://railsdoc.com/rails)。

rspecを導入するので、`-T`オプションくらいつけておけばよかったと少し後悔しています。

`bundle install`とかが走るので、少し待機すると、プロジェクト生成完了！

こんな感じのディレクトリ構造になりました。

```sh:terminal
→ tree -L 1 .
.
├── Gemfile
├── Gemfile.lock
├── README.md
├── Rakefile
├── app
├── bin
├── config
├── config.ru
├── db
├── lib
├── log
├── public
├── storage
├── test
├── tmp
└── vendor
```

各ディレクトリやファイルがどういった役割をしているかは、Railsガイドに概要が書いてあります。

[こちら](https://railsguides.jp/getting_started.html#%E3%83%96%E3%83%AD%E3%82%B0%E3%82%A2%E3%83%97%E3%83%AA%E3%82%B1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B)を参照いただければ良いかなと思います。


## 画面を表示してみる
プロジェクトを作成したことですし、とりあえず画面が見れること確認してみます。

```sh:terminal
# 開発サーバを起動
→ bin/rails s
=> Booting Puma
=> Rails 7.0.2.3 application starting in development
=> Run `bin/rails server --help` for more startup options
Puma starting in single mode...
* Puma version: 5.6.2 (ruby 3.0.2-p107) ("Birdie's Version")
*  Min threads: 5
*  Max threads: 5
*  Environment: development
*          PID: 46992
* Listening on http://127.0.0.1:3000
* Listening on http://[::1]:3000
Use Ctrl-C to stop
```

起動ができたら、http://127.0.0.1:3000にアクセスします。

![](https://storage.googleapis.com/zenn-user-upload/cc0d5456deef-20220324.png)

完璧ですね、めっちゃ簡単。

表示の確認ができたので、`Ctrl-C`でサーバを止めておきます。

## Rspec&factory_botの導入方法
Rspecとfactory_botについての説明は、わかりやすい記事がたくさんあるので、割愛します。

Rspec:テスト用のライブラリ

factory_bot:テストデータを作るライブラリ

今はこれくらいのイメージができていればOKかなと。

Railsでは、デフォルトで`Minitest`というライブラリが組み込まれています。

これで進めてもよいのですが、お仕事でRspecを書いていて、もっと使いこなせるようになりたいので、

今回はRspecを導入していこうと思います。

参考にするのは、下記のサイトになります。

https://rspec.info/documentation/5.0/rspec-rails/

https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md

### Gemfileの修正
まずは、必要なライブラリをインストールすることから始めます。
```diff ruby:Gemfile
 group :development, :test do
   # See https://guides.rubyonrails.org/debugging_rails_applications.html#debugging-with-the-debug-gem
   gem "debug", platforms: %i[ mri mingw x64_mingw ]
+  gem "factory_bot_rails"
+  gem 'rspec-rails', '~> 5.0.0'
 end
```

```sh:terminal
→ bundle install
Fetching gem metadata from https://rubygems.org/...........
Resolving dependencies...
Using rake 13.0.6
Using concurrent-ruby 1.1.10
Using websocket-extensions 0.1.5
Using marcel 1.0.2
Using erubi 1.10.0
Using builder 3.2.4
Using mini_mime 1.1.2
Using timeout 0.2.0
Using strscan 3.0.1
Using public_suffix 4.0.6
Using minitest 5.15.0
Using bindex 0.8.1
Using msgpack 1.4.5
Using bundler 2.2.22
Using matrix 0.4.2
Using regexp_parser 2.2.1
Using childprocess 4.1.0
Using io-console 0.5.11
Using racc 1.6.0
Using thor 1.2.1
Using zeitwerk 2.5.4
Using io-wait 0.2.1
Using rack 2.2.3
Using nio4r 2.5.8
Using rubyzip 2.3.2
Using sqlite3 1.4.2
Using i18n 1.10.0
Using tzinfo 2.0.4
Using websocket-driver 0.7.5
Using mail 2.7.1
Using addressable 2.8.0
Using bootsnap 1.11.1
Using reline 0.3.1
Using nokogiri 1.13.3 (arm64-darwin)
Using net-protocol 0.1.2
Using activesupport 7.0.2.3
Using rack-test 1.1.0
Using xpath 3.2.0
Using irb 1.4.1
Using sprockets 4.0.3
Using digest 3.1.0
Using crass 1.0.6
Using method_source 1.0.0
Using rexml 3.2.5
Fetching diff-lcs 1.5.0
Using rails-dom-testing 2.0.3
Using loofah 2.15.0
Using globalid 1.0.0
Using activemodel 7.0.2.3
Using net-imap 0.2.3
Using net-pop 0.1.1
Using net-smtp 0.3.1
Using capybara 3.36.0
Using debug 1.4.0
Fetching rspec-support 3.11.0
Using puma 5.6.2
Using rails-html-sanitizer 1.4.2
Using activejob 7.0.2.3
Using actionview 7.0.2.3
Using activerecord 7.0.2.3
Using actionpack 7.0.2.3
Using jbuilder 2.11.5
Fetching factory_bot 6.2.1
Using selenium-webdriver 4.1.0
Using actioncable 7.0.2.3
Using activestorage 7.0.2.3
Using actionmailer 7.0.2.3
Using railties 7.0.2.3
Using sprockets-rails 3.4.2
Using actionmailbox 7.0.2.3
Using actiontext 7.0.2.3
Using importmap-rails 1.0.3
Using stimulus-rails 1.0.4
Using turbo-rails 1.0.1
Using web-console 4.2.0
Using webdrivers 5.0.0
Using rails 7.0.2.3
Installing rspec-support 3.11.0
Installing diff-lcs 1.5.0
Installing factory_bot 6.2.1
Fetching rspec-core 3.11.0
Fetching rspec-mocks 3.11.0
Fetching rspec-expectations 3.11.0
Fetching factory_bot_rails 6.2.0
Installing factory_bot_rails 6.2.0
Installing rspec-core 3.11.0
Installing rspec-mocks 3.11.0
Installing rspec-expectations 3.11.0
Fetching rspec-rails 5.0.3
Installing rspec-rails 5.0.3
Bundle complete! 17 Gemfile dependencies, 82 gems now installed.
Bundled gems are installed into `./vendor/bundle`
```

### rspecのセットアップ
```sh:terminal
→ bin/rails g rspec:install
      create  .rspec
      create  spec
      create  spec/spec_helper.rb
      create  spec/rails_helper.rb
```

```diff rb:spec/rails_helper.rb
  # (略)
- # Dir[Rails.root.join('spec', 'support', '**', '*.rb')].sort.each { |f| require f }
+ Dir[Rails.root.join('spec', 'support', '**', '*.rb')].sort.each { |f| require f }
  #（略）
```

コメントアウトを外すだけです。

これによって、`spec/support`ディレクトリ配下に作ったヘルパーたちを自由に使えるようになります。

今のところ使うかわからないので、必要になったときに外すでもいいかもしれません。

### factory_botのセットアップ
```diff rb:spec/rails_helper.rb
 # (略)
 RSpec.configure do |config|
+  config.include FactoryBot::Syntax::Methods

   # Remove this line if you're not using ActiveRecord or ActiveRecord fixtures
   config.fixture_path = "#{::Rails.root}/spec/fixtures"
   #（略）
```

この設定をすることで、テストコードを書くのが少し楽になります。

解説してある記事はいっぱいあるので、省略しますが、rubydocのページを貼っておきます。

https://www.rubydoc.info/gems/factory_bot/FactoryBot/Syntax/Methods

### testディレクトリの削除
Rspecの導入ができたので、minitest用のディレクトリである`test`ディレクトリを削除しておきます。

```bash:terminal
→ rm -rf test
```

## Git/Githubで管理する
Gitを使ってバージョン管理をして、それらをGithubにあげるところまでやります。

まずは、今までのソースコードをコミットまでしておきます。

```bash:terminal
→ git add .
→ git commit -m "create project and add rspec, factory_bot"
→ git log --oneline
37673ac (HEAD -> main) create project and add rspec, factory_bot
```

Github上にリポジトリを作っておきます。

名前はなんでもいいですが、今回は`moc-blog`にしておきます。

![](https://storage.googleapis.com/zenn-user-upload/bb8e0d915bff-20220325.png)

`…or push an existing repository from the command line`のところに既存のリポジトリをプッシュする手順が書いてあるので、その通り実施していきます。

![](https://storage.googleapis.com/zenn-user-upload/f4300604732a-20220325.png)

```bash:terminal
→ git remote add origin https://github.com/ytskmt14/moc-blog.git
→ git branch -M main
→ git push -u origin main
Enumerating objects: 6232, done.
Counting objects: 100% (6232/6232), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5249/5249), done.
Writing objects: 100% (6232/6232), 21.62 MiB | 6.03 MiB/s, done.
Total 6232 (delta 587), reused 6232 (delta 587), pack-reused 0
remote: Resolving deltas: 100% (587/587), done.
To https://github.com/ytskmt14/moc-blog.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'.
```

これで完了です。

こんな感じでリポジトリができました。

https://github.com/ytskmt14/moc-blog

# まとめ
今回は、個人開発の第一歩として、Railsプロジェクトの作成・Rspecの導入・Githubへのpushまでやってみました。

これから少しずつ形にしていこうと思います。

どんな風にしようか今からわくわくです。
