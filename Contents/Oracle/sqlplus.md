---
layout: default
title: ":bar_chart: Oracle Database"
description: ":pen: Oracle Databaseコマンド"
date: "2020/05/17"
lastmod: "2020/05/17"
---

### 1. SID、ユーザー確認

```:prompt
def
```

### 2. インスタンス名の確認

```:prompt
select instance_name from v$instance;
または
show parameter instance_name
```

### 3. パスワードの変更

オラクルのパスワードはデフォルトで180日に設定されている。  

3-1. デフォルトプロファイルのパスワード有効期限を無効にする。  

```:prompt
alter profile default limit password_life_time unlimited;
```

3-2. ユーザーのパスワードを再設定する。  

```:prompt
alter user ユーザ名 identified by 新パスワード;
```

※ロックされたアカウントの解除方法。  

```:prompt
alter user ユーザ名 account unlock;
```

### 4. ユーザーに権限を付与

```:prompt
grant connect,resource to xxx;
```

### 5. ユーザーの権限を無くす

```:prompt
grant resource from xxx;
```

### 6. 他のユーザーに権限を付与。

権限を許すユーザーでログイン

```:prompt
grant select on テーブル名 to 他のユーザー名;
```

* * *
