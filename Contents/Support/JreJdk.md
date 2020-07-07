---
layout: default
title: ":newspaper: Oracle Java"
description: ":paperclip: Oracle Java Development Kit リリース、サポート表"
date: "2020/05/17"
lastmod: "2020/05/23"
---

## 0. はじめに

Oracle JDK(本家)を商用目的で利用するには有料でOracle社と契約する必要があります。非商用(個人向け)または開発目的向けに限り  
無料で使う事ができます。  
※OracleサイトからOracle JDKアップデート(本家用)をダウンロードできるが非商用(個人向け)または開発目的向けでこれが提供される  
のは`2020年12月31日`までとなります。  
JDK10以前では無料使用、サポートがありましたが、最終のJDK8の商用向け無償サポートは2019年01月31日で終了し2019年04月16日以降  
Oracle社のJavaのライセンスが変わりそれ以降のバージョンでの商用利用は有償となりました(非商用や開発は無料)。  
J2SE1.3は、2006年の1.3.1 Update 20を最後に、J2SE1.4は、2008年の1.4.2 Update 19を最後に無料サポートを終了しています。  
J2SE5.0は、2009年10月リリースのUpdate22を最後のUpdateとし2009年10月31日で無料サポートを終了しました。  
Java6は2013年2月の6u37リリースで無料サポートを終了しJava7は2015年04月30日で無償サポートを終了しています。  

また、商用利用も無料で使うには純正Oracle製ではなくAdopt製のOpenJDKやOracle製でもOpenJDK(サポート短い)など互換を使用する手もある。  
半年毎に新しいメジャーバージョンが出る。新しい長期サポート(LTS)版は６バージョン後(３年後)に出る。  

<br />

## 1. リリース、サポート  
[リリースノート](https://www.oracle.com/technetwork/java/javase/jdk-relnotes-index-2162236.html)  
[Oracle Java SE サポート・ロードマップ](https://www.oracle.com/technetwork/jp/java/eol-135779-ja.html)  
[Javaリリース](https://www.java.com/ja/download/faq/release_dates.xml)  
[Adopt OpenJDK サポート](https://adoptopenjdk.net/support.html)  
[RedHat OpenJDK サポート](https://access.redhat.com/ja/articles/1457743)  

| バージョン         | 提供         |      リリース日 | Oracle 無償<br />サポート期限 | Oracle Premier<br />サポート期限 | Oracle Extended<br />サポート期限 |    状態    | LTS |
| :------------ | :--------- | ---------: | --------------------: | -------------------------: | --------------------------: | :------: | :-: |
| 1.0.0         | Oracle JDK | 1996/01/23 |                    ?? |                       none |                        none |  **End** |  -- |
| 1.1.0         | Oracle JDK | 1997/02/19 |                    ?? |                       none |                        none |  **End** |  -- |
| 1.2.0         | Oracle JDK | 1998/12/08 |                    ?? |                       none |                        none |  **End** |  -- |
| 1.3.0         | Oracle JDK | 2000/05/08 |            2006/12/31 |                       none |                        none |  **End** |  -- |
| 1.4.0         | Oracle JDK | 2002/02/06 |            2008/10/31 |                       none |                        none |  **End** |  -- |
| 1.5.0         | Oracle JDK | 2004/09/30 |            2009/10/31 |                       none |                        none |  **End** |  -- |
| 1.5.0         | Oracle JDK | 2004/09/30 |            2009/10/31 |                       none |                        none |  **End** |  -- |
| 1.6.0         | Oracle JDK | 2006/12/11 |            2013/02/19 |                 2015/12/31 |                  2018/12/31 |  **End** |  -- |
| 1.6.0_37-b06  | Oracle JDK | 2013/02/19 |            2013/02/19 |                 2015/12/31 |                  2018/12/31 |  **End** |  -- |
| 1.6.0_211-b11 | Oracle JDK | 2018/10/16 |            2013/02/19 |                 2015/12/31 |                  2018/12/31 |  **End** |  -- |
| 1.7.0         | Oracle JDK | 2011/07/28 |            2015/04/30 |                 2019/07/31 |                  2022/07/31 |    now   |  -- |
| 1.7.0_80      | Oracle JDK | 2015/04/14 |            2015/04/30 |                 2019/07/31 |                  2022/07/31 |  Current |  -- |
| 1.7.0_261-b07 | Oracle JDK | 2020/04/14 |            2015/04/30 |                 2019/07/31 |                  2022/07/31 |  Current |  -- |
| 1.8.0         | Oracle JDK | 2014/03/18 |            2019/01/31 |                 2022/03/31 |                  2030/12/31 |    now   |  -- |
| 1.8.0_251-b08 | Oracle JDK | 2020/04/14 |                    -- |                 2022/03/31 |                  2030/12/31 |  Current |  -- |
| 9 + 181       | Oracle JDK | 2017/09/21 |            2018/03/20 |                 2018/03/20 |                        none |  **End** | non |
| 9.0.1 + 11    | Oracle JDK | 2017/10/17 |            2018/03/20 |                 2018/03/20 |                        none |  **End** | non |
| 9.0.4 + 11    | Oracle JDK | 2018/01/16 |            2018/03/20 |                 2018/03/20 |                        none |  **End** | non |
| 10            | Oracle JDK | 2018/03/20 |            2018/09/25 |                 2018/09/25 |                        none |  **End** | non |
| 10.0.1 + 10   | Oracle JDK | 2018/04/17 |            2018/09/25 |                 2018/09/25 |                        none |  **End** | non |
| 10.0.2 + 13   | Oracle JDK | 2018/07/17 |            2018/09/25 |                 2018/09/25 |                        none |  **End** | non |
| 11            | Oracle JDK | 2018/09/25 |                    -- |                 2023/09/30 |                  2026/09/30 |    now   | LTS |
| 11.0.7 + 8    | Oracle JDK | 2020/04/14 |                    -- |                 2023/09/30 |                  2026/09/30 |  Current | LTS |
| 12 + 33       | Oracle JDK | 2019/03/19 |                    -- |                 2019/09/17 |                        none |  **End** | non |
| 12.0.1 + 12   | Oracle JDK | 2019/04/16 |                    -- |                 2019/09/17 |                        none |  **End** | non |
| 12.0.2 + 10   | Oracle JDK | 2019/07/16 |                    -- |                 2019/09/17 |                        none |  **End** | non |
| 13 + 13       | Oracle JDK | 2019/09/17 |                    -- |                 2020/03/31 |                        none |  **End** | non |
| 13.0.1 + 9    | Oracle JDK | 2019/10/15 |                    -- |                 2020/03/31 |                        none |  **End** | non |
| 13.0.2 + 8    | Oracle JDK | 2020/01/14 |                    -- |                 2020/03/31 |                        none |  **End** | non |
| 14 + 36       | Oracle JDK | 2020/03/17 |                    -- |                 2020/09/30 |                        none |    now   | non |
| 14.0.1 + 7    | Oracle JDK | 2020/04/14 |                    -- |                 2020/09/30 |                        none |  Current | non |
| 15            | Oracle JDK | 2020/09/xx |                    -- |                 2021/09/30 |                        none |   Next   | non |
| 17            | Oracle JDK | 2021/09/xx |                    -- |                 20xx/xx/xx |                  2029/09/xx | LTS Next | LTS |

* * *
