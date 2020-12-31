---
layout: default
title: ":page_with_curl: Oracle Java"
description: ":heavy_check_mark: Mockito(モックテストツール)"
date: "2020/10/30"
lastmod: "2020/11/03"
---

## 0. はじめに

単体テストを行うにあたって、テキストファイルやデータベースなどの資源やテストする対象のクラスを用意できていない間でも疑似的な仕組みを  
作る事により他の残りのテストを行うことができます。  
疑似的な仕組みとは、メソッドを実行した事にして戻り値に値を代入する所から再開させる事を言います。  

<br />

## 1. 事前準備

MavenRepositoryでmockito_core.jar、mockito-junit-jupiter.jar、byte-buddy.jar、byte-buddy-agent.jar、objenesis.jarなどをMavenProject  
でダウンロードしクラスパスへ追加するか、MavenプロジェクトやGradleで依存関係ファイルへ打ち込みビルドしますが準備中です。  

<br />

## 2. 疑似的な仕組みモックを作る

ここでは、テキストファイルなどの外部資源を扱うテストではなく対象のクラスが出来ていない事とします。  
例えば、100円の商品を数量でかける以下の様なクラスがまだ出来ていないとします。  
しかし、kingakuと言うメソッドは数量を入れたら計算をして金額が返ってくるメソッドという事は分かる事とします。  
ですので、クラスとメソッドのひな型だけは作成しておきます。  

**対象のテストクラスのひな型を仮作成する**  

```java
public class SampleClass {
    public int kingaku(int su) {
        return 0;
    }
}
```

### 2-1. Mockでモックを作る

Mockは、クラスすべてのpublicメソッドをモックします。  
テスト対象のクラスをモック宣言する方法は、`@Mock`アノテーションを使う方法と`org.mockito.Mockito.mock`を使う方法がありますがどちらでも  
良いです。  
`doReturn`を使い疑似的な仕組みを作ります。  
今回は、kingaku(1)メソッド(数量は1とする)を実行したら金額を500を返す仕組みを作ります。  
本来は数量1 x 100円なので100が返るはずで500が返る事はあり得ないのですがこの様なとんでもない使い方ができます。  

#### JUnit4 doReturnメソッドの場合

```java
import org.mockito.Mock;
//import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.doReturn;
import org.junit.Test;
import org.junit.runner.RunWith;
import static org.junit.Assert.*;
import org.mockito.junit.MockitoJUnitRunner;

@RunWith(MockitoJUnitRunner.class)
class TestClass1 {
    @Mock
    SampleClass sc;
    //または
    //private SampleClass sc = mock(SampleClass.class);
    
    @Test
    public void mockTest() {
        doReturn(500).when(sc).kingaku(1); //sc.kingaku(1)を実行すると500を返すように疑似的な仕組みを作ります。
        int kei = sc.kingaku(1); //変数keiには500が入ります。
        int zeikomi = kei * 1.1; //その後のテストができる。
    	assertEquals(500, kei); //検証するとkeiは500なので成功します。
    }
}
```

#### JUnit5の場合の変更点

```diff
+ import org.junit.jupiter.api.Test;
+ import static org.junit.jupiter.api.Assertions.*;
+ import org.junit.jupiter.api.extension.ExtendWith;
+ import org.mockito.junit.jupiter.MockitoExtension;
+ @ExtendWith(MockitoExtension.class)
- import org.junit.Test;
- import static org.junit.Assert.*;
- import org.junit.runner.RunWith;
- import org.mockito.junit.MockitoJUnitRunner;
- @RunWith(MockitoJUnitRunner.class)
```

#### whenメソッドの場合の変更点

```diff
+ import static org.mockito.Mockito.when;
- import static org.mockito.Mockito.doReturn;
+ when(sc.kingaku(1)).thenReturn(500);
- doReturn(500).when(sc).kingaku(1);
```

### 2-2. Spyでモックを作る

Spyは、テスト対象クラスのインスタンスを作りますがdoReturnやwhenで指定していないメソッド(引数がある場合は完全一致)はモックされておらず  
テスト対象のクラスそのものの動作をします。  
テスト対象のクラスをモック宣言する方法は、`@Spy`アノテーションを使う方法と`org.mockito.Mockito.spy`を使う方法がありますがどちらでも  
良いです。  

#### Spyでモックを作る場合の変更点

```diff
+ import org.mockito.Spy;
+ import static org.mockito.Mockito.spy;
- import org.mockito.Mock;
- import static org.mockito.Mockito.mock;
+ @Spy
+ SampleClass sc;
+ // または
+ private SampleClass sc = spy(new SampleClass());
- @Mock
- SampleClass sc;
- // または
- private SampleClass sc = mock(SampleClass.class);
```

### 2-3. doThrowメソッドで例外を発生させるモックを作る

#### JUnit4

```java
import org.mockito.Mock;
import static org.mockito.Mockito.doThrow;
import org.junit.Test;
import org.junit.runner.RunWith;
import static org.junit.Assert.*;
import org.mockito.junit.MockitoJUnitRunner;

@RunWith(MockitoJUnitRunner.class)
class TestClass1 {
    @Mock
    SampleClass sc;
    
    @Test
    public void mockTest() {
        //sc.kingaku(1)を実行するとRuntimeExceptionを返すように疑似的な仕組みを作ります。
        doThrow(RuntimeException.class).when(sc2).kingaku(1); 
        //検証するとkingakuメソッドでRuntimeExceptionが発生するので成功します。
		assertThrows(RuntimeException.class, () -> sc.kingaku(1));
    }
}
```

#### JUnit5の場合の変更点

```diff
+ import org.junit.jupiter.api.Test;
+ import static org.junit.jupiter.api.Assertions.*;
+ import org.junit.jupiter.api.extension.ExtendWith;
+ import org.mockito.junit.jupiter.MockitoExtension;
+ @ExtendWith(MockitoExtension.class)
- import org.junit.Test;
- import static org.junit.Assert.*;
- import org.junit.runner.RunWith;
- import org.mockito.junit.MockitoJUnitRunner;
- @RunWith(MockitoJUnitRunner.class)
```

* * *
