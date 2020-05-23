---
layout: post
title:  "DockerでJekyllをローカルで動かす"
date:   2020-05-23 00:17:29 +0900 
categories: jekyll docker
tags: memo
---
GithubPages＋Jekyllで技術ブログをつくってみました

Jekyllでブログを建てるのはGitHubからフォークしてくるだけなので簡単なのですが、ローカルで動かすとなるとやや苦戦したのでメモに残します

[https://jamstackthemes.dev/ssg/jekyll/][url-1]

たくさんのテーマがありますね

<hr>

Dockerを立ち上げます

~~~
$ docker pull jekyll/jekyll
$ docker container run -p 4000:4000 -it --name jekyll-test -v [任意のディレクトリ]/:/usr/src/app -w /usr/src/app jekyll/jekyll bash
~~~

任意のディレクトリにはGitHubからcloneしてきたJekyllディレクトリを指定します

あとは `bundle install` して `jekyll serve` をすれば、起動します

ドキュメントには `bundle exec jekyll serve` で起動しろと書いてあるのですが、なぜかこれだと繋がりませんでした...　

[url-1]: https://jamstackthemes.dev/ssg/jekyll/
