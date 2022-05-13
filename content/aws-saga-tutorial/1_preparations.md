---
title: 1_事前準備
date: 2022-04-13
draft: false
weight: 10
---
## NodeJSの導入

参考サイト：  
https://selegee.com/535/  
https://tecadmin.net/install-latest-nodejs-amazon-linux/  

公式ページにてv14が指定されているので、14.xを指定して下記コマンドでインストール実行する
```command
$ curl --silent --location https://rpm.nodesource.com/setup_14.x | sudo bash -  
$ sudo yum -y install nodejs
```

下記コマンドで確認を行う
```command
$ npm -v
6.14.16
$ node -v
v14.19.1
```

## AWS CDK CLIのインストール
参考サイト：  
https://aws.amazon.com/jp/getting-started/guides/setup-cdk/module-one/

公式ページにてv 1.89.0が指定されているので、下記コマンドでバージョンを指定してインストール実行する

```command
$ npm install -g aws-cdk@1.89.0
```
下記コマンドで確認を行う
```
$ cdk --version
1.89.0 (build df7253c)
```

## 必要パッケージのインストール

公式サイトに掲載されているpackage.jsonを利用して必要パッケージを一括インストールする

```command
$ npm install
```
/node_modules 配下に必要パッケージがインストールされていることを確認する