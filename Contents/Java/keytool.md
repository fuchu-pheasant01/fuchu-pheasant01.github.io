---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":clipboard: KeyTool操作手順"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Javaが標準で内蔵している`KeyTool`と言う鍵や証明書の操作を行うアプリケーションの使い方について説明します。 
※KeyToolは、環境変数のPath設定を行うとスムーズですがPath設定を行わない場合はJavaのbinフォルダへ移動して行って下さい。 

<br />

## 1. キーストアを作成する

### 1-1. KeyToolでキーストアの作成

今回は、P12(PKCS:Public Key Cryptography Standards)という鍵や証明書などの複数の暗号化オブジェクトを使う事にします。  
`-genkeypair`オプションは、秘密鍵とサーバー公開鍵証明書(公開鍵含む)を両方作成するという意味です。  
※Java6以降はgenkeyオプションではなく、`-genkeypair`に変わったそうです。  
その両方を格納するキーストアの形式がPKCS12という形式で秘密鍵と証明書を交換するフォーマットです。  
暗号化の形式は`-keyalg`、鍵のサイズは`-keysize`で指定し、`-validity`オプションは証明書の有効日数です。  
`-alias`は別名といいキーストアに対する名前なので自身で名前を決めて下さい。  
Enter後、入力を求められますが姓名(CN:Common Name)は使用するサーバーのホスト名(ドメイン名)と同じにする必要があります。  

    $ keytool -genkeypair -alias <別名> -storetype PKCS12 -keyalg RSA -keysize 2048 -keystore <ファイルPath>.p12 -validity 365
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。この新規キーストア(鍵)に付けるもの)
    新規パスワードを再入力してください:(※パスワードは表示されないが構わず入力。再入力。)
    姓名は何ですか。
      [Unknown]:  <ホスト名(ドメイン名)>
    組織単位名は何ですか。
      [Unknown]:
    組織名は何ですか。
      [Unknown]:  
    都市名または地域名は何ですか。
      [Unknown]:
    都道府県名または州名は何ですか。
      [Unknown]:
    この単位に該当する2文字の国コードは何ですか。
      [Unknown]:  jp
    CN=<ホスト名(ドメイン名)>, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=jpでよろしいですか。
      [いいえ]:  y

<br />

## 2. キーストアから公開鍵証明書をエクスポート

### 2-1. PKCS12形式のキーストアから公開鍵証明書をエクスポート

4.で作成したキーストア(.P12)から公開鍵証明書を`.cer`形式でエクスポートします。  
これは、HTTPS通信を行うにあたりアプリケーションからサーバーにアクセスを行えるようにするために行います。  
Java(JVM)のアプリケーションからアクセスするには、Java(JVM)が管理するキーストア`cacerts`ファイルへこの証明書を登録  
しないとJVMのセキュリティに引っかかりますので登録しなければなりません。  
先ほどの、&lt;ファイル名>.p12キーストア(-keystoreオプション)から&lt;別名>の公開鍵証明書を`<ファイル名>.cer`ファイルとしてエクスポート。  

    $ keytool -export -alias <別名> -file <ファイルPath>.cer -keystore <ファイルPath>.p12
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。登録した<ファイル名>.p12のパスワード)
    証明書がファイル<ファイル名.cer>に保存されました

### 2-2. ASP.NETの公開鍵証明書をエクスポート

これは、証明書マネージャー(cermgr.msc)を開きウィザードに従ってエクスポートします。  
※詳しくは以下を参照して下さい。  
[ASP.NET SSL証明書についてのエクスポートを参照](../DotNet/AspNetSsl)  

<br />

## 3. 公開鍵証明書をJava(JVM)のキーストアへ登録する

JavaのアプリケーションでHTTPS通信を行うためには、Java(JVM)が管理するキーストア`cacerts`ファイルへこの証明書を登録  
しなければなりません。JVMのセキュリティに引っかかるからです。  
エクスポートした公開鍵証明書(.cer)をJava(JVM)が管理するキーストア`cacerts`ファイルへ登録します。  
※KeyToolがあるディレクトリからコマンドを実行していますので相対Pathの`../lib/security/cacerts`としています。 
※Java6以降はimportオプションではなく`-importcert`とする様です。  
※aliasは別名と呼ばれますがキーに対する名前なので何でも良いです。  

**2-1.の公開鍵証明書登録例**  

    $ keytool -importcert -trustcacerts -file <ファイルPath>.cer -keystore ../lib/security/cacerts -alias <別名>
    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:
    所有者: CN=<ホスト名(ドメイン名)>, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=jp
    発行者: CN=<ホスト名(ドメイン名)>, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=jp
    シリアル番号: xxxxxxxx
    有効期間の開始日: Tue Apr 14 16:45:38 JST 2020終了日: Wed Apr 14 16:45:38 JST 2021
    証明書のフィンガプリント:
             SHA1: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
             SHA256: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
    署名アルゴリズム名: SHA256withRSA
    サブジェクト公開鍵アルゴリズム: 2048ビットRSA鍵
    バージョン: 3

    拡張:

    #1: ObjectId: 2.5.29.xx Criticality=false
    SubjectKeyIdentifier [
    KeyIdentifier [
    0000: xx xx xx xx xx xx xx xx   xx xx xx xx xx xx xx xx  <..../..e.Ok. ..
    0010: xx xx xx xx                                        ..?.
    ]
    ]

    この証明書を信頼しますか。 [いいえ]:  y
    証明書がキーストアに追加されました

