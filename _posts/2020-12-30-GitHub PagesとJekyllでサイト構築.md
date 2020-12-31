---
title: "GitHub PagesとJekyllでサイト構築"
categories:
  - blog
tags:
  - GitHub Pages
  - Jekyll
  - Ruby
---

## GitHub PagesとJekyllでサイト構築

とりあえずリポジトリ作ってREADME.mdに名前だけ書いて放置していたgithub.ioのページを更新してみた。

GitHub PagesはGitHubがあらかじめ用意している[テーマ](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/adding-a-theme-to-your-github-pages-site-with-the-theme-chooser)がいくつかあるが、
MarkdownとYAMLでサイトを作る[Jekyll](https://jekyllrb.com/)フレームワークを採用しているので、世にある他のテーマを使うことができる。

また、Remote Themeの設定をすれば、テーマをダウンロードすることなく適用ができる。

ということで、今回選んだテーマは[minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/)というやつ。

[GitHubで検索して](https://github.com/topics/jekyll-theme)、一番上に出てきたのを適当に選んだ。

## やってみて

自分の場合は、プロフィールとブログがあればとりあえずいいかなってことで、テーマの機能を絞って導入した。（そもそも載せるようなコンテンツがない）
gemのインストールとかは特に迷うことがなく進められたので、よかったが、
そもそもJekyllのサイトの構成を理解しないまま、適当に始めたのでので_config.ymlに書くとどう反映されるのか、どういうディレクトリにどういうファイルを置くべきなのかを逆引き的にファイル名や、コンテンツの内容を検索しながら調べていった。

_config.ymlは最初自分で書いていったが、途中でめんどくなって、テンプレートからコピペしてきた。

```sh
_data
  └ navigation.yml #ナビゲーションメニューの内容

# 固定ページを置くところ。
# ここじゃなくてもルートにファイルをおいたり、
# 別のディレクトリ作ってもいい。
_pages 
  └ home.md # permalinkの設定をして上げる必要がある

_posts # ブログ系のページ
assets # ファイル置き場
blog
  └ index.html　# /blog のページ
```

よくわからないのが、blogのページでページネーションを使うためには、ディレクトリを分けて、htmlにして、permalinkの設定をしてはダメらしい。