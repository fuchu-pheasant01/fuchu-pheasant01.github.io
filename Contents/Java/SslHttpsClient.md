---
layout: default
title: ":eyeglasses: Oracle Java、Apache Tomcat、Apache Maven、Spring Boot"
description: ":clipboard: HTTPS通信によるクライアント側設定手順"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

HTTPS通信でサーバーに証明書が付いている場合Javaのクライアント側から`https`で通信すると以下の様なエラーメッセージが表示  
され通信できません。  

-   **http通信からURIをhttpsと変えた単純なソース例(Java11以降)**  

```java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;

HttpClient httpClient = HttpClient.newBuilder()
	.version(HttpClient.Version.HTTP_1_1).build();
HttpRequest request = HttpRequest.newBuilder(URI.create("https://xxx:xxxx/"))
    .GET().build();
```

**JavaアプリケーションからASP.NET WebAPIへのアクセスエラー**  

    java.io.IOException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: 
    	unable to find valid certification path to requested target
    訳：java.io.IOException: PKIXパスの構築に失敗しました：sun.security.provider.certpath.SunCertPathBuilderException：
    	リクエストされたターゲットへの有効な証明書パスが見つかりません

    javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.
    	SunCertPathBuilderException: unable to find valid certification path to requested target
    訳：javax.net.ssl.SSLHandshakeException：PKIXパスの構築に失敗しました：sun.security.provider.certpath.
    	SunCertPathBuilderException：要求されたターゲットへの有効な証明書パスが見つかりません

    PKIX path validation failed: java.security.cert.CertPathValidatorException: signature check failed
    訳：PKIXパスの検証に失敗しました：java.security.cert.CertPathValidatorException：署名チェックに失敗しました

**JavaアプリケーションからSpring Bootへのアクセスエラー**  

    java.io.IOException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: 
    	unable to find valid certification path to requested target
    訳：java.io.IOException：PKIXパスの構築に失敗しました：sun.security.provider.certpath.SunCertPathBuilderException：
    	リクエストされたターゲットへの有効な証明書パスが見つかりません

    javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.
    	SunCertPathBuilderException: unable to find valid certification path to requested target
    訳：javax.net.ssl.SSLHandshakeException：PKIXパスの構築に失敗しました：sun.security.provider.certpath。
    SunCertPathBuilderException：要求されたターゲットへの有効な証明書パスが見つかりません

    sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.
    	SunCertPathBuilderException: unable to find valid certification path to requested target
    訳：sun.security.validator.ValidatorException：PKIXパスの構築に失敗しました：sun.security.provider.certpath。
    	SunCertPathBuilderException：要求されたターゲットへの有効な証明書パスが見つかりません

    sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to 
    	requested target
    訳：sun.security.provider.certpath.SunCertPathBuilderException：要求されたターゲットへの有効な証明書パスが見つかりません

サーバーに鍵と証明書がありますが、その情報をクライアントは持っていないからです。  
また、証明書をこちらで検索してみましたが有効な証明書を持っていませんという事の様です。  
これは、Java Virtual Machine(JVM)のセキュリティに引っかかったからです。  
サーバーへアクセスして認証が失敗した訳では無くJVMが対象のサーバーの証明書を持っていない所へのアクセスは認められませんとの事。  
すなわちJavaで作成したアプリから証明書付きのサーバーへHTTPSアクセスするためにはサーバーの証明書を登録する必要があります。  
登録とは、`キーストア`という鍵や証明書を管理している所で尚且つJVMが管理している`cacerts`というキーストアファイルへ証明書の登録  
を行うという事です。    

<br />

## 1. 公開鍵証明書をJavaのキーストアーへ登録して解決する

### 1-1. 公開鍵証明書の登録

`cacerts`というキーストアへ公開鍵証明書の登録を行う場合は**KeyTool**というアプリケーションを使います。  
具体的な方法は別途ページを用意していますので[KeyToolでJava(JVM)へSSL証明書登録手順のキーストアーへ登録](keytool)を参考に登録して下さい。  

登録後は、該当するサーバーへHTTPS通信を行ってみて下さい。  

### 1-2. 正しく行わなかった場合のエラー集

#### **CNとドメイン名(ホスト名)が一致しない場合のエラー**

姓名(CN:Common Name)とドメイン名(ホスト名)が違う場合に以下の様なエラーが発生します。  
姓名(CN:Common Name)はキーストアの中身の発行者や所有者などの所へ記載されています。  
※ローカルアクセスの場合は`localhost`、www.hogehoge.comの場合は`www.hogehoge.com`にする必要があります。  

    java.io.IOException: No name matching localhost found
    訳：java.io.IOException：localhostに一致する名前が見つかりません

    javax.net.ssl.SSLHandshakeException: No name matching localhost found
    訳：javax.net.ssl.SSLHandshakeException：localhostに一致する名前が見つかりません

    java.security.cert.CertificateException: No name matching localhost found
    java.security.cert.CertificateException：localhostに一致する名前が見つかりません

#### **CNとドメイン名(ホスト名)が一致しているがポート番号を間違えた場合のエラー**

