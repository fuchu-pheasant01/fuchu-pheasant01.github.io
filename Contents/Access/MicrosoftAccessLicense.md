---
layout: default
title: ":newspaper: Microsoft Access"
description: ":paperclip: Microsoft Accessライセンス"
date: "2020/10/08"
lastmod: "2020/10/12"
---

## 2. Accessランタイムについて

Microsoft Accessランタイムを使用するとコンピュータにAccessを完全にインストールしていないユーザーに対してもAccessアプリケー  
ションを配布することができます。  
Microsoft Accessランタイムは、デザインの編集やプログラムの開発はできませんがデータを操作する事ができるアプリケーションが  
含まれている様です。  
無料で再配布可能ですが、開発されたアプリケーション(Accessファイル)とAccess Runtimeはセットで配布する必要があります。  
※Microsoft Access 2003以前は、有償のMicrosoft Access Developer Edition(Extension)などに配布ライセンスが含まれており、  
ランタイムもセットアップキットを作成して配布へインストールしなければならなかったそうです。Access 2007からは不要。  

[Microsoft Access 2010 Runtime](https://www.microsoft.com/ja-jp/download/details.aspx?id=10910)  
[Microsoft Access 2010 Runtime SP2 32bit](https://www.microsoft.com/ja-jp/download/details.aspx?id=39643)  
[Microsoft Access 2010 Runtime SP2 64bit](https://www.microsoft.com/ja-jp/download/details.aspx?id=39644)  
[Microsoft Access 2013 Runtime](https://www.microsoft.com/ja-jp/download/details.aspx?id=39358)  
[Microsoft Access 2016 Runtime](https://www.microsoft.com/ja-jp/download/details.aspx?id=50040)  
[Microsoft 365 Runtime](https://support.microsoft.com/ja-jp/office/microsoft-365-access-%e3%83%a9%e3%83%b3%e3%82%bf%e3%82%a4%e3%83%a0%e3%82%92%e3%83%80%e3%82%a6%e3%83%b3%e3%83%ad%e3%83%bc%e3%83%89%e3%81%97%e3%81%a6%e3%82%a4%e3%83%b3%e3%82%b9%e3%83%88%e3%83%bc%e3%83%ab%e3%81%99%e3%82%8b-185c5a32-8ba9-491e-ac76-91cbe3ea09c9?ui=ja-jp&rs=ja-jp&ad=jp)  

> #### Microsoft抜粋
>
> アプリケーションとAccess Runtimeを一緒にパッケージ化して配布する必要があります。  

> Access 2010 Runtime を再配布するために特別な製品を購入する必要はありません。  
> 自由に再配布したり、ユーザーに対してダウンロード先を示したりすることができます。  

※Office 365は2020/04/22日、Microsoft 365と名称が変更されました。  
※Microsoft 365 Runtimeは、Access2010から2019で作成されたファイルへもアクセスできます。  

<br />

## 3. Accessデータベースエンジン再頒布可能コンポーネントについて

アプリケーション開発者が、ODBCやOLEDB接続でAccessファイルのデータへアクセスするためのドライバーです。  
無料で再配布可能ですが、以下のMicrosoft抜粋の指示に従う必要があります。  

[Microsoft Accessデータベース エンジン2010 再頒布可能コンポーネント](https://www.microsoft.com/ja-jp/download/details.aspx?id=10910)  
[Microsoft Accessデータベース エンジン2016 再頒布可能コンポーネント(US)](https://www.microsoft.com/en-us/download/details.aspx?id=54920)  
[Accessデータベースエンジン再頒布可能コンポーネントについて](https://docs.microsoft.com/ja-jp/office/troubleshoot/access/cannot-use-odbc-or-oledb)  
[Microsoft Access 2010 を使用したデータ プログラミング](https://docs.microsoft.com/ja-jp/previous-versions/office/ff965871(v=office.14))  

> #### Microsoft抜粋
>
> このダウンロードを実行すると、Microsoft Office Access(_.mdbおよび_.accdb)ファイルやMicrosoft Office Excel(_.xls、_.xlsx、  
> および\*.xlsb)ファイルなどの既存の Microsoft Officeファイルと、Microsoft SQL Serverなどの他のデータソースとの間のデータ転送  
> を簡単に行うためのコンポーネントがインストールされます。既存のテキスト ファイルへの接続もサポートされています。インストール  
> されたODBCドライバーおよびOLEDBドライバーは、アプリケーション開発者がOfficeファイル形式に対応したアプリケーションを開発する  
> 際に利用できます。  

> Access データベース エンジン再頒布可能コンポーネントは、以下の目的には使用できません。  

> 1.  Jet の全般的な代替としての使用。Jet の全般的な代替が必要な場合は、SQL Server Express Edition (英語版) が必要です。  
>     Aceの一般的な代替品としての使用。(Aceの一般的な代替品が必要な場合は、SQL Server Express Editionを使用する必要があります。)  

> 2.  サーバー側アプリケーション内での Jet OLEDB プロバイダーとしての使用。  

> 3.  一般的なワード プロセッサ、スプレッドシート、またはデータベース管理システムとしての使用。 つまり、ファイル作成の手段  
>     としての使用。Microsoft Office または Office オートメーションを使うと、Microsoft Office でサポートされるファイルを作成  
>     することができます。  

> 4.  システム サービスまたはサーバー側プログラム (コードがシステム アカウントの下で実行されるもの、複数のユーザー ID を同時  
>     に処理するもの、高度に再入可能で動作が不安定になるもの) による使用。これには、ユーザーがログインしていないときにタスク  
>     スケジューラーから実行されるプログラムや、ASP.NET などのサーバー側 Web アプリケーションから呼びだされるプログラム、COM+  
>     サービスの元で実行される分散コンポーネントなどがあります。  

* * *
