---
title: "Railsのプレイグラウンド的なやつを作ってるお話~環境構築編~" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["RubyOnRails"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

普段Railsでお仕事をしているのですが、記事を書くときにいろいろ試す環境を作りたいなと思い、作ってます。
何かちょっと試したいなという方がいればぜひご利用ください。

こちらのリポジトリになります。
https://github.com/ytskmt14/rails-playground

:::message
本記事執筆時点では、諸々Gemを導入するところまでになります。
今後、いろいろお試しできるように機能追加していく予定です。
:::

# 動作確認環境
```
→ sw_vers
ProductName:    macOS
ProductVersion: 11.6
BuildVersion:   20G165

→ docker --version
Docker version 20.10.21, build baeda1f

→ docker-compose --version
Docker Compose version v2.12.2
```

# 参考
## 環境構築
今回は環境構築でできるだけ楽したかったので、他の方の記事を参考に構築しました。
https://zenn.dev/wakkunn/articles/33c84147608078


# この記事のゴール
1. Railsのプレイグラウンド的な環境が完成

# 目次
1. 環境構築
2. 各種gemの導入
   - Rspec
   - rubocop
   - devise
   - cancancan
   - ActiveAdmin
   - annotate

# 環境構築
こちらを参考に作成しています。
https://zenn.dev/wakkunn/articles/33c84147608078

```
→ docker-compose exec web ruby --version
ruby 3.1.2p20 (2022-04-12 revision 4491bb740a) [aarch64-linux]

→ docker-compose exec web bin/rails --version
Rails 7.0.4.2

→ docker-compose exec db mysql --version
mysql  Ver 14.14 Distrib 5.7.39, for Linux (x86_64) using  EditLine wrapper
```

# 各種gemの導入
:::message
以降は、コンテナを起動した状態で実施してください。
:::

:::message
コードブロック内に出てくる「→」はプロンプトです。
コピペする際は無視してください。
:::
## Rspec
こちらを参考にさせていただきました。
https://qiita.com/tmasuyama/items/5252925a1533adaab723

```diff ruby:Gemfile
group :development, :test do
  # See https://guides.rubyonrails.org/debugging_rails_applications.html#debugging-with-the-debug-gem
  gem "debug", platforms: %i[ mri mingw x64_mingw ]
+ gem "rspec-rails"
+ gem "factory_bot_rails"
end
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose exec web bin/rails g rspec:install
      create  .rspec
      create  spec
      create  spec/spec_helper.rb
      create  spec/rails_helper.rb
```

```plain:ターミナル
→ docker-compose exec web rspec
No examples found.

Finished in 0.00037 seconds (files took 0.1551 seconds to load)
0 examples, 0 failures
```

```diff ruby:spec/rails_helper.rb
RSpec.configure do |config|
    # 中略
+   # FactoryBot configuration
+   config.include FactoryBot::Syntax::Methods
end
```

```plain:ターミナル
→ rm -rf test
```

## rubocop
こちらを参考にさせていただきました。
https://blog.shibayu36.org/entry/2023/01/23/173000
```diff ruby:Gemfile
group :development do
  # 中略
+ gem "rubocop", require: false
+ gem "rubocop-rails", require: false
+ gem "rubocop-rspec", require: false
end
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ touch .rubocop.yml
```

```diff yml:.rubocop.yml
require:
  - rubocop-rails
  - rubocop-rspec

AllCops:
  Exclude:
    - "vendor/**/*"
    - "db/**/*"
    - "config/**/*"
    - "bin/*"
    - "node_modules/**/*"
    - "Gemfile"
  NewCops: disable
```

```plain:ターミナル
→ docker-compose exec web rubocop
Inspecting 11 files
CCCCCCCCCCC

Offenses:

Rakefile:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
# Add your own tasks in files placed in lib/tasks ending in .rake,
^
Rakefile:4:18: C: [Correctable] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
require_relative "config/application"
                 ^^^^^^^^^^^^^^^^^^^^
app/channels/application_cable/channel.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
module ApplicationCable
^
app/channels/application_cable/connection.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
module ApplicationCable
^
app/controllers/application_controller.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
class ApplicationController < ActionController::Base
^
app/helpers/application_helper.rb:1:1: C: Style/Documentation: Missing top-level documentation comment for module ApplicationHelper.
module ApplicationHelper
^^^^^^^^^^^^^^^^^^^^^^^^
app/helpers/application_helper.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
module ApplicationHelper
^
app/jobs/application_job.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
class ApplicationJob < ActiveJob::Base
^
app/mailers/application_mailer.rb:1:1: C: Style/Documentation: Missing top-level documentation comment for class ApplicationMailer.
class ApplicationMailer < ActionMailer::Base
^^^^^^^^^^^^^^^^^^^^^^^
app/mailers/application_mailer.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
class ApplicationMailer < ActionMailer::Base
^
app/mailers/application_mailer.rb:2:17: C: [Correctable] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
  default from: "from@example.com"
                ^^^^^^^^^^^^^^^^^^
app/mailers/application_mailer.rb:3:10: C: [Correctable] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
  layout "mailer"
         ^^^^^^^^
app/models/application_record.rb:1:1: C: Style/Documentation: Missing top-level documentation comment for class ApplicationRecord.
class ApplicationRecord < ActiveRecord::Base
^^^^^^^^^^^^^^^^^^^^^^^
app/models/application_record.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
class ApplicationRecord < ActiveRecord::Base
^
config.ru:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
# This file is used by Rack-based servers to start the application.
^
config.ru:3:18: C: [Correctable] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
require_relative "config/environment"
                 ^^^^^^^^^^^^^^^^^^^^
spec/rails_helper.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
# This file is copied to spec/ when you run 'rails generate rspec:install'
^
spec/rails_helper.rb:6:7: C: [Correctable] Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation or special symbols.
abort("The Rails environment is running in production mode!") if Rails.env.production?
      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
spec/rails_helper.rb:34:25: C: Rails/FilePath: Prefer Rails.root.join('path/to').
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
spec/spec_helper.rb:1:1: C: [Correctable] Style/FrozenStringLiteralComment: Missing frozen string literal comment.
# This file was generated by the `rails generate rspec:install` command. Conventionally, all
^
spec/spec_helper.rb:49:1: C: [Correctable] Style/BlockComments: Do not use block comments.
=begin ...
^^^^^^

11 files inspected, 21 offenses detected, 17 offenses autocorrectable

```

## devise
https://github.com/heartcombo/devise#getting-started

```diff ruby:Gemfile
gem "devise"
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose exec web bin/rails g devise:install
Calling `DidYouMean::SPELL_CHECKERS.merge!(error_name => spell_checker)' has been deprecated. Please call `DidYouMean.correct_error(error_name, spell_checker)' instead.
      create  config/initializers/devise.rb
      create  config/locales/devise.en.yml
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
```


```diff ruby:config/environments/development.rb
  # Don't care if the mailer can't send.
  config.action_mailer.raise_delivery_errors = false

  config.action_mailer.perform_caching = false

+ config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

```plain:ターミナル
→ docker-compose exec web bin/rails g devise User
      invoke  active_record
      create    db/migrate/20230305053813_devise_create_users.rb
      create    app/models/user.rb
      invoke    rspec
      create      spec/models/user_spec.rb
      invoke      factory_bot
      create        spec/factories/users.rb
      insert    app/models/user.rb
       route  devise_for :users
```

```diff ruby:db/migrate/yyyymmddhhmmss_devise_create_users.rb
# frozen_string_literal: true

class DeviseCreateUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      ## Database authenticatable
+     t.string :last_name
+     t.string :first_name
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""
      # 以下略
```

```plain:ターミナル
→ docker-compose exec web bin/rails db:migrate
== yyyymmddhhmmss DeviseCreateUsers: migrating ================================
-- create_table(:users)
   -> 0.0176s
-- add_index(:users, :email, {:unique=>true})
   -> 0.0536s
-- add_index(:users, :reset_password_token, {:unique=>true})
   -> 0.0176s
== yyyymmddhhmmss DeviseCreateUsers: migrated (0.0890s) =======================
```

```plain:ターミナル
→ docker-compose exec web bin/rails g devise:views
      invoke  Devise::Generators::SharedViewsGenerator
      create    app/views/devise/shared
      create    app/views/devise/shared/_error_messages.html.erb
      create    app/views/devise/shared/_links.html.erb
      invoke  form_for
      create    app/views/devise/confirmations
      create    app/views/devise/confirmations/new.html.erb
      create    app/views/devise/passwords
      create    app/views/devise/passwords/edit.html.erb
      create    app/views/devise/passwords/new.html.erb
      create    app/views/devise/registrations
      create    app/views/devise/registrations/edit.html.erb
      create    app/views/devise/registrations/new.html.erb
      create    app/views/devise/sessions
      create    app/views/devise/sessions/new.html.erb
      create    app/views/devise/unlocks
      create    app/views/devise/unlocks/new.html.erb
      invoke  erb
      create    app/views/devise/mailer
      create    app/views/devise/mailer/confirmation_instructions.html.erb
      create    app/views/devise/mailer/email_changed.html.erb
      create    app/views/devise/mailer/password_change.html.erb
      create    app/views/devise/mailer/reset_password_instructions.html.erb
      create    app/views/devise/mailer/unlock_instructions.html.erb
```

## cancancan

```diff ruby:Gemfile
+ gem "cancancan"
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose exec web bin/rails g cancan:ability
    create  app/models/ability.rb
```

## ActiveAdmin
```diff ruby:Gemfile
+ gem "activeadmin"
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose exec web bin/rails g active_admin:install
    invoke  devise
    generate    No need to install devise, already done.
      invoke    active_record
      create      db/migrate/yyymmddhhmmss_devise_create_admin_users.rb
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
      create  db/migrate/yyymmddhhmmss_create_active_admin_comments.rb
```

```
→ docker-compose exec web bin/rails db:migrate
== yyymmddhhmmss DeviseCreateAdminUsers: migrating ===========================
-- create_table(:admin_users)
   -> 0.0275s
-- add_index(:admin_users, :email, {:unique=>true})
   -> 0.0212s
-- add_index(:admin_users, :reset_password_token, {:unique=>true})
   -> 0.0129s
== yyymmddhhmmss DeviseCreateAdminUsers: migrated (0.0619s) ==================

== yyymmddhhmmss CreateActiveAdminComments: migrating ========================
-- create_table(:active_admin_comments)
   -> 0.0304s
-- add_index(:active_admin_comments, [:namespace])
   -> 0.0164s
== yyymmddhhmmss CreateActiveAdminComments: migrated (0.0469s) ===============
```

:::details http://localhost:3000/admin/login にアクセスしたときにエラーが出る場合
次のようなエラーが出る場合の対処法
![](https://storage.googleapis.com/zenn-user-upload/9530333c65da-20230305.png)

https://qiita.com/Yu_unI1/items/15926b72934dada66ed3

```diff ruby:Gemfile
  # Use Sass to process CSS
- #gem "sassc-rails"
+ gem "sassc-rails"
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose restart
```

http://localhost:3000/admin/login にアクセス
![](https://storage.googleapis.com/zenn-user-upload/b9d6a396b098-20230305.png)
:::

## annotate

```diff ruby:Gemfile
group :development do
  # 中略
+ gem "annotate"
end
```

```plain:ターミナル
→ docker-compose exec web bundle install
```

```plain:ターミナル
→ docker-compose exec web bin/rails g annotate:install
    create  lib/tasks/auto_annotate_models.rake
```

# Gem導入中のワーニングの解消
## Rubocopのエラー対応
rubocop実行すると結構引っかかる、、、
```plain:ターミナル
→ docker-compose exec web rubocop
# 中略
21 files inspected, 93 offenses detected, 85 offenses autocorrectable
```

[safe-autocorrect](https://docs.rubocop.org/rubocop/1.46/usage/autocorrect.html#safe-autocorrect)を実行
```plain:ターミナル
→ docker-compose exec web rubocop -a
# 中略
21 files inspected, 96 offenses detected, 67 offenses corrected, 21 more offenses can be corrected with `rubocop -A`
```

29箇所残ってるので、そこを見ていきます。
内訳はこんな感じでした。
|#|Cop|message|件数|
| --- | --- | --- | --- |
|1|Style/FrozenStringLiteralComment | Missing frozen string literal comment. | 19 |
|2|Style/CommentedKeyword | Do not place comments on the same line as the end keyword.| 1 |
|3|Style/Documentation | Missing top-level documentation comment for ~ | 6 |
|4|Metrics/BlockLength | Block has too many lines. | 1 |
|5|Rails/RakeEnvironment | Include :environment task as a dependency for all Rake tasks. | 1 |
|6|Rails/FilePath | Prefer Rails.root.join('path/to'). | 1 |

と、思ったのですが、正直gemの自動生成のコードなので、rubocopのAutocorrectにできるだけ直してもらいます。
```plain:ターミナル
→ docker-compose exec web rubocop -A
# 中略
21 files inspected, 47 offenses detected, 39 offenses corrected
```

残ったのはこの8件。
|#|Cop|message|件数|
| --- | --- | --- | --- |
|1|Style/Documentation | Missing top-level documentation comment for ~ | 6 |
|2|Metrics/BlockLength | Block has too many lines. | 1 |
|3|Rails/FilePath | Prefer Rails.root.join('path/to'). | 1 |

### Style/Documentation
```diff yml:.rubocop.yml
Style/Documentation:
  Enabled: false
```

### Style/Documentation
```diff yml:.rubocop.yml
Metrics/BlockLength:
  Exclude:
    - lib/tasks/auto_annotate_models.rake
```

### Rails/FilePath
```diff ruby:spec/rails_helper.rb
- # config.fixture_path = "#{::Rails.root}/spec/fixtures"
+ config.fixture_path = Rails.root.join('spec/fixtures')
```


## DidYouMean::SPELL_CHECKERSのdeprecated対応
コマンド打つとやたらこのメッセージが出てくるので対応します。
```plain
Calling `DidYouMean::SPELL_CHECKERS.merge!(error_name => spell_checker)' has been deprecated. Please call `DidYouMean.correct_error(error_name, spell_checker)' instead.
```

どうもbundlerのバージョンの問題の模様なので、バージョンをあげます。
https://github.com/rubygems/rubygems/issues/5234
```plain:ターミナル
→ docker-compose exec web bundle -v
Calling `DidYouMean::SPELL_CHECKERS.merge!(error_name => spell_checker)' has been deprecated. Please call `DidYouMean.correct_error(error_name, spell_checker)' instead.
Bundler version 2.1.4

→ docker-compose exec web bundle update --bundler

→ docker-compose exec web bundle -v
Bundler version 2.4.7
```

# さいごに
Railsをお仕事でやり始めて2年くらい経ちますが、環境構築周りはちゃんとやったことなかったので、思ったより時間かかりました。。

この記事のどこかがどなたかのお役に立てば幸いです。

Musubiteというサービスでトークテーマを公開してます！
ざっくばらんにお話しできればと思いますので、興味ある方お話ししましょうー！
https://musubite-job.com/recruitments/XWaimw?utm_source=zenn
