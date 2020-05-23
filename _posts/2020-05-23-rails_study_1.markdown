---
layout: post
title:  "Rails Study #1"
date:   2020-05-23 13:24:44 +0900
categories: Rails
tags: study
---

復習メモその１

[Railsガイド - Railsをはじめよう](https://railsguides.jp/getting_started.html) より

<hr>

- Rails哲学
    - 同じことを繰り返すな (Don't Repeat Yourself: DRY)
    - 設定より規約が優先される (Convention Over Configuration)
        - レールから外れると碌なことにならないので、自己流で書くのは控え、正攻法を調べるべし

- `rails new -h` でアプリケーションビルダで使えるすべてのオプションを確認できる

- strong_parameters : 本当に使ってよいパラメータだけを厳密に指定する
    - マスアサインメント脆弱性対策

{% highlight ruby %}
def create
  @article = Article.new(article_params)
 
  @article.save
  redirect_to @article
end
 
private
  def article_params
    params.require(:article).permit(:title, :text)
  end

#共用できるようにメソッドをくくりだしておくのが一般的  
{% endhighlight %}

- 記事モデルにbelongするコメントモデルの実装
    - 以下で生成されるルーティングは　`/articles/1/comments`

~~~
<%= form_with(model: [ @article, @article.comments.build ], local: true) do |form| %>
~~~

{% highlight ruby %}
def create
  @article = Article.find(params[:article_id])
  @comment = @article.comments.create(comment_params)
{% endhighlight %}




- `link_to` の書き方が見覚えなかったのでメモ

~~~
 <%= link_to 'Edit', edit_article_path(@article) %>
 <%= link_to 'Destroy Comment', [comment.article, comment],
               method: :delete,
               data: { confirm: 'Are you sure?' } %>
~~~

- BASIC認証機能：簡易な認証システム
    - 人気が高い認証システムは Devise と Authlogic 

{% highlight ruby %}
 http_basic_authenticate_with name: "dhh", password: "secret", except: [:index, :show]
{% endhighlight %}

