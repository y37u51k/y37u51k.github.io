---
title: Hugoの導入とGithub Pageの作成
date: 2022-04-13
draft: false
weight: 10
---

## Hugoの導入

### インストール
- [リリースページ](https://github.com/gohugoio/hugo/releases)から、Win64bitをzipダウンロード
- 下記の例を参考に任意の場所にHugoのbinフォルダを作成しzipを解凍、環境変数パスを通し、hugoコマンドが実行できることを確認する
```directory path
    C:\Hugo\bin
```
### テーマの適用

- hugoフォルダ配下で下記のコマンドを実行し、作業フォルダを作成する

```
    $ hugo new site "hugo-document(任意の名称)"
```

    ひな形テンプレート
    
```
    hugo-document
    ├─archetypes : contentファイル以下に作るファイルの雛形を定義する
    ├─content : Markdownで作成したコンテンツを入れる
    ├─data : サイト全体で共有する情報を定義する
    ├─layouts : サイトの構造を定義する
    ├─static : cssや画像を置く
    └─themes : テーマ資材を置く
```

- 作成したフォルダに移動して、[テーマ](https://themes.gohugo.io/)をインストールする

```
    $ cd hugo-document/themes

    //テーマをインストール
    $ git clone https://github.com/thingsym/hugo-theme-techdoc.git
```

### 新規ページの作成

- 下記のコマンドを実行して新規ファイルを作成する
```
    $ hugo new content/test.md(任意の名称)
```

- Hugoのセクション機能を使って記事を階層構造にする場合、セクションのindexページを作成する
```
    /content/_index.md (どのセクションにも属していないindex page)
    /content/tips/_index.md (tipsセクションのindex page)
    /content/tips/page1.md (single page)
```


## Github Pageの作成

### config. tomlの修正
- 直下のconfig.tomlを、下記を参考に適宜修正する

```
    baseurl = "https://y37u51k.github.io/"

    languageCode = "ja-jp"
    DefaultContentLanguage = "ja"
    title = 'Document'
    theme = "hugo-theme-techdoc"

    hasCJKLanguage = true
    metaDataFormat = "yaml"

    defaultContentLanguage = "en"
    defaultContentLanguageInSubdir= false
    enableMissingTranslationPlaceholders = false

    #Github Pageで公開する際はpublic/ではなく、docs/配下にhtmlを設置すること
    publishdir = "docs"
    #github.io/配下にディレクトリ階層を持たせたい場合には、trueにすること
    canonifyurls = true
```
### ドラフト機能の切り替え
- Hugoのドラフト機能を用いると、未完成の記事はデフォルトでhtmlファイルとして出力されない
- 下記の通りに各Markdownファイルのドラフト機能をオフにする、もしくは、html出力時に-Dオプションを指定すること

```.mdファイル
    ---
    title: Tips
    date: 2022-04-13
    lastmod: 2022-04-13
    #falseにするとhtml出力が行われる
    draft: false
    weight: 9

    description: Text about this post
    images:
    ---
    ## 

```

html出力コマンド
```command
    $ hugo -D
```
### html出力
- 下記のコマンドを用いて、htmlファイルを/docs配下に出力する

```
    #ドラフト記事は出力しない
    $ hugo
    #ドラフト記事も出力する
    $ hugo -D
```

### Github Pageの設定
- /docsを含む全てのディレクトリをGitのremoteレポジトリにプッシュする
- Github上の```Settings```タブから、Github Pagesのsourceをmaster/docsに設定する
- [https://y37u51k.github.io/](https://y37u51k.github.io/)にアクセスするとページが閲覧可能となる
