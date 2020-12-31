---
layout: default
title: ":leaves: Spring Boot"
description: ":clipboard: Spring BootでのHTTPS通信手順"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Spring BootでHTTPS通信を行うための設定や開発用の鍵と証明書などの設定を行います。  
今回はJDKに同梱しているKeyToolを使って行う事とします。  

<br />

## 1. キーストアを作成する

### 1-1. KeyToolでキーストアの作成

今回は、P12(PKCS:Public Key Cryptography Standards)という鍵や証明書などの複数の暗号化オブジェクトを使う事にします。  
`-genkeypair`オプションは、秘密鍵とサーバー公開鍵証明書(公開鍵含む)を両方作成するという意味です。  
その両方を格納するキーストアの形式がPKCS12という形式で秘密鍵と証明書を交換するフォーマットです。  
そのキーストアのファイル名をspboot.p12とします(`-keystore`で行う)。  
暗号化の形式は`-keyalg`、鍵のサイズは`-keysize`で指定し、`-validity`オプションは証明書の有効日数です。  
`-alias`は別名といいキーストアに対する名前なので自身で名前を決めて下さい。  
Enter後、入力を求められますが姓名(CN:Common Name)は使用するサーバーのホスト名(ドメイン名)と同じにする必要があります。  
今回は、`localhost`とします。その他は、入力しなくても良いですが今回は以下の様な形とします。  

    $ keytool -genkeypair -alias spboot -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore spboot.p12 -validity 365
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。この新規キーストア(鍵)に付けるもの)
    新規パスワードを再入力してください:(※パスワードは表示されないが構わず入力。再入力。)
    姓名は何ですか。
      [Unknown]:  localhost
    組織単位名は何ですか。
      [Unknown]:
    組織名は何ですか。
      [Unknown]:  mysoshiki
    都市名または地域名は何ですか。
      [Unknown]:
    都道府県名または州名は何ですか。
      [Unknown]:
    この単位に該当する2文字の国コードは何ですか。
      [Unknown]:  jp
    CN=localhost, OU=Unknown, O=mysoshiki, L=Unknown, ST=Unknown, C=jpでよろしいですか。
      [いいえ]:  y

<br />

## 2. キーストアから公開鍵証明書をエクスポート

キーストア(P12)から公開鍵証明書を`.cer`形式でエクスポートします。  
これは、HTTPS通信を行うにあたりクライアントアプリからSpring Bootにアクセスを行えるようにするために行います。  
Java(JVM)のクライアントアプリからアクセスするには、Java(JVM)が管理するキーストア`cacerts`ファイルへこの証明書を登録  
しなければなりません。まずは、公開鍵証明書をエクスポートします。  
先ほどの、spboot.p12キーストア(-keystoreオプション)から別名spbootの公開鍵証明書を`spboot.cer`ファイルとしてエクスポート。  

    $ keytool -export -alias spboot -file spboot.cer -keystore spboot.p12
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。spboot.p12で付けたパスワード)
    証明書がファイル<spboot.cer>に保存されました

<br />

## 3. Java(JVM)のキーストアへ公開鍵証明書を登録

次は、先ほどエクスポートした公開鍵証明書(.cer)をJava(JVM)が管理するキーストア`cacerts`ファイルへこの証明書を登録  
しなければなりません。Javaで作成したアプリケーションでHTTPS通信を行うとJVMのセキュリティに引っかかるからです。  
※KeyToolがあるディレクトリからコマンドを実行していますので相対Pathの`../lib/security/cacerts`としています。  

    $ keytool -importcert -trustcacerts -file spboot.cer -keystore ../lib/security/cacerts -alias spboot
    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。spboot.p12で付けたパスワード)
    所有者: CN=localhost, OU=Unknown, O=mysoshiki, L=Unknown, ST=Unknown, C=jp
    発行者: CN=localhost, OU=Unknown, O=mysoshiki, L=Unknown, ST=Unknown, C=jp
    シリアル番号: xxxxxxxx
    有効期間の開始日: Tue Apr 14 16:45:38 JST 2020終了日: Wed Apr 14 16:45:38 JST 2021
    証明書のフィンガプリント:
             SHA1: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
             SHA256: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
    署名アルゴリズム名: SHA256withRSA
    サブジェクト公開鍵アルゴリズム: 2048ビットRSA鍵
    バージョン: 3

    拡張:

    #1: ObjectId: 2.5.29.14 Criticality=false
    SubjectKeyIdentifier [
    KeyIdentifier [
    0000: xx xx xx xx xx xx xx xx   xx xx xx xx xx xx xx xx  <..../..e.Ok. ..
    0010: xx xx xx xx                                        ..?.
    ]
    ]

    この証明書を信頼しますか。 [いいえ]:  y
    証明書がキーストアに追加されました

※.cerでない.crtや.p12ファイルなどで登録を行おうとすると`java.lang.Exception: 入力はX.509証明書ではありません`と言う  
エラーが出ますので`.cer`ファイルで行う事。  

※また、以下で`cacerts`ファイルに正しく登録できているか確認できます。登録と同じ様なパラメータが表示されたらOKです。  

    $ keytool -list -v -alias sboot -keystore ../lib/security/cacerts

<br />

## 4. Spring Bootのapplication.propertiesファイル設定

まず、キーストアファイルspboot.p12をプロジェクト直下(.pomファイルと同じ階層)に移動します。  
application.propertiesファイルへ以下の様に入力します。  
※spboot.p12を作成した別のフォルダに入れた場合などはserver.ssl.key-store: folder/spboot.p12とする。  
application.propertiesフォルダと同じ階層に移動した場合は`classpath:spboot.p12`などと指定できます。  

    server.port: 8443
    server.ssl.key-store: spboot.p12
    server.ssl.key-store-password: changeit
    server.ssl.key-store-type: PKCS12
    server.ssl.key-alias: spboot

**間違えない事**  
パスワードパラメータは**server.ssl.key-store-password**です。間違えないで下さい。  
実は、今回Eclipseの自動入力補完(オートコンプリート)につられて`server.ssl.key-password`としておりました。  
キーストアタイプのファイルの場合server.ssl.key-**store**-passwordでした。  
これを間違えるとSpring Boot起動時に以下のメッセージが出ます。  

    org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat server
    訳：org.springframework.boot.web.server.WebServerException：組み込みTomcatサーバーを起動できません

    java.lang.IllegalArgumentException: standardService.connector.startFailed

    org.apache.catalina.LifecycleException: Protocol handler start failed
    訳：org.apache.catalina.LifecycleException：プロトコルハンドラーの開始に失敗しました

    java.lang.IllegalArgumentException: Private key must be accompanied by certificate chain
    訳：java.lang.IllegalArgumentException：秘密鍵には証明書チェーンが必要です

詳しい原因は分かりませんが証明書チェーンが切断される様です。  
これに気付かずキーストアや登録、証明書のエクスポートやファイル形式を変更したり、そもそも作り方自体間違えているのか？  
自身の認証局を作り公開鍵署名要求(csr)も作り署名が必要なのか？迷いましたが違いました。苦労したので書いておきます。  

* * *
