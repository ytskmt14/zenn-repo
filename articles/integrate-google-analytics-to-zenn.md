---
title: "zennのアクセス解析にgoogle analyticsを導入してみる" # 記事のタイトル
emoji: "👶" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["googleAnalytics"] # トピックス（タグ）["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

# はじめに
ご覧いただきありがとうございます。mocです。

今回は、zennのアクセス解析にGoogle Analyticsを導入していきたいと思います。

初めてやるので、間違ってるところもあるかもしれません。

その時はご指摘いただけると嬉しいです。

# 今回の内容
今回やるのは、おおきく2ステップだけです。

1. Google Analyticsの設定
2. zennのアカウント設定の変更

それでは、やっていきましょう。

## Google Analyticsの設定
まず最初に、[Google Analysisの公式ページ](https://analytics.google.com/analytics/web/)にアクセスしましょう。

左のサイドメニューの歯車アイコン（管理）をクリックすると、こんな画面が表示されるので、**アカウントを作成**をクリックしてみましょう、

![](https://storage.googleapis.com/zenn-user-upload/iklgi5u3wk1oqn59q0hw7xyk6rrw)

そうすると、アカウントの設定画面が表示されるので、順番に進めていきます。
最初は、アカウント名を入力していきます。
今回は、**moc**としました。

入力したら、**次へ**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/yilnhzywd3mvbxo78shuk3hm4p4o)

次は、プロパティの設定です。
プロパティ名を入力します。

今回は、**zenn-analysis**としました。

タイムゾーンや通貨は、なんでもいいみたいですが、今回は日本にそれぞれ合わせてみました。

これらができたら、**次へ**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/xib8bwo1yedylh0eky0j5ymtt0i2)

最後に、ビジネスの概要を設定していきます。

この辺よくわからなかったので、ふわっと設定してみました。

そしたら、**作成**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/wgikyhg3ivcpr7okd7kntijl9a7b)

利用規約の画面が表示されるので、チェックボックスにチェックを入れて、**同意する**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/nsneugw62jms9ro8cahmhm0wztdg)

次に、**Choose a platform** の**ウェブ**をクリックします。

![](https://storage.googleapis.com/zenn-user-upload/ygpe3nkgqhpsrjoparehj1ihbcz8)

データストリームの設定です。
ウェブサイトのURLは、**zenn.dev/** と入力し、ストリーム名は任意ですが、今回は、**moc**としました。

入力したら、**ストリームを作成**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/kg8xe6oe4jtule7s3hoojnevkt0a)

すると、測定IDが作成されて表示されます。
（ **G-** から始まる文字列です。）

これは、後で使うのでメモしておきましょう！

![](https://storage.googleapis.com/zenn-user-upload/0nlcedsqwhwcyww5zhlgnj85epy2)

これで、Google Analytics側の設定は完了です。

## zennのアカウント設定の変更
次に、zennの設定をしていきます。

まず、[zenn](https://zenn.dev/)を開いて、ヘッダーの自分のアイコンをクリックします。

メニューが開くので、**アカウント設定**を選択します。
![](https://storage.googleapis.com/zenn-user-upload/3vuzushrp99bpw8gi4dibqaz12om)

アカウントの設定画面が開くので、**トラッキングIDを設定**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/3380p7fd9devfnhf4pnb2wgbzdss)

トラッキングIDの入力画面が開くので、先ほどメモしておいたID（ **G-** から始まる文字列）を入力します。

入力したら、**保存する**ボタンをクリックします。

![](https://storage.googleapis.com/zenn-user-upload/hxkkp9tkioxrnea18wkpu4fq7fhw)

**Google Analytics**の項目のところにトラッキングIDが表示されていたら完璧です。

![](https://storage.googleapis.com/zenn-user-upload/tabsipbzco7a3ryx767axdhnt7xh)

これで、設定は完了です。

最後に、Google Analyticsのページをみてみましょう。

![](https://storage.googleapis.com/zenn-user-upload/kutfjxf09v5zq7rl2erd3jbzkva9)

おぉ、表示されました！

# まとめ
今回は、zennのアクセス解析にGoogle Analyticsを導入してみました。

これから定期的にちょこちょこ確認してみよーかなと思います。
