---
layout: default
title: ":smiley_cat: Apache Tomcat"
description: ":exclamation: Apache Tomcat 9.0.31以降のAJP接続について"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

Tomcatを9.0.31にした所、以下の様なエラーが発生してしまいました。  
Apache JServ Protocol(AJP)接続を8009ポートで行う時にエラーが出ており他で8009ポートが使われている場合に起きる  
ようですが今回は使われていませんでした。  
理由を調べてみると、2020/02/11にCVE-2020-1938(Ghostcat)という脆弱性が見つかったので対策を施した様です。  
[JPCERT](https://www.jpcert.or.jp/at/2020/at200009.html)  
[SIOS Security Blog](https://security.sios.com/vulnerability/tomcat-security-vulnerability-20200225.html)  

AJPは、HTTP ApacheとTomcatを自動で繋ぐものの様ですが、例えばTomcat9系の9.0.30以前のバージョンではデフォルトで  
すべてのIPアドレスの8009ポートへListenする様です。  
8.5系では8.5.50以前、7系では7.0.99以前のバージョンが対象になる様です。  
9.0.31、8.5.51、7.0.100からは対策が施されているようですが対策により以下の様なメッセージが出ます。   

    3月 08, 2020 10:34:19 午前 org.apache.catalina.util.LifecycleBase handleSubClassException
    重大: コンポーネント[Connector[AJP/1.3-8009]]の開始に失敗しました。
    org.apache.catalina.LifecycleException: プロトコルハンドラの起動に失敗しました
    	at org.apache.catalina.connector.Connector.startInternal(Connector.java:1038)
    	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183)
    	at org.apache.catalina.core.StandardService.startInternal(StandardService.java:438)
    	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183)
    	at org.apache.catalina.core.StandardServer.startInternal(StandardServer.java:930)
    	at org.apache.catalina.util.LifecycleBase.start(LifecycleBase.java:183)
    	at org.apache.catalina.startup.Catalina.start(Catalina.java:633)
    	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
    	at org.apache.catalina.startup.Bootstrap.start(Bootstrap.java:343)
    	at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:474)
    Caused by: java.lang.IllegalArgumentException: The AJP Connector is configured with secretRequired="true" but the secret attribute is either null or "". This combination is not valid.
    	at org.apache.coyote.ajp.AbstractAjpProtocol.start(AbstractAjpProtocol.java:264)
    	at org.apache.catalina.connector.Connector.startInternal(Connector.java:1035)
    	... 12 more

    3月 08, 2020 10:34:19 午前 org.apache.catalina.startup.Catalina start
    情報: サーバーの起動 [1,417]ms

これは、Tomcatの`server.xml`ファイル内のconnectorタグが以下の様に変わり`secretRequired`が指定されていないので  
デフォルトで`true`になったための様です。  

```xml
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

回避策は、AJP接続をしない場合は無効にする。  
AJP接続を行いたい場合は、このsecretRequiredをfalseと指定するとセキュリティ的に良くないのですが特定のIPアドレス限定で  
行う方法を回避策として挙げられている様です。  

**設定例**  

```xml
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" address="localhost" secretRequired="false"/>
```

これで、エラーは出なくなりました。  

* * *
