---
layout: default
title: ":bar_chart: Microsoft Access"
description: ":page_with_curl: Microsoft Accessその他"
date: "2020/05/17"
lastmod: "2020/10/21"
---

## 1. Microsoft Accessの利用について

-   Microsoft Accessファイルの最大容量は2Gバイトまででありそれを超えて使用できません。また、2Gバイト近くになると壊れやすい。  
-   Microsoft Accessファイルをネットワークで共有できるユーザーは、最大255ユーザーまでとなっており50ユーザーからのアクセスは非推奨となっている様ですが10ユーザー程度  
    に抑えておいた方が無難な様です。  
-   Microsoft Accessファイルを置いて共有するコンピュータのOSは、Windows10などのクライアントOSではなくWindows Serverなどのサーバー向けOSを利用する方が壊れにくい。  
-   バックアップは定期的に行う。  
-   Microsoft Accessファイルは最適化し不要な領域を削除しファイルサイズを小さくする事ができパフォーマンスを向上する事ができる。  
    定期的に行うことにより壊れにくくする。  

[データベースを最適化および修復する](https://support.microsoft.com/ja-jp/office/%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E3%82%92%E6%9C%80%E9%81%A9%E5%8C%96%E3%81%8A%E3%82%88%E3%81%B3%E4%BF%AE%E5%BE%A9%E3%81%99%E3%82%8B-6ee60f16-aed0-40ac-bf22-85fa9f4005b2)  

<br />

## 2. ldb、laccdbロックファイルについて

[Access のロックファイル (laccdb および .ldb) の概要](https://docs.microsoft.com/ja-jp/office/troubleshoot/access/lock-files-introduction)  
[Office Access データベースを排他モードで開いたユーザーを確認する](https://docs.microsoft.com/ja-jp/office/troubleshoot/access/determine-shared-resources-use)  

Microsoft Accessファイル(.mdbや.accdbなど)を使用している間は使用中を表すldb、laccdbロックファイルが作成されAccessファイルを閉じると削除されます。  
このロックファイルには使用したコンピュータ名などのコンピュータに関する情報が含まれますがこれは**共有モード**と呼ばれるモードで開いた場合に限ります。  
Microsoft Accessファイルは`共有モード`と`排他モード`と呼ばれるモードが存在します。  
共有モードでは、ロックファイルにより管理されますが排他モードでAccessファイルを使用している間は他のユーザーはAccessファイルを開く事ができません。  
以下のメッセージが表示されます。  

    '<データベース .mdb> ' を使用できませんでした <path> \ 。ファイルは既に使用されています。
    または、
    '<データベース .accdb> ' を使用できませんでした <path> \ 。ファイルは既に使用されています。

また、排他モードではロックファイルは作成されませんので誰がファイルを開いたかの情報は分かりません。  
共有モードでのロックファイルは、使用したコンピュータの情報が含まれますがAccessファイルを閉じる事により必ずしも情報が削除されるとは限りません。  
よって、使用した事のあるユーザーではありますが**現在使用中のユーザーではありません**のでロックファイルによりユーザーを特定する事は出来ない様です。  

> #### Microsoft抜粋
>
> ユーザーが共有データベースを閉じても、ユーザーのエントリはロックファイルから削除されません。 ただし、別のユーザーがデータベースを開いたときに、  
> ユーザーのエントリが上書きされることがあります。 つまり、ロックファイルだけを使用して、現在データベースを使用しているユーザーを特定することは  
> できません。  

<br />

## 3. .NETアプリでSQLの読み書きで同時実行違反エラーについて

列名が`半角のNo`や`半角の数字のみ`や`半角のID`(半角IDについてはメモを無くした＆覚えがない)の名前を付けていると、  
.NETアプリのOLEDB接続で.mdbファイルの読み書きをすると同時実行違反というエラーが出る。  
解決方法があるのか分からないがこの様な列名を使わない事で対策する以外方法が無かったので新規でテーブル物理名を決める  
時にはこの様な名前にしない事が良いと思われる。  
.NETアプリでOLEDB接続のOleDbDataAdapterクラスを、DataTableクラスに格納しその変更結果をUpdate()メソッドでデータベース  
に反映する時だけに起こる症状だった気がする。  
OleDbCommandクラスのExecuteNonQuery()メソッドで更新する場合では発生しなかったかもしれない。  
記憶があいまい＆その後未確認。  

<br />

## 4. Accessの.accdbを.NETアプリで使用するには2007用のドライバが必要

Access2013で作成した.accdbファイルを.NETのアプリで使う場合2010用のドライバではうまくいかず(エラー内容は当時控えていない)  
Access2007用のAccessドライバーでなければならない事があった気がする。  
また、OLEDB接続での接続文字列のバージョンは12.0でなければならなかった。Access2016以上では未確認。  

**2020/10/08日追記：当時、AccessランタイムとAccessデータベースエンジンドライバーを混同しており２つ存在すると知らなかったため、  
Accessランタイムの方をインストールして接続できないと思い込んだ可能性があるので本当はAccess2010データベースエンジンでも良いのかもしれない。  
しかし、2020/10/13日をもってOffice2010系のサポートが終了するので今後はAccess2016データベースエンジンドライバーを使う必要がある。**  

* * *
