---
layout: default
title: ":bar_chart: Microsoft SQL Server"
description: ":book: Microsoft SQLServerコマンド(sqlcmd)"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. sqlcmd起動

```:prompt
sqlcmd -E -S localhost\sqlexpress
```

\-Eはwindows認証

### 2. sqlcmd終了

```:prompt
exit
```

#### 3. データベース一覧表示

※オラクルとは違い、インスタンス内には複数のデータベースが作成できる。  
目的のインスタンスにログインする

```:prompt
select name from sys.databases
go
```

#### 4. ログインユーザー作成

※他のデータベースとは違い、インスタンスへの許可ユーザーであるログインユーザーと、  
各データベースへの許可ユーザーであるデータベースユーザーとがある。  
データベースユーザーを作成するには、ログインユーザーが必要。  
ログインユーザーだけ作成してもおそらく何もできない事から、データへアクセスするには、  
ログインユーザーとデータベースユーザーを作る必要がある。  
またSQLSERVERには、ログインを許可する権限のCONNECTロールなどがおそらくない。  

```:prompt
create login JisakuUser with password = 'pass';
go
```

#### 5. ログインユーザー削除

```:prompt
drop login JisakuUser;
go
```

* * *
