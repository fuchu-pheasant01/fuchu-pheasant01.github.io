---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":black_nib: .NETにおけるスレッド、非同期の使い方"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 1. Threadクラスによるスレッド

## 2. BackgroundWorkerクラスによるスレッド

UWPでは廃止となった模様。  

## 3. System.Threading.Tasks.Taskクラス(非同期)によるスレッド

.NET Framework4.0で導入されTPL(タスク並列ライブラリ)と呼ばれる。  
これは、スレッドとスレッドプールを直接操作せずタスクと呼ばれるものを生成し、スレッドとスレッドプールの  
管理や実行順序を自動的に任して行われる。  
タスクをスタートさせるとスレッドプールのキューに自動で投入されます。  

-   スタート方法1。引数、戻り値なし、ラムダ式。.NET Framework4.0以降。  

```vb
Dim task1 As Task
task1 = New Task(Sub()
                   '非同期で実行
                   System.Threading.Thread.Sleep(2000)
                 End Sub)
task1.Start()
```

-   スタート方法2。引数、戻り値なし、ラムダ式。.NET Framework4.5以降。  

```vb
Dim task1 As Task
task1 = Task.Run(Sub()
                   '非同期で実行
                   System.Threading.Thread.Sleep(2000)
                 End Sub)
```

-   スタート方法3。引数、戻り値なし、ラムダ式。.NET Framework4.0以降。  

```vb
Dim task1 As Task
task1 = Task.Factory.StartNew(Sub()
                                '非同期で実行
                                System.Threading.Thread.Sleep(2000)
                              End Sub)
```

-   タスクの完了を待つ方法１。ブロックして待つ。  

```vb
Dim task1 As Task
'この間でタスクのスタートをしておく。
task1.Wait()
```

-   タスクの完了を待つ方法2。ポーリングして待つ。オブジェクトのインスタンス作成が必要。  

```vb
Dim task1 As Task
task1 = New Task(Sub()
                   '非同期で実行
                   System.Threading.Thread.Sleep(2000)
                 End Sub)
task1.Start()
Do While(Not task1.IsCompleted)
  'task1の完了まで繰り返し
Loop
```

-   複数のタスクの完了を待つ方法。ブロックして待つ。  

```vb
Dim task1 As Task
task1 = Task.Run(Sub()
                   '非同期で実行
                   System.Threading.Thread.Sleep(1000)
                 End Sub)

Dim task2 As Task
task2 = Task.Run(Sub()
                   '非同期で実行
                   System.Threading.Thread.Sleep(2000)
                 End Sub)

Task.WaitAll(task1, task2)
```

-   いずれかのタスクの完了を待つ方法。ブロックして待つ。残りのタスクは投げっぱなしとなる?。  

```vb
Task.WaitAny(task1, task2)
```

## 4. async/awaitを使った非同期メソッド

-   #### `await`
    await修飾子が付いた非同期タスク呼び出し以降を一旦停止してそれ以降のメソッド内の処理は予約待ち状態にし、  
    そのメソッドを一旦抜けた形にする。呼び出したスレッドを止めることなく非同期タスク側も実行する仕組み。  
    非同期タスク側が終了した後await以降(そのメソッド内の残り)の処理が実行される。  

-   #### `async`
    async修飾子は非同期で実行したいタスクを生成したいメソッドに付ける。  
    async修飾子が付いたから非同期ではなく呼び出し側は同期メソッド。その中のawaitが付いたタスクが非同期メソッド。  
    よって、asyncが付いてもawaitが無いメソッドはasyncが付いていないものと同じとなる。  

・非同期で実行するタスクを生成し、同期の様にTask.wait()で待つ例  
※同期でwaitする例はその間メインスレッドが止まるのでまず無い気がするが・・・。  

```vb
Private Sub method()

  SyncMethod()
  System.Console.WriteLine("SleepMethod終了、SyncMethod終了後に実行される。")

End Sub

Private Sub SyncMethod()

  Dim task As Task = task.Run(Sub()
                                SleepMethod()
                              End Sub)
  task.Wait()
  System.Console.WriteLine("SleepMethod終了後に実行される。")
  
End Sub

Private Sub SleepMethod()

  System.Threading.Thread.Sleep(2000)
  
End Sub
```

・非同期で実行するタスクを生成するasync/awaitの例  

```vb
Private Sub method()

  AsyncMethod()
  System.Console.WriteLine("SleepMethodを待たずに実行される。")

End Sub

Private Async Sub AsyncMethod()

  Await Task.Run(Sub()
                   SleepMethod()
                 End Sub)
  System.Console.WriteLine("SleepMethod終了後に実行される。")
  
End Sub

Private Sub SleepMethod()

  System.Threading.Thread.Sleep(2000)
  
End Sub
```

・非同期で実行するタスクを生成するTaskクラスのみの例(async/awaitが使えない代用例)  

```vb
Private Sub method()

  AsyncMethod()
  System.Console.WriteLine("task呼び出し後停止せずに実行される。")

End Sub

Private Sub AsyncMethod()

  Dim task As Task = task.Run(Sub() 'このタスクは止めず非同期で実行することによりメインスレッドを止めない。
                              Dim task2 As Task = Task.Run(Sub()
                                                            SleepMethod()
                                                           End Sub)
                              task2.Wait() 'await代用。メインスレッドを止めれないのでTaskの中にTaskを作り止める。
                              System.Console.WriteLine("SleepMethod終了後(task2終了後)に実行される。")
                              End Sub)
  
End Sub

Private Sub SleepMethod()

  System.Threading.Thread.Sleep(2000)
  
End Sub
```

-   #### `タスクのキャンセル`
    非同期で実行されているタスクをキャンセルする方法  

```vb
Private cts As System.Threading.CancellationTokenSource

Private Async Sub method()

  cts = New Threading.CancellationTokenSource
  Try
    Await MugenLoopCancel(cts.Token)
  Catch ex As System.OperationCanceledException
    'この例外は、スレッドによって実行されていた操作が取り消されたときにそのスレッドでスローされます。
    'CancellationTokenSource.Cancel()メソッドを実行したらOperationCanceledExceptionが発生するが、
    'このためだけの専用例外ではなくスレッドが取り消された場合全般の例外です。
    System.Console.WriteLine("強制終了されました。")
  End Try

End Sub

Private Async Function MugenLoopCancel(ByVal ct As System.Threading.CancellationToken) As Task

  'キャンセルされるまでループしつづける非同期メソッド
  Do While (1)
    ct.ThrowIfCancellationRequested()
    Await Task.Delay(2000)
  Loop

End Function

Private Sub StopMethod()

  '非同期処理をキャンセルする。
  cts.Cancel()
  
End Sub
```

## 5. Parallel.Forによる並列処理

* * *
