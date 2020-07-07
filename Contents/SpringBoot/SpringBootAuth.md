---
layout: default
title: ":leaves: Spring Boot"
description: ":closed_lock_with_key: Spring Boot Securityによる認証"
date: "2020/05/17"
lastmod: "2020/05/17"
---

## 0. はじめに

基本的なSpring Bootプロジェクトに追加してSpring Boot Security機能を追加する目的で書きますので  
プロジェクトの作成の仕方は別途参照下さい。  

<br />

## 1. pom.xml

プロジェクト内のpom.xmlにSpring Securityを追加します。  

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

<br />

## 2. Basic認証

WebSecurityConfigurerAdapterクラスを継承しconfigureメソッド内に以下を追加します。  
@EnableWebSecurityアノテーションを指定する事によりSpring Securityをサポートしたクラスとして認識される。  
configure(AuthenticationManagerBuilder auth)クラスでユーザーの管理を行う。  

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class LoginSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	public void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.antMatchers("/").permitAll() //ルートは全ユーザーがアクセス可能
				.anyRequest().authenticated() //その他のURLは認証が必要
			.and()
				.httpBasic().realmName("Original Realm") //Basic認証
			.and()
				.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
			.and()
			.csrf().disable(); //csrf対策は無効。今回は省略。
	}

	@Autowired
	@Override
	public void configure(AuthenticationManagerBuilder auth) throws Exception {
		PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
		auth
			.inMemoryAuthentication()
				.withUser(User.withUsername("userid")
					.passwordEncoder(encoder::encode)
					.password("password")
					.roles("USER")
					.build());
	}
}
```

<br />

## 3. Digest認証

@EnableWebSecurityアノテーションを指定する事によりSpring Securityをサポートしたクラスとして認識される。  

**LoginSecurityConfigクラス**  

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.context.annotation.Bean;
import org.springframework.security.web.authentication.www.DigestAuthenticationEntryPoint;
import org.springframework.security.web.authentication.www.DigestAuthenticationFilter;

@Configuration
@EnableWebSecurity
public class LoginSecurityConfig extends WebSecurityConfigurerAdapter {
	@Autowired
	private LoginUser loginuser;
	
	@Override
	public void configure(HttpSecurity http) throws Exception {
		http
			.antMatcher("/rest")
			.authorizeRequests()
				.antMatchers("/").permitAll() //ルートは全ユーザーがアクセス可能。
				.anyRequest().authenticated() //その他のURLは認証が必要
			.and()
				.addFilter(digestFilter(digestEntry()))
				.exceptionHandling().authenticationEntryPoint(digestEntry())
			.and()
			.csrf().disable(); //csrf対策は無効。今回は省略。
	}
	
	private DigestAuthenticationEntryPoint digestEntry() {
		var entry = new DigestAuthenticationEntryPoint();
		entry.setRealmName("Original Realm");
		entry.setKey("acegi");
		entry.setNonceValiditySeconds(90);
		return entry;
	}
	
	private DigestAuthenticationFilter digestFilter(DigestAuthenticationEntryPoint entry) {
		var filter = new DigestAuthenticationFilter();
		filter.setAuthenticationEntryPoint(entry);
		filter.setUserDetailsService(loginuser);
		filter.setPasswordAlreadyEncoded(true); //true=MD5、false=平文
		return filter;
	}

    @Autowired
	@Override
	public void configure(AuthenticationManagerBuilder auth) throws Exception {
		auth.userDetailsService(loginuser);
    }

    @Bean
    public PasswordEncoder passwordEncoder(){
        return NoOpPasswordEncoder.getInstance();
    }
}
```

別途、ユーザーを管理するクラスとパスワードをMD5でハッシュ化するクラスを作成します。  
本当は、データベースからハッシュ化されたパワードを取り出し比較を行うと良いがここでは、ダイレクトに平文でswitch文を使い  
一致するかで行っています。

**LoginUserクラス**  

```java
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
//import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.stereotype.Service;
import org.springframework.util.DigestUtils;

@Service
public class LoginUser implements UserDetailsService {
	@Override
	public UserDetails loadUserByUsername(String user) throws UsernameNotFoundException {
		if (user == null) {
			throw new UsernameNotFoundException("empty");
		}
		String password;
		switch (user) {
		case "user":
			password = DigestUtils.md5DigestAsHex((user + ":" + "Original Realm" + ":" + "passuser").getBytes());
			break;
		default :
			throw new UsernameNotFoundException("Not Found");
		}
		
		return new User(user, password, Collections.emptySet());
	}
}
```

<br />

## 4. Form認証

@EnableWebSecurityアノテーションを指定する事によりSpring Securityをサポートしたクラスとして認識される。  
`/login`はルートディレクトリにlogin.htmlがありこれを認証ページとした場合の例です。  

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;

@Configuration
@EnableWebSecurity
public class LoginSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	public void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.antMatchers("/").permitAll() //ルートは全ユーザーがアクセス可能。
				.anyRequest().authenticated() //その他のURLは認証が必要
			.and()
			.formLogin() //Form認証
				.loginPage("/login") // 認証を行っていない場合/loginページへ誘導される。
				.defaultSuccessUrl("/view") //ログイン成功時のアクセス先html。UserDetailsServiceメソッドを使う場合はいらない。
				.permitAll()
			.and()
			.csrf().disable() //csrf対策無効。今回は省略。
			.logout()
				.permitAll(); //logoutは全員がアクセス可能。
	}

	@Autowired
	@Override
	public void configure(AuthenticationManagerBuilder auth) throws Exception {
		PasswordEncoder encoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
		auth
			.inMemoryAuthentication()
				.withUser(User.withUsername("admin")
					.passwordEncoder(encoder::encode)
					.password("passadmin")
					.roles("ADMIN")
					.build())
			.and()
			.inMemoryAuthentication()
				.withUser(User.withUsername("user")
					.passwordEncoder(encoder::encode)
					.password("passuser")
					.roles("USER")
					.build());
	}
}
```

以下は、ページをコントロールするクラスであるのでForm認証と直接関係は無いですがルートページをログインページに移動  
させるコードを書いておくのも良いでしょう。  

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MvcConfig implements WebMvcConfigurer {
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/login").setViewName("login");
    }
}
```

* * *
