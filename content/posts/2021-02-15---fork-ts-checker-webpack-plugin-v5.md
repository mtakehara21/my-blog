---
title: "Vue3のビルド時にCannot find module 'fork-ts-checker-webpack-plugin-v5'というエラーがでて苦しんだ"
date: "2021-02-15T00:31:32.169Z"
template: "post"
draft: false
slug: "20200215"
category: "Programming"
tags:
  - "Web Development"
  - "CI/CD"
description: "Vue3のビルド時にCannot find module 'fork-ts-checker-webpack-plugin-v5'というエラーがでて苦しんだ"
socialImage: "/media/GWake0I9A3339_TP_V (1).jpg"
---

Vue3で作ったアプリケーションをCircleCIでビルドしようとした際に起きたエラーへの対応方法を記す。
対応があまりにもお粗末なので、その内容も備忘録として記す。


- [状況](#状況)
- [解決方法](#解決方法)
- [試したこと](#試したこと)
- [まとめ](#まとめ)


## 状況
Vue3 + TypeScriptで作成したアプリケーションをデプロイするワークフローをCircleCIを用いて作成中。
デプロイ自体はCDK（S3へのアップ・CloudFrontでの配信）で行うが、その前にビルドする必要があるので、CircleCIで用意したコンテナ内でビルドする必要があった。
しかし、ビルド時に `Cannot find module 'fork-ts-checker-webpack-plugin-v5'`というエラーが出て、ビルドで成功しない状態が続いた。ローカルでは正常に動いていたのにだ。

## 解決方法
CircleCIのDockerイメージはなんとなく、`circleci/python:3.7.2-node`を使っていたのが問題。
これではnodeは10系なので、それが原因で`npm run build`が失敗していた。
イメージを`circleci/python:3.9.0b5-node`に変更したところ無事に成功

## 試したこと
ローカルとコンテナの差はなんだろうと考えていたが、nodeのバージョンの違いに気づくまでに小一時間かかり、macOSとLinuxの違いなのだと思い込みひたすら、package.jsonをいじっていた。

* ncuを使ってupdate
* fork-ts-checker-webpack-pluginのインストール
* 他のパッケージの依存関係修正
* optional ででるwarningへの対応


## まとめ
パッケージの依存関係でインストールされた、見に覚えのないパッケージにより振り回された。
パット見はnpm周りの問題に見えるのも、解決をおくらせた原因。実行環境の違いに気がつくまで時間がかかってしまった。

