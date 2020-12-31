---
layout: default
title: ":page_with_curl: Oracle Java"
description: ":heavy_check_mark: JUnit(単体テストツール)"
date: "2020/10/29"
lastmod: "2020/11/07"
---

## 0. はじめに

Javaの単体テストツールであるJUnitを使いメソッドなどの単独テストを行います。  
JUnitはJUnit4とJUnit5 Jupiterで変わっている事も多いのでなるべく両方の使い方を行っていきます。  

<br />

## 1. EclipseでJUnitを使う準備を行う

プロジェクトの中に「JUnitテスト・ケース」を選びテストクラスを追加するだけで良いが詳細は準備中  
実行は、プロジェクトまたは、テストクラスを右クリックし「実行」→「JUnitテスト」を選ぶとテストできる。  

<br />

## 2. アサーション(一致判定)

テストした結果が期待する値(こうなるであろう、こうなってほしい値)と一致するかの判定をしてくれるテストメソッドがあります。  
以下の例では、数量5と単価の100をかけた金額を計算し金額500を返すであろうという想定でテストします。  
また、テストするメソッドには`@Test`アノテーションを付けます。  

**テスト対象クラス**  

```java
public class SampleClass {
    public int kingaku(int su) {
        return su * 100;
    }
    
    public void throwUp() {
        throw new RuntimeException();
    }
}
```

### 2-1. assertEqualsメソッドでの一致判定テスト

#### JUnit4

```java
import org.junit.Test;
import static org.junit.Assert.*;

class TestClass1 {
    @Test
    public void assertTest() {
        var sc = new SampleClass();
        int kei = sc.kingaku(5);
    	assertEquals(500, kei);
    }
}
```

#### JUnit5を使う場合の変更点

JUnit4からJUnit5へ変更した場合の変更点です。  

```diff
+ import org.junit.jupiter.api.Test;
+ import static org.junit.jupiter.api.Assertions.*;
- import org.junit.Test;
- import static org.junit.Assert.*;
```

#### assertTrueメソッドを使う場合の変更点

assertEqualsの他にassertTrueで一致するかの判定テストができます。  
assertEqualsからassertTrueへ変更した場合の変更点です。  

```diff
+ assertTrue(kei == 500);
- assertEquals(500, kei);
```

#### assertThatメソッドを使う場合の変更点

assertThatメソッドはJUnit5では廃止されていますのでJUnit4のみ有効です。  
org.junit.Assert.assertThatメソッドは非推奨ですのでorg.hamcrest.MatcherAssert.assertThatを使います。  
また、判定で使うisメソッドも非推奨ですのでequalToを使用します。  
assertEqualsからassertThatへ変更した場合の変更点です。  

```diff
+ import static org.hamcrest.MatcherAssert.assertThat;
+ import static org.hamcrest.CoreMatchers.equalTo;
- import static org.junit.Assert.*;
+ assertThat(500, equalTo(kei));
- assertEquals(500, kei);
```

### 2-2. assertFalse()メソッドでNot判定テスト

assertThatは一致する場合成功ですがassertFalseは一致しなければ成功で一致すれば失敗とします。  

#### JUnit4

```java
import org.junit.Test;
import static org.junit.Assert.*;

class TestClass1 {
    @Test
    public void assertTest() {
        var sc = new SampleClass();
        int kei = sc.kingaku(4);
    	assertFalse(kei == 500);
    }
}
```

#### JUnit5を使う場合の変更点

```diff
+ import org.junit.jupiter.api.Test;
+ import static org.junit.jupiter.api.Assertions.*;
- import org.junit.Test;
- import static org.junit.Assert.*;
```

### 2-3. assertNullメソッドでnull判定テスト

assertNullメソッドでnullかどうかを判定テストする事ができます。  

#### JUnit4

```java
import org.junit.Test;
import static org.junit.Assert.*;

class TestClass1 {
    @Test
    public void assertTest() {
        var sc = new SampleClass();
        String str = null;
    	assertNull(str);
    }
}
```

#### JUnit5を使う場合の変更点

```diff
+ import org.junit.jupiter.api.Test;
+ import static org.junit.jupiter.api.Assertions.*;
- import org.junit.Test;
- import static org.junit.Assert.*;
```

### 2-4. assertThrowsメソッドで例外判定テスト

