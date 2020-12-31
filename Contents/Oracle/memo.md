---
layout: default
title: ":bar_chart: Oracle Database"
description: ":exclamation: Oracle Database注意点・他"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. .NETアプリの接続文字列について

Oracleのルール（SQLServerなどとは構造が違うため備忘録とする）  
１つのSIDには、１つのインスタンスと１つのデータベースが存在する。  
他のデータベースとは異なり、１つのインスタンスには１つのデータベースしか存在しない。  
11g expressではインスタンスは１つ。デフォルトはXE。standardやenterpriseでは、orcl。10gはora10g、9iはora9i。  
12cは、不明だが、おそらくorcl。18c以上は不明。  
コントロールパネルの管理ツールのサービスにoracleservice??の??がそのパソコンに作成されているインスタンス  
ユーザー＝スキーマとなる。他のデータベースは違う。sysならスキーマもsys。  
VisualStudioのConnectionStringでは、大文字、小文字も区別される。  
VisualStudioでの接続でtnsnames.oraファイルへSID情報を登録する方法はユーザー、パスワード、DataSourceだけでよい。  

**tnsnames.oraを使わない接続方法**  
（例）

```vb:sample.vb
Dim sb As New System.Text.StringBuilder
sb.Append("User Id=scott; Password=tiger;")
sb.Append("Data Source=(DESCRIPTION = (ADDRESS_LIST = ")
sb.Append("(ADDRESS = (PROTOCOL = TCP)(HOST = localhost)")
sb.Append("(PORT = 1521)))(CONNECT_DATA = (SERVER = DEDICATED)")
sb.Append("(SERVICE_NAME = ORCL)));")
conn.ConnectionString = sb.ToString
```

XEのDataSourceは`localhost:1521/XE`でないといけない。  

コマンドプロンプトでtnsping xx(SID)でtnsnames.oraに登録されているか確認できる。  

* * *
