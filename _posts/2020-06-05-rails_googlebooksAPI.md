---
layout: post
title:  "GoogleBooksAPIとRailsで書籍登録"
date:   2020-06-05 13:24:44 +0900
categories: Rails GoogleBooksAPI
tags: memo
---

#### 環境
- ruby  2.5.8
- rails 5.2.4
- Bootstrap 3.4

#### 完成図

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/422212/cdd1797b-465e-8141-530f-de1ff1877847.png)

タイトルをクリックすると、

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/422212/69eafb76-46ac-9880-e5f0-8ba0e2895cf9.png)

フォームに値がセットされ、簡単に書籍登録ができます。


#### 実装

BooksControllerは作ってあることとします。

まずは書籍検索機能です。
今回は書籍の登録にGoogleBookAPIを用いようと思うので、検索機能はnewアクションに記述していきます。



{% highlight ruby %}
  def new
    @book = Book.new

    #書籍検索APIの処理
    if params[:keyword].present?
      require 'net/http'
      url = 'https://www.googleapis.com/books/v1/volumes?q='
      request = url + params[:keyword]
      enc_str = URI.encode(request)
      uri = URI.parse(enc_str)
      json = Net::HTTP.get(uri)
      @bookjson = JSON.parse(json)

      count = 5 #検索結果に表示する数
      @books = Array.new(count).map{Array.new(4)}
      count.times do |x|
        @books[x][0] = @bookjson.dig("items", x, "volumeInfo", "title")
        @books[x][1] = @bookjson.dig("items", x, "volumeInfo", "imageLinks", "thumbnail")
        @books[x][2] = @bookjson.dig("items", x, "volumeInfo", "authors")
        @books[x][2] = @books[x][2].join(',') if @books[x][2] #複数著者をカンマで区切る
        @books[x][3] = @bookjson.dig("items", x, "volumeInfo", "industryIdentifiers", 0, "identifier")
      end
    end
  end
{% endhighlight %}

GoogleBookAPIは特に登録などしなくとも、
`https://www.googleapis.com/books/v1/volumes?q=`に検索ワードを与えてあげれば検索結果をjson形式で返してくれます。

Railsで検索した時はこんな感じです↓
https://www.googleapis.com/books/v1/volumes?q=rails

取得したjsonはJSON.parceを使ってハッシュに変換して、そこから欲しい値を抜き出していきます。

`@bookjson["items"][x][volumeInfo][title]` でも取得はできるのですが、一部のデータが存在しないとNoMethodErrorが発生してしまいます。条件分岐で例外処理もできますが、やや冗長。

ということでdigメソッドを用いてハッシュを掘っています。キーに対応する値が存在しなければnilを返してくれるので安心ですね。

ビューに検索窓と検索結果欄を作ります。

~~~
<h1>書籍の新規登録</h1>
  <%= form_tag('/booksearch', method: :get) do %>
    <div class="input-group">
      <%= search_field_tag "keyword", params[:keyword], class: "form-control", placeholder: "キーワード検索" %>
        <span class="input-group-btn">
          <%= submit_tag "検索", class: "btn btn-primary" %>
        </span>
    </div>
  <% end %>
<h2>検索結果</h2>
<% if @books.present? %>
  <% @books.each do |book| %>
     <img src="<%= book[1] %>" width="40" vspace="2">
     <%= book[0] %> |
     <%= book[2] %> | 
     <%= book[3] %> <br>
  <% end %>
<% end %>
~~~

検索窓用のルーティングも書いてあげましょう

{% highlight ruby %}
  resources :books
  get '/booksearch', to: 'books#new'
{% endhighlight %}
これで検索機能は完成です。

続いて、書籍登録のフォームを用意します。

~~~
<%= form_with scope: :book, url: books_path, local: true do |form| %>
  <%= render 'shared/error_messages' %>
  <%= form.label :title %>
  <%= form.text_field :title, class: 'form-control', value: "#{@title}" %>

  <%= form.label :author %>
  <%= form.text_field :author, class: 'form-control', value: "#{@author}" %>

  <%= form.label :code %>
  <%= form.text_field :code, class: 'form-control', value: "#{@code}" %>

  <%= form.hidden_field :img, class: 'form-control', value: "#{@img}" %

  <%= form.submit "Save Book", class: "btn btn-primary" %>
<% end %>
~~~

`value: "#{@title}"`としているのがポイントで、これで値を受け取ります。
画像サムネイルのURLは手直しが発生することがないので`hidden_field`で受け取っています。

フォームに値を送信する仕組みを作ります。

~~~
...
<img src="<%= book[1] %>" width="40" vspace="2">
<%= link_to book[0], controller: "books", action: "new", title: book[0], code: book[3], author: book[2], img: book[1] %>
<%= book[2] %> | 
...
~~~

先程の検索結果に表示させたいた book[0]（※書籍のタイトル）にクエリ付きのリンクを貼ります。これでコントローラに値を送信します。

{% highlight ruby %}
  def new
    ...
    @title = params[:title] if params[:title].present?
    @code = params[:code] if params[:code].present?
    @author = params[:author] if params[:author].present?
    @img = params[:img] if params[:img].present?
  end
{% endhighlight %}

newアクションにクエリパラメータを受け取る処理を追記しました。ビュー（フォーム）用にインスタンス変数に格納します。

これにて完成です。
