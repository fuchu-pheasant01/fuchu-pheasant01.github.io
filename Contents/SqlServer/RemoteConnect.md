---
layout: default
title: ":bar_chart: Microsoft SQL Server"
description: ":computer: Microsoft SQLServerリモート接続について"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. リモート接続(名前付きインスタンスSQLEXPRESSの場合の例)

    1-1. Sqlserver Configuration Manager(SQLServer xxxx 構成マネージャー)を開く。
    1-2. ツリーの「SQLServer ネットワークの構成」→「SQLEXPRESSのプロトコル」を選ぶ。
    1-3. 「TCP/IP」 上で右クリックし「有効化(E)」を押し有効にする。
    1-4. 「TCP/IP」 上で右クリックし「プロパティ(R)」を選び「TCP/IPのプロパティ」ダイアログを開く。
    1-5. 「IPアドレス」タブをクリックしツリーの「IPAll」の「TCPポート」に1433と入力し「OK」ボタン
    　　を押し確定する。
    1-6 .このままでは適用されないので「SQLServerのサービス」の「SQLServer(SQLEXPRESS)」を
    　　右クリックし「再起動」を選びSQLServerを再起動させる。
    1-7. 「SQL Server Browser」を右クリックし「開始(S)」を選び開始する。
    　　※後に調べると特に必要ない(2015/10/14)。JavaのJDBCなどでは起動する必要がある(2019/02/06)
    1-8. 「コントロールパネル」→「Windows Defender ファイアウォール」→左側の「詳細設定」を
    　　クリックし、「セキュリティが強化されたWindows Defender ファイアウォール」ダイアログ
      　  を開く。
    1-9. 左側のリストの「受信側の規則」をクリック。
    　   右側のリスト「操作」の「新しい規則」をクリックし、「新規の受信の規則ウィザード」
           ダイアログを開く。
    1-10. 「ポート」ラジオボタンを選び「次へ(N)」をクリック。
         　「TCP(T)」と「TCP,特定のローカルポート(S)」ラジオボタンを選びテキストボックスに「1433」
            と入力し、「次へ(N)」をクリック。
            「接続を許可する(A)」ラジオボタンを選び「次へ(N)」をクリック。
            規則のチェックボックスは状況に応じてチェックを入れ(今回はすべてにチェックを入れる)
            「次へ(N)」をクリック。
    　　「名前(N)」と「説明(オプション)(D)」テキストボックスに入力し、「完了(F)」をクリックし
      　　新たな規則を作る。

<b><strike>ここまででリモート接続を試したが接続できず(2014/07/09)</strike></b>  

 参考資料 : <https://awoni.net/fc/remote>  

### 2015/10/14もう一度試す

    ファイアウォールの設定  
    4. の設定はサーバー側の受信でクライアントは特に必要なし。  

    1 . のIPAllの動的ポートは無しとし、固定ポートに1433と入れる。  
    接続成功。  

**補足**  
※既定のインスタンスではなく、名前付きインスタンス`SQLEXPRESS)`などとしている場合は動的ポート  
を使うようになっているので固定ポートを使うようにする。  
また、`1433ポート`はファイアウォールがデフォルトでは許可されていないため許可する。  
インストール時に既定のインスタンスの場合は`固定ポート1433`を使うようになっている。  

**SQLserverBrowserを実行できない**  
デフォルトでは`SQLServerBrowser`を実行できないようになっている。  

    1. 「コントロールパネル」→「管理ツール」→「サービス」を開く。  
    　一覧の「SQL Server Browser」上で右クリックしプロパティを選ぶ。  
    2. 「スタートアップの種類(E)」コンボボックスの「無効」を「手動」にする。  
    　「OK」ボタンを押し適用する。

SqlserverConfigurationManagerのSQLServerBrowserが手動で開始できるようになる。  

<br />

### Microsoft Visual Studio .NETでの接続文字列

名前付きインスタンスの場合：`コンピュータ名\\インスタンス名,1433`  
既定のインスタンスの場合は：`コンピュータ名,1433`  
※コンピュータ名はIPアドレスでも可能。  

* * *
