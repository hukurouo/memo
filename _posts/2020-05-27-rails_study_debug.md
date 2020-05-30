---
layout: post
title:  "Rails Study #2"
date:   2020-05-30 13:24:44 +0900
categories: Rails
tags: study
---

学習メモその２


<hr>
> [RailsGuide - デバッグ編](https://railsguides.jp/debugging_rails_applications.html)

- デバッグ
    - `<%= debug @article %>`
    - オブジェクトの中身をviewに表示

- ログをカスタムしたい場合は、以下
  - この場合、コントローラの途中でオブジェクトの中身を確認できる

~~~
 def create
    @article = Article.new(article_params)
    logger.debug "新しい記事: #{@article.attributes.inspect}"
    logger.debug "記事が正しいかどうか: #{@article.valid?}"
~~~

<br>
> [RailsGuide - ActiveRecord:マイグレーション](https://railsguides.jp/active_record_basics.html)


> Active Recordは、「知的に振る舞うのはモデルであり、データベースではない」というコンセプトに基づいています。

カラムの作成時や変更時に、カラムの修飾子を適用できる

- limit: string/text/binary/integerフィールドの最大サイズを設定します。
- precision: decimalフィールドの精度 (precision) を定義します。この精度は、その数字の総桁数で表されます。
- scale: decimalフィールドの精度 (スケール: scale) を指定します。この精度は小数点以下の桁数で表されます。
- polymorphic: belongs_to関連付けで使うtypeカラムを追加します。
- null: カラムでNULL値を許可または禁止します。
- default: カラムでのデフォルト値の設定を許可します。dateなどの動的な値を使う場合は、デフォルト値は最初 (マイグレーションが実行された日付など) にしか計算されないことに注意してください。
- index: カラムにインデックスを追加します。
- comment: カラムにコメントを追加します。

既存のマイグレーションを直接変更するのは一般的によくない（共同作業者にまで余分な作業を強いることになるので）


マイグレーションを新たに生成し、それを編集するのが一番良い

自分でマイグレーションファイルを書くときは、とりあえずchangeメソッドを使っておけばロールバックが出来るので、安全