assertThrowsメソッドで例外が発生したかどうかを判定テストする事ができます。  

#### JUnit4

```java
import org.junit.Test;
import static org.junit.Assert.*;

class TestClass1 {
    @Test
    public void assertTest() {
        var sc = new SampleClass();
        //throwUpメソッドでRuntimeExceptionが発生したら成功、発生しなかったら失敗
    	assertThrows(RuntimeException.class, () -> sc.throwUp);
    }
}
```

<br />

## 3. パラメータ化テスト

固定的な定数を何種類か複数テストしたい場合に引数にセットして連続テストができます。  

### 3-1. @ParameterizedTestおよび@ValueSourceアノテーションを使いパラメータ化

以下の例では、testParams(3)→testParams(5)→testParams(6)を実行する事ができます。  
※複数の引数であるなら@ValueSource内をカンマで区切れば複数指定できます。  
※JUnit5からの機能ですのでJUnit4では使えません。  

#### JUnit5

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

class TestClass1 {
    @Test
    @ParameterizedTest
    @ValueSource(ints = {3, 5, 6})
    void testParams(int su) {
        var sc = new SampleClass();
        assertEquals(500, sc.kingaku(su));
    }
}
```

<br />

## 4. メソッドをカテゴリー別に分ける

テストを実行する単位をクラスごとに分けても良いがクラス内や複数クラスで目的のメソッドをグループ化してグループ単体でテストする事ができます。  
そのグループごとカテゴリー別に分ける方法です。  

#### JUnit4

JUnit4では、カテゴリー別に分けるために空のインターフェースクラスを作ります。今回はカテゴリーを２つ作ってみます。  

```java
public interface Cate1 {

}
```

```java
public interface Cate2 {

}
```

次にそれぞれのメソッドに`@Category`アノテーションを付けカテゴリー別に分けます。  

```java
import org.junit.Test;
import org.junit.experimental.categories.Category;

public class TestClass1 {
    @Test
    @Category(Cate1.class)
    pubic void testMethod1() {
        //プログラム
    }
    
    @Test
    @Category(Cate2.class)
    pubic void testMethod2() {
        //プログラム
    }
}
```

```java
import org.junit.Test;
import org.junit.experimental.categories.Category;

public class TestClass2 {
    @Test
    @Category(Cate2.class)
    @Category(Cate3.class)
    pubic void testMethod1() {
        //プログラム
    }
}
```

次にテストを走らせる実行単位クラスを作ります。  

```java
import org.junit.experimental.categories.Categories;
import org.junit.experimental.categories.Categories.IncludeCategory;
import org.junit.runner.RunWith;
import org.junit.runners.Suite.SuiteClasses;

@RunWith(Categories.class) //カテゴリー別に走らせます。
@SuiteClasses({TestClass1.class, TestClass2.class}) //対象のクラスはTestClass1とTestClass2です。
@IncludeCategory(Cate2.class) //@CategoryアノテーションにCate2.classを指定しているメソッドが対象です。
public class SuiteClass {

}
```

これで、`Cate2`はTestClass1のtestMethod2とTestClass2のtestMethod1だけをテスト実行できるようになります。  
プロジェクトのSuiteClassファイルの上で右クリックし「実行」→「JUnitテスト」をクリックし実行します。  

#### JUnit5

JUnit5では`@Tag`アノテーションを付けカテゴリー別に分けます。  
インターフェースを作らず文字列で指定できるのも良いです。JUnit4と違い複数クラスに渡ってグループ分けする場合の指定は別段ありません。  
「実行の構成」で「タグの抱合/除外」の「構成」ボタンを押し「include tags」に`category2`など@Tagアノテーションに付けた名前を付け実行します。  

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

public class TestClass1 {
    @Test
    @Tag("category2")
    pubic void testMethod1() {
        //プログラム
    }
}
```

また、以下のようにインタフェースファイルを別途作りオリジナルのアノテーションで指定する方法もあります。  

```java
import org.junit.jupiter.api.Tag;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Tag("category2")
public @interface Cate2 {

}
```

```java
import org.junit.jupiter.api.Tag;
import org.junit.jupiter.api.Test;

public class TestClass1 {
    @Test
    @Cate2 //オリジナルアノテーションで指定
    pubic void testMethod1() {
        //プログラム
    }
}
```

