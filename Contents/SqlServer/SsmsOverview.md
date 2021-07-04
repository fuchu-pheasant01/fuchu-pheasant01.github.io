---
layout: default
title: ":newspaper: 概要・システム要件"
description: ":paperclip: Microsoft SQL Server Management Studio概要"
date: "2020/05/17"
lastmod: "2021/07/04"
---

## 0. はじめに  

Microsoft SQL Server Management Studioは、SQL ServerのデータベースをグラフィカルなGUIベースで操作する管理ツールである。  
SQL Serverデータベース本体ではないためCUIベースのSQLコマンドで操作できる知識がある場合は必ずしも必要ではない。  

SQL Server 2016より単独(SQL Server本体やServicePackやUpdateのリリースタイミングではない)のペースでリリースされるようになった。  
SQL Server 2014以前は、本体とリリースのタイミングが同じでExpress EditonのSQL Server Express with Advanced ServicesやSQL Server Express with Toolsなどでは本体と同梱  
されており単独で別途インストールする必要はないパッケージも存在する。  

[SQL Server Management Studio (SSMS) のダウンロード(US)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)  
[SQL Server Management Studio (SSMS) のダウンロード(日本語)](https://docs.microsoft.com/ja-jp/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)  

**※ダウンロードは日本語サイトでは反映が遅いのですでにサポート切れになっている場合が多い。US(米国)サイトで日本語版をダウンロードできるので最新版をダウンロードする。**  

> ### SQL Server Management Studio (SSMS) のダウンロード [Microsoft抜粋]  
> #### SSMS のダウンロード  

> 最新の一般提供 (GA) バージョンは SSMS 18.9.1 です。 以前の GA バージョンの SSMS 18 がインストールされている場合は、SSMS 18.9.1 をインストールするとそれが 18.9.1 に  
> アップグレードされます。  

> SSMS 18.x のインストールでは、17.x 以前のバージョンの SSMS がアップグレードまたは置き換えられることはありません。 SSMS 18.x は以前のバージョンとは別にサイド バイ サイド  
> でインストールされるので、両方のバージョンを使用できます。 ただし、"プレビュー" 版の SSMS 18.x がインストールされている場合は、それをアンインストールしてから SSMS 18.9.1  
> をインストールする必要があります。  

<br />

## 1. システム要件  

> ### SQL Server Management Studio (SSMS) のダウンロード [Microsoft抜粋]  
> #### SSMS のシステム要件抜粋  

> SSMS の現在のリリースでは、利用可能な最新の Service Pack と共に使用する場合、次の 64 ビット プラットフォームがサポートされます。  

> サポートされているオペレーティング システム:  

>   * Windows 10 (64 ビット) バージョン 1607 (10.0.14393) 以降  
>   * Windows 8.1 (64 ビット)  
>   * Windows Server 2019 (64 ビット)  
>   * Windows Server 2016 (64 ビット)  
>   * Windows Server 2012 R2 (64 ビット)  
>   * Windows Server 2012 (64 ビット)  
>   * Windows Server 2008 R2 (64 ビット)  

> サポートされているハードウェア:  

>   * 1.8 GHz 以上の x86 (Intel、AMD) プロセッサ。 デュアル コア以上を推奨  
>   * 2 GB の RAM (4 GB の RAM を推奨) (仮想マシン上で実行される場合は最小 2.5 GB)  
>   * ハードディスク領域: 最低でも 2 GB、最大 10 GB の使用可能な領域  

* * *