**2-2.の公開鍵証明書登録例**  

    $ keytool -importcert -trustcacerts -file <ファイルPath>.cer -keystore ../lib/security/cacerts -alias <別名>
    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。デフォルトパスワードはchangeit)
    所有者: CN=localhost
    発行者: CN=localhost
    シリアル番号: xxxxxxxxxxxxxxx
    有効期間の開始日: Thu Apr 09 11:32:24 JST 2020終了日: Fri Apr 09 11:32:24 JST 2021
    証明書のフィンガプリント:
             SHA1: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
             SHA256: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
    署名アルゴリズム名: SHA256withRSA
    サブジェクト公開鍵アルゴリズム: 2048ビットRSA鍵
    バージョン: 3

    拡張:

    #1: ObjectId: 1.3.6.1.4.1.311.xx.x.x Criticality=false
    0000: 01                                                 .


    #2: ObjectId: 2.5.29.xx Criticality=xxx
    BasicConstraints:[
      xxxxx
      xxxxx
    ]

    #3: ObjectId: 2.5.29.xx Criticality=xxx
    ExtendedKeyUsages [
      xxxxx
    ]

    #4: ObjectId: 2.5.29.xx Criticality=xxx
    KeyUsage [
      xxxxx
      xxxxx
    ]

    #5: ObjectId: 2.5.29.xx Criticality=xxx
    SubjectAlternativeName [
      DNSName: xxxxx
    ]

    この証明書を信頼しますか。 [いいえ]:  y
    証明書がキーストアに追加されました

※.cerでない.crtや.p12ファイルなどで登録を行おうとすると`java.lang.Exception: 入力はX.509証明書ではありません`と言う  
エラーが出ますので`.cer`ファイルで行う事。

<br />

## 4. Javaのキーストアに登録されている内容を表示

Javaのキーストア`cacerts`に登録されている内容を表示します。  
※-alias &lt;別名> オプションを指定しなければ全件表示されます。  

    $ keytool -list -v -alias <別名> -keystore ../lib/security/cacerts

**aliasに対する証明書などが存在した場合**  
cacertsに登録した時の内容が表示されます。  

    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。デフォルトパスワードはchangeit)
    別名: <別名>
    作成日: 2020/04/11
    エントリ・タイプ: trustedCertEntry

    所有者: CN=localhost
    発行者: CN=localhost
    シリアル番号: xxxxxxxxxxxxxxx
    有効期間の開始日: Thu Apr 09 11:32:24 JST 2020終了日: Fri Apr 09 11:32:24 JST 2021
    証明書のフィンガプリント:
             SHA1: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
             SHA256: xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
    署名アルゴリズム名: SHA256withRSA
    サブジェクト公開鍵アルゴリズム: 2048ビットRSA鍵
    バージョン: 3

    拡張:

    #1: ObjectId: 1.3.6.1.4.1.311.xx.x.x Criticality=false
    0000: 01                                                 .


    #2: ObjectId: 2.5.29.xx Criticality=xxx
    BasicConstraints:[
      xxxxx
      xxxxx
    ]

    #3: ObjectId: 2.5.29.xx Criticality=xxx
    ExtendedKeyUsages [
      xxxxx
    ]

    #4: ObjectId: 2.5.29.xx Criticality=xxx
    KeyUsage [
      xxxxx
      xxxxx
    ]

    #5: ObjectId: 2.5.29.xx Criticality=xxx
    SubjectAlternativeName [
      DNSName: xxxxx
    ]

**aliasに対する証明書などが無い場合**  

    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。デフォルトパスワードはchangeit)
    keytoolエラー: java.lang.Exception: 別名<aspnet>は存在しません
    java.lang.Exception: 別名<aspnet>は存在しません
            at java.base/sun.security.tools.keytool.Main.doPrintEntry(Main.java:1972)
            at java.base/sun.security.tools.keytool.Main.doCommands(Main.java:1234)
            at java.base/sun.security.tools.keytool.Main.run(Main.java:398)
            at java.base/sun.security.tools.keytool.Main.main(Main.java:391)

<br />

## 5. Javaのキーストアに登録されている証明書などを削除

キーストアーに登録されているSSL証明書を削除します。  
※成功すると何も表示されませんが削除されています。2.のコマンドで確認してみて下さい。  

    $ keytool -delete -alias <別名> -keystore ../lib/security/cacerts
    警告: cacertsキーストアにアクセスするには-cacertsオプションを使用してください
    キーストアのパスワードを入力してください:(※パスワードは表示されないが構わず入力。デフォルトパスワードはchangeit)

* * *