## 5. 表示名の変更

JUnit5限定ですが、`@DisplayName`アノテーションを使うとテストの表示名などにクラス名やメソッド名ではなく別の分かりやすい名前を付ける事ができます。  

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class TestClass1 {
    @Test
    @DisplayName("金額を計算するテスト")
    pubic void testKingaku() {
        //プログラム
    }
}
```

<br />

## 6. @Testアノテーションやテストクラスの前後で実行されるメソッド

### 6-1. @Beforeアノテーションと@Afterアノテーション

`@Test`アノテーションが付いたメソッドを実行するたびにその前後で必ず実行される事を目的としたアノテーションです。  

#### JUnit4

```java
import org.junit.After;
import org.junit.Before;
import org.junit.Test;

public class TestClass1 {
    @Before
    public void before() {
        System.out.println("Beforeが呼ばれました");
    }
    
    @After
    public void after() {
        System.out.println("Afterが呼ばれました");
    }
    
    @Test
    pubic void testMethod1() {
        System.out.println("Method1が呼ばれました");
    }
    
    @Test
    public void testMethod2() {
        System.out.println("Method2が呼ばれました");
    }
}
```

**実行結果**  

    Beforeが呼ばれました
    Method1が呼ばれました
    Afterが呼ばれました
    Beforeが呼ばれました
    Method2が呼ばれました
    Afterが呼ばれました

#### JUnit5での変更点

JUnit5では、@Beforeの代わりに`@BeforeEach`@Afterの代わりに`@AfterEach`アノテーションとなっています。  

```diff
+ import org.junit.jupiter.api.AfterEach;
+ import org.junit.jupiter.api.BeforeEach;
- import org.junit.After;
- import org.junit.Before;
+ @BeforeEach
+ @AfterEach
- @Before
- @After
```

### 6-2. @BeforeClassアノテーションと@AfterClassアノテーション

これは、`@Test`アノテーションが付いているメソッドのたびではなくテストクラスの前後１度だけ実行したいメソッドに付けます。  
また、`@BeforeClass`および`@AfterClass`アノテーションを使うメソッドには`static`を付けなければなりません。  

    org.junit.runners.model.InvalidTestClassError: Invalid test class 'test.sample.TestClass1':
    1. Method initclass() should be static

    訳：org.junit.runners.model.InvalidTestClassError：無効なテストクラス 'test.sample.TestClass1'：
    1。メソッドinitclass（）は静的である必要があります

#### JUnit4

```java
import org.junit.After;
import org.junit.AfterClass;
import org.junit.Before;
import org.junit.BeforeClass;
import org.junit.Test;

public class TestClass1 {
    @BeforeClass
    public static void beforeClass() {
        System.out.println("BeforeClassが呼ばれました");
    }
    
    @AfterClass
    public static void afterClass() {
        System.out.println("AfterClassが呼ばれました");
    }
    
    @Before
    public void before() {
        System.out.println("Beforeが呼ばれました");
    }
    
    @After
    public void after() {
        System.out.println("Afterが呼ばれました");
    }
    
    @Test
    pubic void testMethod1() {
        System.out.println("Method1が呼ばれました");
    }
    
    @Test
    public void testMethod2() {
        System.out.println("Method2が呼ばれました");
    }
}
```

**実行結果**  

    BeforeClassが呼ばれました
    Beforeが呼ばれました
    Method1が呼ばれました
    Afterが呼ばれました
    Beforeが呼ばれました
    Method2が呼ばれました
    Afterが呼ばれました
    AfterClassが呼ばれました

#### JUnit5での変更点

JUnit5では、@BeforeClassの代わりに`@BeforeAll`@AfterClassの代わりに`@AfterAll`アノテーションとなっています。  

```diff
+ import org.junit.jupiter.api.AfterAll;
+ import org.junit.jupiter.api.AfterEach;
+ import org.junit.jupiter.api.BeforeAll;
+ import org.junit.jupiter.api.BeforeEach;
- import org.junit.After;
- import org.junit.AfterClass;
- import org.junit.Before;
- import org.junit.BeforeClass;
+ @BeforeAll
+ @AfterAll
+ @BeforeEach
+ @AfterEach
- @BeforeClass
- @AfterClass
- @Before
- @After
```

* * *
