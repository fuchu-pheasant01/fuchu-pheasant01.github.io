---
layout: default
title: ":newspaper: Oracle Java"
description: ":paperclip: Oracle Java Development Kitライセンス"
date: "2020/10/16"
lastmod: "2020/10/23"
---

## 1. Oracle Java Development Kitのライセンスについて

本家Oracleのライセンスが2019/04/16日より変わっています。  
本家Oracle製品を使うには、有償の正規ライセンスを購入(商用利用も可)し正規製品版を使うか、無料のOTN開発者ライセンスに基づいて(商用利用不可)  
OTN(Oracle Technology Network)サイトで無料ダウンロードして使う方法があります。  

[Oracle Java SE ライセンスFAQ](https://www.oracle.com/technetwork/jp/java/javase/overview/oracle-jdk-faqs-5488066-ja.html)  

> #### Oracle抜粋
>
> #### Oracle JDK のライセンスに関する重要な変更

> 2019年4月16日のリリースより、Oracle JDKのライセンスが変更されました。  
> 新しいライセンス、Oracle Technology Network License Agreement for Oracle Java SEは、これまで提供してきた過去のバージョンのJDKの  
> ライセンスと大きく異なります。新しいライセンスでは、個人での利用や開発での利用などには無償で使用できます。しかし、以前のOracle JDK  
> ライセンスで許可されていたその他の目的には使用できなくなっている可能性があります。これらの製品をダウンロード、使用する前にライセンス  
> の内容を十分にご確認ください。FAQも合わせてご確認下さい。  

> 商用ライセンスおよびサポートは低コストのJava SE Subscriptionでご利用いただけます。  

> また、オラクルはjdk.java.netで最新のOpenJDKリリースをオープンソースのGPLライセンスで提供しています。  

### 1-1. OTN開発者ライセンスについて

[Oracle Technology Network License Agreement for Oracle Java SE](https://www.oracle.com/jp/downloads/licenses/javase-license1.html)  

<br />

## 2. Oracle OpenJDKのライセンスについて

[GNU GPL2 with the Classpath Exception](https://openjdk.java.net/legal/gplv2+ce.html)  

<br />

## 3. Adopt OpenJDKのライセンスについて

Adopt OpenJDKは、無料で商用利用が可能です。GPL v2 with Classpath Exceptionライセンスなので単なるGPLライセンスと違いJDKライブラリー(.jarなど)  
をクラスパスに設定してリンクとして使う場合はソースコードの開示は必要ないという解釈です。  

[Adopt OpenJDK License(s)](https://adoptopenjdk.net/about.html)  

> #### Adopt OpenJDK抜粋(日本語訳)
>
> #### ライセンス
>
> -   バイナリを生成するためのビルドスクリプトおよびその他のコード、Webサイト、およびその他のビルドインフラストラクチャは、[ApacheLicenseバー  
>     ジョン2.0](https://www.apache.org/licenses/LICENSE-2.0)でライセンスされています。  
> -   OpenJDKコード自体は、[GPL v2 with Classpath Exception](https://openjdk.java.net/legal/gplv2+ce.html)（GPLv2 + CE）を使用してGPLv2でライセンスされています。  
> -   Eclipse OpenJ9は、[いくつかのライセンス](https://github.com/eclipse/openj9/blob/master/LICENSE)の下でライセンスされています。  

> #### 使用上のFAQ
>
> 次のセクションは、正式な法的アドバイスとして解釈されるべきではありません。そのためには、資格のある弁護士を探してください。  
>
> 1.  AdoptOpenJDKバイナリを配布できますか？  
>     はい。  
> 2.  AdoptOpenJDKバイナリをアプリケーションにバンドルして配布できますか？  
>     はい。 GPLv2 + CEライセンスでは、これを行うことができます。  
> 3.  TCK / JCKの欠如がAdoptOpenJDKを法的リスクにさらしているのではないかと疑問に思う人もいます。 AdoptOpenJDKバイナリを配布すると（アプリの  
>       一部であるかスタンドアロンであるかに関係なく）、法的リスクにさらされますか？  
>     いいえ。AdoptOpenJDKには、OpenJDKと同じソースコードとマイナーパッチ（同じGPLv2 + CEライセンスの下）があります。

> #### サポート
>
> AdoptOpenJDKバイナリには、コミュニティサポートと商用サポートの両方があります。詳細については、[サポートページ](https://adoptopenjdk.net/support.html)を参照してください。  

<br />

## 4. Amazon Corretto(コレット)のライセンスについて

Amazon Correttoは、無料で商用利用が可能です。GPL v2 with Classpath Exceptionライセンスなので単なるGPLライセンスと違いJDKライブラリー(.jarなど)  
をクラスパスに設定してリンクとして使う場合はソースコードの開示は必要ないという解釈です。  
当初はAWSクラウドのみの使用と考えられていた様ですがそれ以外でも可能(他のクラウドやオンプレミスでも可)となっている。  
おまけに、Java11に至っては本家よりもサポートが長くなっている。  

[Amazon Corretto のよくある質問](https://aws.amazon.com/jp/corretto/faqs/)  

> #### Amazon抜粋
>
> #### 全般
>
> Q: Corretto の使用には費用がかかりますか?  
> A: Corretto は、オープンソースライセンスに基づいて Amazon から無償で配布されています。これは、Class Path Exception ([GPLv2とCPE](https://openjdk.java.net/legal/gplv2+ce.html))付きのGNU  
> 公衆利用許諾書バージョン 2 の条件のもと、ライセンスされています。使用や配布に対して Amazon が請求することはありません。  

> #### ライセンスとオープンソース
>
> Q: Corretto のライセンス条項は何ですか?  
> A: Corretto は、Class Path Exception ([GPLv2とCPE](https://openjdk.java.net/legal/gplv2+ce.html))付きのGNU公衆利用許諾書バージョン 2 の条件というOpenJDK と同じオープンソースライセンス  
> のもとリリースされています。CorrettoはOpenJDKを使うのと同じように使用していただけます。  

> Q: Amazon は OpenJDK にどのように貢献していますか?  
> A: Amazon は 2017 年に OpenJDK への貢献を始めました。これからもより多く、より複雑に貢献していく予定です。  

> Q: Corretto に貢献するにはどうすればよいですか?  

> A: Amazon では、コードを Corretto に取り込む方法として使用することによって OpenJDK プロジェクトへ貢献することを推奨しています。そうすることで、  
> OpenJDK コミュニティ全体がお客様の変更による恩恵を受けます。ビルドロジックなど、Corretto に固有の貢献である場合は、[GitHub](https://github.com/corretto)でコードを入手してく  
> ださい。AWS が問題を評価してプル要求します。  

* * *
