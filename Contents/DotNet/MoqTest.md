---
layout: default
title: ":page_with_curl: .NET(C#、VB.NET)"
description: ":heavy_check_mark: Moq(モックテストツール)"
date: "2020/11/02"
lastmod: "2020/11/02"
---

## 0. はじめに

<br />

## 1. 事前準備

NugetパッケージマネージャーでMoqをインストールしておく。  

<br />

## 2. Mockを使う

**対象のテストクラスのひな型を仮作成する**  
テスト対象のメンバーは、オーバーライドできるメンバーでなくてはならないのでインターフェースおよび抽象クラスでなければならない。  
また、インターフェースおよび抽象クラスはpublicでなければモックできない。  

```csharp
public interface ISampleClass {
    public int Kingaku(int su);
    public string Shohin { get; set; }
    public void ThrowUp();
}
```

### 2-1. Setupでモックを作る

```csharp
public class UnitTest1
{
    [TestMethod]
    public void TestSetup()
    {
        var sc = new Mock<ISampleClass>();
        sc.Setup(x => x.Kingaku(1)).Returns(500); //sc.Kingaku(1)を実行すると500を返すように疑似的な仕組みを作ります。
        var kei = sc.Object.Kingaku(1);
        Assert.AreEqual(500, kei, "一致しませんでした"); //検証するとkeiは500なので成功します。
    }
}
```

### 2-2. SetupGetでプロパティモックを作る

SetupGetを扱うメンバーは、プロパティでなければならない。  

```csharp
public class UnitTest1
{
    [TestMethod]
    public void TestSetupGet()
    {
        var sc = new Mock<ISampleClass>();
        sc.SetupGet(x => x.Shohin).Returns("りんご"); //sc.Shohinを実行するとりんごを返すように疑似的な仕組みを作ります。
        var name = sc.Object.Shohin;
        Assert.AreEqual("りんご", name, "一致しませんでした"); //検証するとnameはりんごなので成功します。
    }
}
```

### 2-3. SetupPropertyでプロパティモックを作り代入する

SetupPropertyを扱うメンバーは、プロパティでなければならない。  

```csharp
public class UnitTest1
{
    [TestMethod]
    public void TestSetupProperty()
    {
        var sc = new Mock<ISampleClass>();
        sc.SetupProperty(x => x.Shohin, "初期値"); //sc.Shohinに値を代入できるようにします。これが無いと代入してもnullとなる。
        sc.Object.Shohin = "りんご"; //りんごをsc.Shohinに代入する。
        var name = sc.Object.Shohin;
        Assert.AreEqual("りんご", name, "一致しませんでした"); //検証するとnameはりんごなので成功します。
    }
}
```

### 2-4. Setup().Throws&lt; T >で例外を発生させるモックを作る

```csharp
public class UnitTest1
{
    [TestMethod]
    public void TestSetupThrows()
    {
        var sc = new Mock<ISampleClass>();
        sc.Setup(m => m.ThrowUp()).Throws<System.Exception>(); //sc.ThrowUpを実行するとException例外を発生する仕組みを作ります。
        Assert.ThrowsException<System.Exception>(() => sc.Object.ThrowUp()); //検証するとException例外が発生するので成功します。
    }
}
```

* * *
