---
layout: default
title: ":cloud: クラウド、Heroku"
description: ":cloud: Herokuコマンド"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. ログイン、ログアウト

### 1-1. ログイン

    $ heroku login --interactive

\--interactiveを入れないと既定のブラウザが開きログイン画面が出る。  
heroku cli はデフォルトでブラウザが開くように設定してあるようだ。  

### 1-2. ログアウト

    $ heroku logout
    Logging out... done

<br />

## 2. Heroku CLIのバージョンを確認する

    $ heroku version
    heroku/7.24.3 win32-x64 node-v11.14.0

<br />

## 3. Heroku CLIのバージョンをアップする

Herokuのコマンドを発行していると以下の様な警告が表示される。  

```\#heroku
 »   Warning: heroku update available from 7.21.0 to 7.24.3
```

Heroku CLIの最新版が出ていますが古いバージョンをお使いですという事でしょう。  

バージョンアップするには以下の様に実行します。  

    $ heroku update
    heroku: Updating CLI from 7.21.0 to 7.24.3... done
    heroku: Updating CLI... done

バージョンアップされましたのでバージョンを確認してみましょう。  

<br />

## 4. ログの確認

     $ heroku log

* * *
