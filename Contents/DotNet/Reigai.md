---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":exclamation: .NETにおける例外処理"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. 例外の扱い方

### 1-1. 例外が発生する可能性を想定して作る方法

Try(try)内に例外が発生する可能性のあるプログラムを書きCatch(catch)内に例外が発生した時のプログラムを書く。 

```vb
Dim reader As System.IO.StreamReader
Try
  reader = System.IO.File.OpenText("c:\test.txt") '例外対象
Catch ex As FileNotFoundException
  '例外(ファイルが存在しなかった場合など)が発生した時の処理
End Try
```

### 1-2. 例外を明示的に発生させる方法

`Throw(trow) New(new) 例外クラス名`で例外を明示的に発生させる方法  

```vb
Throw New FileNotFoundException("明示的に例外を発生させました")
```

<br />

## 2. Throw(throw)により例外を発生させるタイミングを変える

Throwは例外を発生させる事ができたが新しくNew(new)せず単純にThrowと書くと現在保持している例外を再度呼び出す事ができる。  
Catchで例外をキャッチしCatch内で例外が発生した事をログに記入しその後例外を再度呼び出し通知させる事ができる。  
また、単なるThrowはCatch内に書く必要がある。  

```vb
Dim reader As System.IO.StreamReader
Try
  reader = System.IO.File.OpenText("c:\test.txt")　'例外対象
Catch ex As FileNotFoundException
  'ログを取る処理や画面に表示するなどの処理をここに書く
  'その後再度例外を呼び出す
  Throw 'これで例外を再度呼び出す
End Try
```

<br />

## 3. 例外の発生個所を知るStackTraceプロパティを見る

Throw New エラー名で例外を明示的に発生させるのであればそこが発生源となるがTry内に複数行(メソッドなども含む)あればどこが  
発生源か分からない場合がある。この様な場合はStackTraceプロパティで見る事ができる。  
では、コンソールアプリケーションで以下の様なプログラムを入力しデバッグで実行してみる。  

```vb
Imports System.IO

Module Program
  Sub Main(args As String())

    Dim reader As StreamReader
    Try
      Console.WriteLine("Hello World")
      reader = File.OpenText("c:\test.txt")
    Catch ex As FIleNotFoundException
      Debug.WriteLine(ex.StackTrace)
      Throw
    End Try

  End Sub
End Module
```

以下の様なメッセージがデバッグの出力に表示される。  
`line 9`に注目してみると`reader = File.OpenText("c:\test.txt")`の行が例外の発生源だという事が分かる。  

    at ConsoleTryCatch.Program.Main(String[] args) in C:\<省略>\Program.vb:line 9

<br />

## 4. 例外をメソッドの呼び出し元で再度Catchする

例外の発生源は呼び出すメソッドの内部で発生しているが呼び出し側でその例外をCatchする事もできる。  
しかし、この様な使い方でThrowする場合書き方によってはStackTraceプロパティの内容を書き換えてしまい実際の発生源がどこなのか  
分からなくなってしまう場合があるので注意が必要。と言うのも使い方が２つあり良く理解していない人が混乱しているからなのだと思う。  
まずは、例外発生源の行を保持し発生源を特定するために用いる方法で通常はこの使い方をする。  

```vb
Imports System.IO

Module Program
  Sub Main(args As String())

    Try
      ErrorMethod()
    Catch ex As FileNotFoundException
      Debug.WriteLine("Mainメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("MainメソッドでのStackTrace：" & ex.StackTrace)
    End Try

  End Sub

  Private Sub ErrorMethod()

    Dim reader As StreamReader
    Try
      reader = File.OpenText("c:\test.txt")
    Catch ex As FileNotFoundException
      Debug.WriteLine("ErrorMethodメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("ErrorMethodメソッドでのStackTrace：" & ex.StackTrace)
      Throw
    End Try

  End Sub

End Module
```

