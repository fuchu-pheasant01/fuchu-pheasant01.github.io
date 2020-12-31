---
layout: default
title: ":page_with_curl: .NET(C#、VB.NET)"
description: ":heavy_check_mark: MSTest(単体テストツール)"
date: "2020/11/01"
lastmod: "2020/11/07"
---

## 0. はじめに

<br />

## 1. 事前準備

新規プロジェクトの作成でMSTestまたは単体テストを選び作成する。  

<br />

## 2. アサーション(一致判定)

テストした結果が期待する値(こうなるであろう、こうなってほしい値)と一致するかの判定をしてくれるテストメソッドがあります。  
以下の例では、数量5と単価の100をかけた金額を計算し金額500を返すであろうという想定でテストします。  
また、テストするメソッドには`[TestMethod]`属性を付けます。  

**テスト対象クラス**  

```csharp
public class SampleClass {
    public int Kingaku(int su) {
        return su * 100;
    }
    
    public void ThrowUp()
    {
        throw new System.Exception();
    }
}
```

### 2-1. Assert.AreEqualメソッドでの一致判定テスト

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod]
    public void TestAreEqual()
    {
        var sc = new SampleClass();
        Assert.AreEqual(500, sc.Kingaku(2), "一致しませんでした");
    }
}
```

### 2-2. Assert.IsTrueメソッドでの一致判定テスト

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod]
    public void TestIsTrue()
    {
        var sc = new SampleClass();
        Assert.IsTrue(500 == sc.Kingaku(2), "一致しませんでした");
    }
}
```

### 2-3. Assert.IsNullメソッドでのNull判定テスト

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod]
    public void TestIsNull()
    {
        string str = null;
        Assert.IsNull(str, "Nullではありません");
    }
}
```

### 2-4. Assert.ThrowException&lt; T >で例外判定テスト

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod]
    public void TestThrows()
    {
        var sc = new SampleClass();
        Assert.ThrowsException<System.Exception>(() => sc.ThrowUp(), "エラーは発生しませんでした");
    }
}
```

<br />

## 3. パラメータ化テスト

固定的な定数を何種類か複数テストしたい場合に引数にセットして連続テストができます。  

### 3-1. DataTestMethodおよびDataRow属性を使いパラメータ化

以下の例では、TestParams(3)→TestParams(5)→TestParams(6)を実行する事ができます。  
※複数の引数であるならDataRow内をカンマで区切れば複数指定できます。  

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [DataTestMethod]
    [DataRow(3)][DataRow(5)][DataRow(6)]
    public void TestParams(int su)
    {
        var sc = new SampleClass();
        Assert.AreEqual(500, sc.Kingaku(su), "一致しませんでした");
    }
}
```

<br />

## 4. メソッドをカテゴリー別に分ける

テストを実行する単位をクラスごとに分けても良いがクラス内や複数クラスで目的のメソッドをグループ化してグループ単体でテストする事ができます。  
そのグループごとカテゴリー別に分ける方法です。  
`TestCategory`属性を使い文字列で指定するだけでカテゴリー別に分ける事ができます。

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod]
    [TestCategory("category1")]
    public void TestMethod1()
    {
        //プログラム
    }
    
    [TestMethod]
    [TestCategory("category2")]
    public void TestMethod2()
    {
        //プログラム
    }
}
```

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest2
{
    [TestMethod]
    [TestCategory("category2")]
    [TestCategory("category3")]
    public void TestMethod1()
    {
        //プログラム
    }
}
```

これで、`category2`はUnitTest1のTestMethod2とUnitTest2のTestMethod1だけをテスト実行できるようになります。  
テストエクスプローラーのグループ化アイコンをクリックし「特徴」を選択するとTestCategory属性で指定したカテゴリー別に分類されるので、テストしたい  
カテゴリーをクリックし「実行」アイコンを押しテストします。  

<br />

## 5. 表示名の変更

`[TestMethod]`属性の引数に文字列を指定するとテストの表示名などでメソッド名ではなく別の分かりやすい名前を付ける事ができます。  
`[TestClass]`属性や`[DataTestMethod]`属性では使えません。  

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
{
    [TestMethod("金額を計算するテスト")]
    public void TestKingaku()
    {
        //プログラム
    }
}
```

<br />

## 6. TestMethod属性やTestClass属性の前後で実行されるメソッド

### 6-1. TestInitialize属性とTestCleanup属性

