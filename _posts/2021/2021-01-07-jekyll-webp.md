---
title: "Jekyllでwebpを使いたい"
categories:
  - 開発
tags:
  - GitHub Pages
  - Jekyll
---

PageSpeed Insightsで、パフォーマンスを計測してみたら、

モバイル
![mobile_before.png](/assets/images/posts/2021-01-07-jekyll-webp/mobile_before.png)

PC
![pc_before.png](/assets/images/posts/2021-01-07-jekyll-webp/pc_before.png)

と、特にモバイルのパフォーマンスが悪く、一番は画像が遅いことがわかった。

なので、webp対応したいなと思って調べてみると、
[https://github.com/sverrirs/jekyll-webp](https://github.com/sverrirs/jekyll-webp)
というプラグインを見つけた。

gem installしてjekyll serveすれば、_site内にwebpが吐き出されているので、良さそうと思ってpushしてみると、GitHub Pagesに上げると動いてなかった。
というのも、GitHub Pagesでは特定のプラグインしか動かない仕様。[https://pages.github.com/versions/](https://pages.github.com/versions/)

[同じような悩みがissueに上がっており](https://github.com/sverrirs/jekyll-webp/issues/15)、jekyll-actionってGitHub Actions使って、サポートされてないプラグインも実行するようなものがあって、試してみたが動かなかった。

仕方がないので、ローカルでjekyll serveして吐き出したwebpをassetsにコピーして、直接上げることにした。

PageSpeed Insightsのパフォーマンスも一応あがった。

モバイル
![mobile_after.png](/assets/images/posts/2021-01-07-jekyll-webp/mobile_after.png)

PC
![pc_after.png](/assets/images/posts/2021-01-07-jekyll-webp/pc_after.png)

ちなみにwebp対応するのにjpegと両方指定するpictureタグを使いたかったが、使ってるテンプレートでは対応してなくて、webpだけにした。
今は主要なブラウザはwebp対応してるので、ほぼ問題ないだろう。

```
<picture>
  <source srcset="/path/to/image.webp" type="image/webp">
  <img src="/path/to/image.jpg" alt="">
</picture>
```

[https://caniuse.com/webp](https://caniuse.com/webp)