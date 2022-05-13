---
title: 開発準備
date: 2022-05-02
draft: false
weight: 10
---

## AWS Toolkit for IntelliJ (Gradle)でのアプリ開発
参考：  
https://dev.classmethod.jp/articles/aws-toolkit-for-jetbrains-with-java8-gradle/  
https://qiita.com/kalzit/items/5d64f93bf75aaaad9915  

AWSでのサーバレスアプリケーションの開発／デプロイを効率化するために下記2つを用いる
- AWS Toolkit for IntelliJ IDEA：AWS上でのJava及びPythonアプリの開発／デバッグ／デプロイを容易にするOSSプラグイン
- SAM (AWS Serverless Application Model)：サーバレスアーキテクチャを構築するためのOSSフレームワーク

### SAM CLIのインストール
[参考ページ](https://docs.aws.amazon.com/ja_jp/serverless-application-model/latest/developerguide/serverless-sam-cli-install-linux.html)を元に、SAM CLIをインストールする

### AWS Toolkit for IntelliJ IDEAの導入
[参考ページ](https://dev.classmethod.jp/articles/aws-toolkit-for-jetbrains/)を元に、AWS Toolkitの導入、及び、認証情報／リージョンの設定を行う

### プロジェクトの作成
[参考ページ](https://dev.classmethod.jp/articles/aws-toolkit-for-jetbrains-with-java8-gradle/)を元に、SAMテンプレートプロジェクトを作成する