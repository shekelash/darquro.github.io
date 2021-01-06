---
title: "Dangerで "Cannot find a merge base between danger_base and danger_head" というエラーになった"
categories:
  - 開発
tags:
  - GitHub
  - GitHub Actions
  - Danger
---

GitHub ActionsでDangerを動かしてPRのチェックをしているんですが、あるとき、`Cannot find a merge base between danger_base and danger_head.`というエラーが起こるようになった。

## ログの全文はこちら

```
/opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/scm_source/git_repo.rb:138:in `find_merge_base': Cannot find a merge base between danger_base and danger_head. If you are using shallow clone/fetch, try increasing the --depth (RuntimeError)
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/scm_source/git_repo.rb:22:in `diff_for_folder'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/danger_core/dangerfile.rb:270:in `setup_for_running'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/danger_core/dangerfile.rb:280:in `run'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/danger_core/executor.rb:29:in `run'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/lib/danger/commands/runner.rb:73:in `run'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/claide-1.0.3/lib/claide/command.rb:334:in `run'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/lib/ruby/gems/2.7.0/gems/danger-8.2.1/bin/danger:5:in `<top (required)>'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/bin/danger:23:in `load'
	from /opt/hostedtoolcache/Ruby/2.7.2/x64/bin/danger:23:in `<main>'
Error: Process completed with exit code 1.
```

## 発生タイミング

作業中のブランチを最新のメインブランチでリベースをしてpushしたタイミングでした。

## 原因

ログにこのような記載がありますが、

> If you are using shallow clone/fetch, try increasing the --depth (RuntimeError)

内容としては、shallow cloneを使っていたら、depthの増やしてねということです。

### shallow cloneとは

通常git cloneすると、commitの履歴やtagなど全てcheckoutしますが、それだとcloneに時間ががかかり、最新のだけcheckoutすれば十分な場合、shallow cloneを使い、checkoutのボリュームを減らすことができます。
shallow cloneのパラメータが `--depth`で、1であれば、最新1commitだけを取得するようになります。

```sh
git clone --depth 1 <repository>
```

環境はGitHub Actionsで構築しており、以下のようなWorkflowを組んでいました。

```yml
name: Danger

on:
  [ pull_request ]

jobs:
  run_danger:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: Setup Ruby
      uses: actions/setup-ruby@v1
      with:
        ruby-version: '2.x'

    - name: Setup Danger
      run: |
        gem install danger

    - name: Run Danger
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      run: danger
```

`actions/checkout@v2` でcloneしたときに、履歴が取れてないことが原因だと思い、 https://github.com/actions/checkout で仕様を確認してみると、案の定、

```yml
# Number of commits to fetch. 0 indicates all history for all branches and tags.
# Default: 1
fetch-depth: ''
```

と、デフォルトは1となっていました。

## 解決策

`fetch-depth: 0` を指定して、無事エラーが解消できました。

```yml
- uses: actions/checkout@v2
  with:
    fetch-depth: 0
```
