---
layout: default
title: ":bar_chart: Microsoft Access"
description: ":exclamation: Microsoft Access注意点・他"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. .NETアプリでSQLの読み書きで同時実行違反エラーについて

列名が`半角のNo`や`半角の数字のみ`や`半角のID`(半角IDについてはメモを無くした＆覚えがない)の名前を付けていると、  
.NETアプリのOLEDB接続で.mdbファイルの読み書きをすると同時実行違反というエラーが出る。  
解決方法があるのか分からないがこの様な列名を使わない事で対策する以外方法が無かったので新規でテーブル物理名を決める  
時にはこの様な名前にしない事が良いと思われる。  
.NETアプリでOLEDB接続のOleDbDataAdapterクラスを、DataTableクラスに格納しその変更結果をUpdate()メソッドでデータベース  
に反映する時だけに起こる症状だった気がする。  
OleDbCommandクラスのExecuteNonQuery()メソッドで更新する場合では発生しなかったかもしれない。  
記憶があいまい＆その後未確認。  

<br />

## 2. Accessの.accdbを.NETアプリで使用するには2007用のドライバが必要

Access2013で作成した.accdbファイルを.NETのアプリで使う場合2010用のドライバではうまくいかず(エラー内容は当時控えていない)  
Access2007用のAccessドライバーでなければならない。  
また、OLEDB接続での接続文字列のバージョンは12.0でなければならなかった。Access2016以上では未確認。  

* * *