姓名(CN:Common Name)とドメイン名(ホスト名)が一致しているがポート番号を`https://xxx:8443`ではなく`8433`と  
した場合のエラーです。  

    java.net.ConnectException: Connection refused: no further information
    訳：java.net.ConnectException：接続が拒否されました：これ以上の情報はありません

    java.net.ConnectException: Connection refused: no further information
    訳：java.net.ConnectException：接続が拒否されました：これ以上の情報はありません

#### **独自のキーストアを作成しCNが未入力の場合などのエラー**

独自のキーストア(.p12など)を作成し姓名(CN:Common Name)が未入力(Unknown)の場合以下の様なエラーが発生します。  

##### 「その中でも`https://Unknown:8443/api`などとドメイン名をUnknownとした場合」

未解決のアドレス例外という事ですがCNとドメイン名が一致しないエラーとは違う様です。  

    java.net.ConnectException
    java.net.ConnectException
    java.nio.channels.UnresolvedAddressException

##### 「その中でも`https://8443/api`などとドメイン名をポートのみとした場合」

    java.net.ConnectException: Network is unreachable: no further information
    訳：java.net.ConnectException：ネットワークに到達できません：これ以上の情報はありません

    java.net.ConnectException: Network is unreachable: no further information
    訳：java.net.ConnectException：ネットワークに到達できません：これ以上の情報はありません

    java.net.SocketException: Network is unreachable: no further information
    訳：java.net.SocketException：ネットワークに到達できません：これ以上の情報はありません

##### 「その中でも`https://:8443/rest`などとドメイン名をコロンとポートとした場合」

    java.lang.IllegalArgumentException: unsupported URI https：//：8443 / api
    訳：java.lang.IllegalArgumentException：サポートされていないURI https：//：8443 / api

#### **ASP.NET WebAPIサーバーへの登録で気を付ける事**

私の場合、当初キーストアへの登録を行いましたが以下の様なメッセージが表示されました。  

    javax.net.ssl.SSLHandshakeException: PKIX path validation failed: java.security.cert.
    	CertPathValidatorException: signature check failed
    訳：javax.net.ssl.SSLHandshakeException：PKIXパス検証に失敗しました：java.security.cert.
    	CertPathValidatorException：署名チェックに失敗しました

    sun.security.validator.ValidatorException: PKIX path validation failed: java.security.cert.
    	CertPathValidatorException: signature check failed
    訳：sun.security.validator.ValidatorException：PKIXパスの検証に失敗しました：java.security.cert.
    	CertPathValidatorException：署名チェックに失敗しました

-   **原因は以下の通りです**  
    私の場合、ASP.NET WebAPIで作成したサーバーと通信を行おうとしておりASP.NETのlocalhost証明書は複数ありました。  
    よって、別の証明書を登録していたのでミスマッチが起きこのエラーが出ておりました。  

また、以下の様な原因でもエラーが出るようです。  
1. 証明書の期限が切れている。  
2. Java 7u40以降、キーの長さが1024ビット未満のRSA鍵でのX.509証明書の使用は制限されているそうです。  
   キーの長さが少なくとも2048ビットの証明書を使用する必要があります。    

<br />

## 2. TrustManagerによる証明書の自作で検証をスルーして解決する

開発やデバッグなどで第三者機関を通さない自己署名証明書を使っている場合、`cacerts`への登録を行わず検証をスルーしてその先の開発を  
プログラムで解決できる方法もございます。その方法も紹介します。  

TrustMnagerクラスというクラスを使い以下の様な空のメソッドなどを作りSSLContextとSSLParametersメソッドをHTTPClientの作成の際に  
追加する事でそれを可能にします。  

```java
import java.net.http.HttpClient;
import java.net.http.HttpRequest;

import javax.net.ssl.SSLContext;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;
import javax.net.ssl.SSLParameters;
import java.security.KeyManagementException;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;

TrustManager[] transManagers = { new X509TrustManager() {
	public void checkClientTrusted(X509Certificate[] cert, String authType) throws CertificateException {
	}

	public void checkServerTrusted(X509Certificate[] cert, String authType) throws CertificateException {
	}

	public X509Certificate[] getAcceptedIssuers() {
		return new X509Certificate[0];
	}
} };

SSLContext sslcontext = null;
try {
	sslcontext = SSLContext.getInstance("SSL");
} catch (NoSuchAlgorithmException e1) {
	// 適応なエラー対応を行う
	e1.printStackTrace();
}
try {
	sslcontext.init(null, transManagers, new SecureRandom());
} catch (KeyManagementException e) {
	// 適応なエラー対応を行う
	e.printStackTrace();
}

var sslParams = new SSLParameters();
sslParams.setEndpointIdentificationAlgorithm("");
HttpClient httpClient = HttpClient.newBuilder()
	.sslContext(sslcontext)
	.sslParameters(sslParams)
	.version(HttpClient.Version.HTTP_1_1).build();
HttpRequest request = HttpRequest.newBuilder(URI.create("https://xxx:xxxx/"))
    .GET().build();
```

この様な方法で検証をパスする事ができます。  

* * *