`[TestMethod]`属性や`[DataTestMethod]`が付いたメソッドを実行するたびにその前後で必ず実行される事を目的とした属性です。  

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
    [TestInitialize]
    public void Before() {
        Console.WriteLine("TestInitializeが呼ばれました");
    }
    
    [TestCleanup]
    public void After() {
        Console.WriteLine("TestCleanupが呼ばれました");
    }
    
    [TestMethod]
    pubic void TestMethod1() {
        Console.WriteLine("TestMethod1が呼ばれました");
    }
    
    [TestMethod]
    public void TestMethod2() {
        Console.WriteLine("TestMethod2が呼ばれました");
    }
}
```

**実行結果**  

    TestInitializeが呼ばれました
    Method1が呼ばれました
    TestCleanupが呼ばれました
    TestInitializeが呼ばれました
    Method2が呼ばれました
    TestCleanupが呼ばれました

### 6-2. ClassInitialize属性とClassCleanup属性

`[TestMethod]`属性が付いているメソッドのたびではなく`[ClassInitialize]`属性はテストクラスの直前で発生する事を目的とした属性です。  
`[ClassCleanup]`属性はすべてのテストが終わった後に実行されますので対象のクラスのテストが終わった直後ではありません。  
すべてのクラスのテストが行われた後行われますのでタイミングとしては`[AssemblyCleanup]`に近いタイミングとなります。  
`[ClassInitialize]`属性および`[ClassCleanup]`属性を使うメソッドは、staticを付けなければなりません。  
また、`[ClassInitialize]`属性を使うメソッドの引数には`TestContext context`が必要です。  
逆に、`[ClassCleanup]`属性を使うメソッドの引数は不要です。  

    Method MsTestSample.UnitTest1.ClassBefore has wrong signature.
    The method must be static, public, does not return a value and should take a single parameter of type TestContext.
    Additionally, if you are using async-await in method then return-type must be Task.

    訳：メソッドMsTestSample.UnitTest1.ClassBeforeの署名が間違っています。
    メソッドは静的でパブリックである必要があり、値を返さず、TestContext型の単一のパラメーターを受け取る必要があります。
    さらに、メソッドでasync-awaitを使用している場合、return-typeはTaskである必要があります。

    Method MsTestSample.UnitTest1.ClassAfter has wrong signature.
    The method must be static, public, does not return a value and should not take any parameter.
    Additionally, if you are using async-await in method then return-type must be Task.

    訳：メソッドMsTestSample.UnitTest1.ClassAfterの署名が間違っています。
    メソッドは静的でパブリックである必要があり、値を返さず、パラメータをとらないようにする必要があります。
    さらに、メソッドでasync-awaitを使用している場合、return-typeはTaskである必要があります。

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
    [ClassInitialize]
    public static void ClassBefore(TestContext context)
    {
        Console.WriteLine("ClassInitializeが呼ばれました");
    }

    [ClassCleanup]
    public static void ClassCleanUp(TestContext context)
    {
        Console.WriteLine("ClassCleanupが呼ばれました");
    }

    [TestInitialize]
    public void Before() {
        Console.WriteLine("TestInitializeが呼ばれました");
    }
    
    [TestCleanup]
    public void After() {
        Console.WriteLine("TestCleanupが呼ばれました");
    }
    
    [TestMethod]
    pubic void TestMethod1() {
        Console.WriteLine("TestMethod1が呼ばれました");
    }
    
    [TestMethod]
    public void TestMethod2() {
        Console.WriteLine("TestMethod2が呼ばれました");
    }
}
```

**実行結果**  

    ClassInitializeが呼ばれました
    TestInitializeが呼ばれました
    Method1が呼ばれました
    TestCleanupが呼ばれました
    TestInitializeが呼ばれました
    Method2が呼ばれました
    TestCleanupが呼ばれました
    ClassCleanupが呼ばれました

### 6-3. AssemblyInitialize属性とAssemblyCleanup属性

テストの実行全体の前後で実行されることを目的とした属性です。  
`[AssemblyInitialize]`属性および`[AssemblyCleanup]`属性を使うメソッドは、staticを付けなければなりません。  
また、`[AssemblyInitialize]`属性を使うメソッドの引数には`TestContext context`が必要です。  
逆に、`[AssemblyCleanup]`属性を使うメソッドの引数は不要です。  

    Method MsTestSample.UnitTest1.AssemblyBefore has wrong signature.
    The method must be static, public, does not return a value and should take a single parameter of type TestContext.
    Additionally, if you are using async-await in method then return-type must be Task.

    訳：メソッドMsTestSample.UnitTest1.AssemblyBeforeの署名が間違っています。
    メソッドは静的でパブリックである必要があり、値を返さず、TestContext型の単一のパラメーターを受け取る必要があります。
    さらに、メソッドでasync-awaitを使用している場合、return-typeはTaskである必要があります。

    Method MsTestSample.UnitTest1.AssemblyAfter has wrong signature.
    The method must be static, public, does not return a value and should not take any parameter.
    Additionally, if you are using async-await in method then return-type must be Task.

    訳：メソッドMsTestSample.UnitTest1.AssemblyAfterの署名が間違っています。
    メソッドは静的でパブリックである必要があり、値を返さず、パラメータをとらないようにする必要があります。
    さらに、メソッドでasync-awaitを使用している場合、return-typeはTaskである必要があります。

```csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

[TestClass]
public class UnitTest1
    [AssemblyInitialize]
    public static void AssemblyBefore(TestContext context)
    {
        Console.WriteLine("AssemblyInitializeが呼ばれました");
    }

    [AssemblyCleanup]
    public static void AssemblyAfter()
    {
        Console.WriteLine("AssemblyCleanupが呼ばれました");
    }

    [ClassInitialize]
    public static void ClassBefore(TestContext context)
    {
        Console.WriteLine("ClassInitializeが呼ばれました");
    }

    [ClassCleanup]
    public static void ClassAfter(TestContext context)
    {
        Console.WriteLine("ClassCleanupが呼ばれました");
    }

    [TestInitialize]
    public void Before() {
        Console.WriteLine("TestInitializeが呼ばれました");
    }
    
    [TestCleanup]
    public void After() {
        Console.WriteLine("TestCleanupが呼ばれました");
    }
    
    [TestMethod]
    pubic void TestMethod1() {
        Console.WriteLine("TestMethod1が呼ばれました");
    }
    
    [TestMethod]
    public void TestMethod2() {
        Console.WriteLine("TestMethod2が呼ばれました");
    }
}
```

**実行結果**  

    AssemblyInitializeが呼ばれました
    ClassInitializeが呼ばれました
    TestInitializeが呼ばれました
    Method1が呼ばれました
    TestCleanupが呼ばれました
    TestInitializeが呼ばれました
    Method2が呼ばれました
    TestCleanupが呼ばれました
    ClassCleanupが呼ばれました
    AssemblyCleanupが呼ばれました

* * *
