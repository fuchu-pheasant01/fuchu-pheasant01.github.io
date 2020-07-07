---
layout: default
title: ":eyeglasses: .NET(C#、VB.NET、C++/CLI、F#)"
description: ":closed_lock_with_key: ASP.NET Core Web API 認証について"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

ASP.NET Coreでの認証の実装方法です。  

<br />

## 1. BASIC認証(基本認証)

ASP.NET CoreでBasic認証で認証する事が非推奨なのか分かりませんが記事が少ないです。  
`AuthenticationMiddleware`クラスを実装する様ですが見た感じでは単に自作クラスを作り送られてきたBase64文字列を  
自分で取り出しデコードしユーザー名およびパスワードを一致するかも自分で実装しなければならない？様です。  
ただし、その呼出しはIApplicationBuilderのUseMiddleware&lt; T >メソッドで呼び出します。  
まず、**Startup**クラスの`configure`メソッドに以下を追加します。  

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // このメソッドの前後にコードがありますがどこの行へ入れるかは適材適所です。
    app.UseMiddleware<AuthenticationMiddleware>();
}
```

次に、AuthenticationMiddlewareクラスを実装します。  

```csharp
public class AuthenticationMiddleware
{
  private readonly RequestDelegate _next;
  private const string BASIC = "Basic ";
  private const string AUTHORIZATION = "Authorization";
  private const string AUTHENTICATION = "WWW-Authenticate";
  private const string USER = "user"; //サンプルユーザー(本当はDBなどに格納しそこから呼び出し)
  private const string PASS = "passuser"; //サンプルパスワード

  public AuthenticationMiddleware(RequestDelegate next)
  {
	_next = next;
  }

  public async Task Invoke(HttpContext context)
  {
	// クライアントからのAuthorizationリクエストヘッダーを取り出し
	string ReqHead = context.Request.Headers[AUTHORIZATION];
	if (ReqHead != null && ReqHead.StartsWith(BASIC))
	{
	  // クライアントからのリクエストヘッダーの頭文字Basic を取り除く(Base64のみの文字列とする)
	  var Base64 = ReqHead.Substring(BASIC.Length).Trim();
	  System.Text.Encoding encoding = System.Text.Encoding.GetEncoding("UTF-8");
	  // クライアントから送信されたパスワードはBase64文字列なのでデコードし平文文字列にコンバートする
	  var PrainText = encoding.GetString(Convert.FromBase64String(Base64));

	  var seperator = PrainText.IndexOf(':'); // 文字列のコロンの位置をセット
	  var userid = PrainText.Substring(0, seperator); // 先頭からコロン手前まで呼出し
	  var password = PrainText.Substring(seperator + 1); // コロンの次から末尾まで呼出し

	  if (userid == USER && password == PASS) // 認証
	  {
		await _next.Invoke(context);
	  }
	  else
	  {
		// Basic認証用のリクエストヘッダーがあるがユーザー名またはパスワードが間違っている場合
		SetResponse401Error(context);
		return;
	  }
	}
	else
	{
	  // 初回アクセスなどのBasic認証用のリクエストヘッダーが無い場合
	  SetResponse401Error(context);
	  return;
	}
  }

  private void SetResponse401Error (HttpContext context)
  {
	// エラーコード401(Unauthorized)とレスポンスヘッダーをセットする
	context.Response.StatusCode = 401;
	context.Response.Headers.Add(AUTHENTICATION, "Basic realm=\"Original Realm\"");
  }
}
```

<br />

## Digest認証

これまたどの様に行うのか分からないので準備中  

* * *
