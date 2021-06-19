---
title: "【作業Log】Rails6系で本の管理APIを作ってみる①" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Ruby on Rails"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: false # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

最近、お仕事でRailsを使うようになったので、そのお勉強も兼ねて本の管理をするためのAPIを作ってみようと思います。

初心者なので、やり方間違ってたりコードがおかしかったりする可能性があります。
その際は、ご指摘いただけるととても嬉しいです。

# 今回の内容
今回は、プロジェクトの作成からAPIのベースを簡単に作って、Postmanで叩いてみるところまでやっていきます。

作業ログのような形で書くので、詳しい説明はあまり入ってません。

作っていく中で理解が深まってきたら、まとめ直すか、加筆していく予定です。

それではやっていきます。

## 開発環境

```sh
$ sw_vers
ProductName:    macOS
ProductVersion: 11.3.1
BuildVersion:   20E241
$ ruby --version
ruby 2.6.3p62 (2019-04-16 revision 67580) [universal.arm64e-darwin20]
$ rails --version
Rails 6.1.3.2
```

## APIモードでプロジェクトを作成
```sh
# プロジェクト用のディレクトリを作成
$ mkdir book_manage_api
$ cd book_manage_api
# APIプロジェクトをカレントディレクトリに作成
$ rails new . --api
# 作成されたディレクトリたち（1階層分のみ）
$ tree -L 1 
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

## 本のモデルとコントローラを作成する
```sh
# modelを作成
$ rails g model book title:string author:string
# controllerを作成
$ rails g controller books
```

## ルーティングを変更する
```ruby : routes.rb
Rails.application.routes.draw do
  namespace 'api' do
    namespace 'v1' do
      resources :books
    end
  end
end
```

## ディレクトリ構成を変更する
```sh
$ mkdir -p app/controller/api/v1
$ mv app/controller/books_controller.rb app/controller/api/v1/books_controller.rb
```

## コントローラを修正する
### 1. ルーティングに合わせる
```ruby : books_controller.rb
module Api
  module V1
    class BooksController < ApplicationController
    end
  end
end
```

### 2. メソッドを追加する
今回は、下記のことができるようにする

* 本の情報の一覧を取得（index）
* 特定の本の情報を取得（show）
* 本の情報を作成（create）
* 特定の本の情報を更新（update）
* 特定の本の情報を削除（destroy）

```ruby : books_controller.rb
module Api
  module V1
    class BooksController < ApplicationController
      before_action :set_book, only: %i[show update destroy]

      def index
        books = Book.order(created_at: :desc)
        render json: { status: :ok, data: books }
      end

      def show
        render json: { status: :ok, data: @book }
      end

      def create
        book = Book.new(book_params)
        if book.save
          render json: { status: :ok, data: book }
        else
          render json: { status: :bad_request, data: book.errors }
        end
      end

      def update
        if @book.update(book_params)
          render json: { status: :ok, data: @book }
        else
          render json: { status: :ok, data: @book.errors }
        end
      end

      def destroy
        @book.destroy
        render json: { status: :ok, data: @book }
      end

      private

      def set_book
        @book = Book.find(params[:id])
      end

      def book_params
        params.require(:book).permit(:title, :author)
      end
    end
  end
end
```

### 3. 確認してみる
Postmanというツールを使ってこのAPIを叩いてみます。

その前に、開発サーバを立ち上げるのをお忘れなく。
```sh
$ rails s
```

こんな感じで、ちゃんと値が返ってきました。

![](https://storage.googleapis.com/zenn-user-upload/3efd910f42ad7c9b4e3085b3.png)

# まとめ
こんなに簡単にAPI体験できるのは楽しい。

次は、RSpecを導入して、テストコードを書く練習をば！
