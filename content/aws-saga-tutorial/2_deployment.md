---
title: 環境構築
date: 2022-04-18
draft: false
weight: 10
---
## コードの修正
コンパイルを実行する前に適宜コード修正を行う  
```sagaLambda.ts```内のStep Functionsデプロイリージョンを環境に合わせて修正すること
```
const stepFunctions = new AWS.StepFunctions({
region: 'ap-northeast-1'
});
```

## TypeScriptコードのコンパイル

下記コマンドを```/bin```上で実行し、TypeScriptで書かれたCDKスクリプト(```cdk-serverless-saga.ts```)をJSにコンパイルする

```command
$ npm run build
```
JSファイル及び型定義ファイル (.d.ts) が生成されていることを確認する

## CDK 必要スタックの構築

下記コマンドで、CDKで定義したAWSリソースをデプロイするための環境を構築する
```command
cdk bootstrap aws://aws://<アカウントID>/<リージョン名> --profile default
cdk bootstrap aws://626394096352/ap-northeast-1 --profile default
```
CDKToolkitと呼ばれるCloudFormationStackが作成される  
リージョン内にCDKを利用するためのロール、ECR、S3バケット```cdktoolkit-stagingbucket-[random]```が作成される

次に下記コマンドで、CDKで定義したAWSリソースをデプロイする

```command
$ cdk ls
CdkServerlessSagaStack

$ cdk deploy <スタック名> --profile default
$ cdk deploy CdkServerlessSagaStack --profile default
```
上記のスタック名でCloudFormationStackが作成される  
デプロイの完了後にコンソール上に出力される API Gateway URLを取得する

## 備考
デプロイされた単一または複数スタックを削除する際は下記のコマンドを使用する
```command
$ cdk destroy <スタック名> --profile default
$ cdk destroy CdkServerlessSagaStack --profile default
```