**デバッグ出力**  

    ErrorMethodメソッドでの例外メッセージ：Could not find file 'c:\test.txt'
    ErrorMethodメソッドでのStackTrace：at ConsoleTryCatch.Program.ErrorMethod() in C:\<省略>\Program.vb:line 19
    Mainメソッドでの例外メッセージ：Could not find file 'c:\test.txt'
    MainメソッドでのStackTrace：at ConsoleTryCatch.Program.ErrorMethod() in C:\<省略>\Program.vb:line 19
    at ConsoleTryCatch.Program.Main(String[] args) in C:\<省略>\Program.vb:line 7

ErrorMethodでのMessageプロパティとStackTraceプロパティ、MainでCatchしたMessageプロパティとStackTraceプロパティの内容をそれぞれ  
出力してみた。Mainの方はErrorMethodを呼び出す行が追加されているがどちらも行19で例外が発生している事が分かります。  
では、`Throw ex`の様に変数名を付けた方法で見てみる。  

```vb
Imports System.IO

Module Program
  Sub Main(args As String())

    Try
      ErrorMethod()
    Catch ex As FileNotFoundException
      Debug.WriteLine("Mainメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("MainメソッドでのStackTrace：" & ex.StackTrace)
    End Try

  End Sub

  Private Sub ErrorMethod()

    Dim reader As StreamReader
    Try
      reader = File.OpenText("c:\test.txt")
    Catch ex As FileNotFoundException
      Debug.WriteLine("ErrorMethodメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("ErrorMethodメソッドでのStackTrace：" & ex.StackTrace)
      Throw ex 'exを付ける
    End Try

  End Sub

End Module
```

**デバッグ出力**  

    ErrorMethodメソッドでの例外メッセージ：Could not find file 'c:\test.txt'
    ErrorMethodメソッドでのStackTrace：at ConsoleTryCatch.Program.ErrorMethod() in C:\<省略>\Program.vb:line 19
    Mainメソッドでの例外メッセージ：Could not find file 'c:\test.txt'
    MainメソッドでのStackTrace：at ConsoleTryCatch.Program.ErrorMethod() in C:\<省略>\Program.vb:line 23
    at ConsoleTryCatch.Program.Main(String[] args) in C:\<省略>\Program.vb:line 7

今度は、`Throw ex`を実行するまでは例外発生源は行19となっているが、`Throw ex`を実行すると例外内容を保持し発生源だけこの行に変える事が
できる様だ。ただし、余りこの様な使い方はしないかもしれないので注意が必要。  

<br />

## 5. 例外処理の悪い例

Try～Catchを正しく理解していれば分かる事では有るかもしれないがついついやってしまう悪い例です。  

### 5-1. 例外をCatchしたがスルーする

Catch内を何も書かない例です。何が原因でどこで発生した例外か分かりません。プログラムも次々と進み別のトラブルや別の不具合を発生させて  
しまうでしょう。逆に、目に見える症状が出なかった場合ユーザーにも分かりませんので気づくのも遅れます。  

```vb
Dim reader As StreamReader
Try
  reader = File.OpenText("c:\test.txt")
Catch ex As FileNotFoundException

End Try
```

### 5-2. メソッドの呼び出し側でCatchしThrowする

ErrorMethod側のCatch内でエラーログ出力やメッセージ表示を行ってもMainメソッド側ではCatchされませんのでCatch内のThrowは実行されません。  
ErrorMethod側のCatch内で何もせずMainメソッド側のCatch内にログ出力や表示、Throwを書いても再Throwされていませんので例外は握りつぶされます。  
理解している方には当然といえば当然だと思うが気づかない場合もある。  

```vb
Imports System.IO

Module Program
  Sub Main(args As String())

    Try
      ErrorMethod()
    Catch ex As FileNotFoundException
      Debug.WriteLine("Mainメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("MainメソッドでのStackTrace：" & ex.StackTrace)
      Throw
    End Try

  End Sub

  Private Sub ErrorMethod()

    Dim reader As StreamReader
    Try
      reader = File.OpenText("c:\test.txt")
    Catch ex As FileNotFoundException
      Debug.WriteLine("ErrorMethodメソッドでの例外メッセージ：" & ex.Message)
      Debug.WriteLine("ErrorMethodメソッドでのStackTrace：" & ex.StackTrace)
      'ここでThrowせず
    End Try

  End Sub

End Module
```

* * *
