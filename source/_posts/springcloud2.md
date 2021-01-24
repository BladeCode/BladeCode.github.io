---
title: SpringCloud（二）Security OAuth2 的 四种授权模式
date: 2020-07-12 09:00:00
categories: SpringCloud
tag: [SpringCloud, OAuth2]
---

在上一篇文章中，我们了解了 Security OAuth2 相关的一些基础知识，和整个四种授权模式的交互过程，那么本篇是对四种模式的实践，废话不多说，我们直接开始，SpringCloud 相关的实践代码均托管在[rc-cluster-springcloud](https://github.com/RootCluster/rc-cluster-springcloud)项目的中，项目使用的一些依赖版本如下

<!-- more -->

* gradle：6.1.1
* SpringBoot：2.2.6.RELEASE
* SpringCloud：Hoxton.SR4
* JDK：1.8

## 实践

在实践阶段为了方便，我将资源服务器和授权服务器整合在一个服务上，在后续扩展部分，会提供实际生产环境中的常用做法，先看项目的包依赖

```groovy
implementation 'org.springframework.boot:spring-boot-starter-web'
implementation 'org.springframework.cloud:spring-cloud-starter-oauth2'
```

下面的两点，不管是什么模式的授权方式，写法都是一样
1. 业务 API
    ```java
    /**
     * 业务 API
     * 为了方便我直接将 UserInfo 对象放在了 Controller 类中
     *
     */
    @Controller
    public class UserController {

        @GetMapping("/api/userinfo")
        public ResponseEntity<UserInfo> getUserInfo() {
            User user = (User) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            String email = user.getUsername() + "@gmail.com";
            UserInfo userInfo = new UserInfo();
            userInfo.setName(user.getUsername());
            userInfo.setEmail(email);
            // TODO 不同的授权选择不同的模式
            // 授权码模式
            userInfo.setGrantType("authorization_code");
            // 客户端模式
            userInfo.setGrantType("client_credentials");
            // 密码模式
            userInfo.setGrantType("password");
            // 简化模式
            userInfo.setGrantType("implicit");
            return ResponseEntity.ok(userInfo);
        }

        public static class UserInfo {

            private String name;
            private String email;
            private String grantType;

            public String getName() {
                return name;
            }

            public void setName(String name) {
                this.name = name;
            }

            public String getEmail() {
                return email;
            }

            public void setEmail(String email) {
                this.email = email;
            }

            public String getGrantType() {
                return grantType;
            }

            public void setGrantType(String grantType) {
                this.grantType = grantType;
            }
        }
    }
    ```
2. 资源服务
    ```java
    /**
     * 资源服务器
     * 
     * @author : Jerry xu
     * @date : 2020/7/17  23:45
     */
    @Configuration
    @EnableResourceServer
    public class OAuth2ResourceServer extends ResourceServerConfigurerAdapter {

        /**
        * 用来配置对资源的访问控制规则
        * 默认设置下，所有非 /oauth/** 路经下的资源都是被保护的
        *
        * @param http http
        * @throws Exception exception
        */
        @Override
        public void configure(HttpSecurity http) throws Exception {
            http.authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .requestMatchers()
                    // 对 /api/** 路经下的资源进行了保护
                    .antMatchers("/api/**");
        }
    }
    ```

### 权码模式

#### 代码实现

##### 授权服务器配置

```java
@Configuration
@EnableAuthorizationServer
public class OAuth2AuthorizationServer extends AuthorizationServerConfigurerAdapter {

    @Resource
    private BCryptPasswordEncoder passwordEncoder;

    /**
     * 用于配置客户端信息（id，secret，grant_type 等信息），要求至少配置一个客户端、
     * 默认不支持 Resource Owner Password 授权类型，
     * 如果要使用该类型，需要在 AuthorizationServerEndpointsConfigurer 中提供 AuthenticationManager 用于用户认证
     * 授权模式系统默认提供如下方式，请查看：{@link OAuth2AuthorizationServerConfiguration.BaseClientDetailsConfiguration#oauth2ClientDetails()}方法
     * <ol>
     *     <li>authorization_code：授权码模式</li>
     *     <li>password：密码模式</li>
     *     <li>client_credentials：客户端模式</li>
     *     <li>implicit：简化模式</li>
     *     <li>refresh_token：刷新 token 模式</li>
     * </ol>
     *
     * @param clients clients
     * @throws Exception exception
     */
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 在内存中，用于演示，不适用于实际生产环境
        clients.inMemory()
                // withClient + secret 这两个就是凭证
                .withClient("clientapp")
                .secret(passwordEncoder.encode("112233"))
                // 重定向地址，用于授权成功后跳转
                .redirectUris("http://localhost:9001/callback")
                // 授权码模式
                .authorizedGrantTypes("authorization_code")
                // 权限细分
                .scopes("read_userinfo", "read_contacts");
    }
}
```

##### Security Web安全配置

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.requestMatchers()
                .antMatchers("/login", "/oauth/authorize")
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .permitAll()
                .and()
                .csrf()
                .disable();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("jerry")
                .password(passwordEncoder().encode("xyz"))
                .roles("USER");
    }
}
```


#### 测试

整个过程请看视频

https://res.cloudinary.com/incoder/video/upload/v1594910477/blog/video/auth.mp4

1. 获取授权
    ```
    # 浏览器请求地址
    http://localhost:8080/oauth/authorize?client_id=clientapp&redirect_uri=http://localhost:9001/callback&response_type=code&scope=read_userinfo
    ```
2. 使用授权码获取 Token
    ![](https://res.cloudinary.com/incoder/image/upload/v1594911902/blog/authcode.png)
3. 请求资源服务（业务请求）
    ![](https://res.cloudinary.com/incoder/image/upload/v1594912116/blog/biz-request.png)

如果你没有安装 [Postman](https://www.postman.com)，对使用 `curl` 命令比较熟悉，那么可替换上面第 2，3 步操作

* 使用授权码获取 Token
    ```bash
    # 请自行更换 code 参数的值
    curl -X POST --user clientapp:112233 http://localhost:8080/oauth/token -H "content-type: application/x-www-form-urlencoded" -d "code=8uYpdo&grant_type=authorization_code&redirect_uri=http://localhost:9001/callback&scope=read_userinfo"
    ```
* 请求资源服务（业务请求）
    ```bash
    # 请自行更换 authorization 参数
    curl -X GET http://localhost:8080/api/userinfo -H "authorization: Bearer 36cded80-b6f5-43b7-bdfc-594788a24530"
    ```
![](https://res.cloudinary.com/incoder/image/upload/v1594949675/blog/curl-auth.png)

### 客户端模式

#### 代码实现

##### 授权服务器配置

```java
@Configuration
@EnableAuthorizationServer
public class OAuth2AuthorizationServer extends AuthorizationServerConfigurerAdapter {

    @Resource
    private BCryptPasswordEncoder passwordEncoder;

    /**
     * 用于配置客户端信息（id，secret，grant_type 等信息），要求至少配置一个客户端、
     * 默认不支持 Resource Owner Password 授权类型，
     * 如果要使用该类型，需要在 AuthorizationServerEndpointsConfigurer 中提供 AuthenticationManager 用于用户认证
     * 授权模式系统默认提供如下方式，请查看：{@link OAuth2AuthorizationServerConfiguration.BaseClientDetailsConfiguration#oauth2ClientDetails()}方法
     * <ol>
     *     <li>authorization_code：授权码模式</li>
     *     <li>password：密码模式</li>
     *     <li>client_credentials：客户端模式</li>
     *     <li>implicit：简化模式</li>
     *     <li>refresh_token：刷新 token 模式</li>
     * </ol>
     *
     * @param clients clients
     * @throws Exception exception
     */
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 在内存中，用于演示，不适用于实际生产环境
        clients.inMemory()
                // withClient + secret 这两个就是凭证
                .withClient("clientapp")
                .secret(passwordEncoder.encode("112233"))
                // 重定向地址，用于授权成功后跳转
                .redirectUris("http://localhost:9001/callback")
                // 客户端模式
                .authorizedGrantTypes("client_credentials")
                // 权限细分
                .scopes("read_userinfo", "read_contacts");
    }
}
```

##### Security Web安全配置

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.requestMatchers()
                .antMatchers("/login", "/oauth/authorize")
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .permitAll()
                .and()
                .csrf()
                .disable();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("jerry")
                .password(passwordEncoder().encode("xyz"))
                .roles("USER");
    }
}
```

#### 测试

整个过程请看视频

https://res.cloudinary.com/incoder/video/upload/v1594910477/blog/video/auth.mp4

1. 获取授权
    ```
    # 浏览器请求地址
    http://localhost:8080/oauth/authorize?client_id=clientapp&redirect_uri=http://localhost:9001/callback&response_type=code&scope=read_userinfo
    ```
2. 使用授权码获取 Token
    ![](https://res.cloudinary.com/incoder/image/upload/v1594911902/blog/authcode.png)
3. 请求资源服务（业务请求）
    ![](https://res.cloudinary.com/incoder/image/upload/v1594912116/blog/biz-request.png)

如果你没有安装 [Postman](https://www.postman.com)，对使用 `curl` 命令比较熟悉，那么可替换上面第 2，3 步操作

* 使用授权码获取 Token
    ```bash
    # 请自行更换 code 参数的值
    curl -X POST --user clientapp:112233 http://localhost:8080/oauth/token -H "content-type: application/x-www-form-urlencoded" -d "code=8uYpdo&grant_type=authorization_code&redirect_uri=http://localhost:9001/callback&scope=read_userinfo"
    ```
* 请求资源服务（业务请求）
    ```bash
    # 请自行更换 authorization 参数
    curl -X GET http://localhost:8080/api/userinfo -H "authorization: Bearer 36cded80-b6f5-43b7-bdfc-594788a24530"
    ```
![](https://res.cloudinary.com/incoder/image/upload/v1594949675/blog/curl-auth.png)

### 简化模式

#### 代码实现

##### 授权服务器配置

```java
@Configuration
@EnableAuthorizationServer
public class OAuth2AuthorizationServer extends AuthorizationServerConfigurerAdapter {

    @Resource
    private BCryptPasswordEncoder passwordEncoder;

    /**
     * 用于配置客户端信息（id，secret，grant_type 等信息），要求至少配置一个客户端、
     * 默认不支持 Resource Owner Password 授权类型，
     * 如果要使用该类型，需要在 AuthorizationServerEndpointsConfigurer 中提供 AuthenticationManager 用于用户认证
     * 授权模式系统默认提供如下方式，请查看：{@link OAuth2AuthorizationServerConfiguration.BaseClientDetailsConfiguration#oauth2ClientDetails()}方法
     * <ol>
     *     <li>authorization_code：授权码模式</li>
     *     <li>password：密码模式</li>
     *     <li>client_credentials：客户端模式</li>
     *     <li>implicit：简化模式</li>
     *     <li>refresh_token：刷新 token 模式</li>
     * </ol>
     *
     * @param clients clients
     * @throws Exception exception
     */
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 在内存中，用于演示，不适用于实际生产环境
        clients.inMemory()
                // withClient + secret 这两个就是凭证
                .withClient("clientapp")
                .secret(passwordEncoder.encode("112233"))
                // 重定向地址，用于授权成功后跳转
                .redirectUris("http://localhost:9001/callback")
                // 授权码模式
                .authorizedGrantTypes("authorization_code")
                // 权限细分
                .scopes("read_userinfo", "read_contacts");
    }
}
```

##### Security Web安全配置

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.requestMatchers()
                .antMatchers("/login", "/oauth/authorize")
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .permitAll()
                .and()
                .csrf()
                .disable();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("jerry")
                .password(passwordEncoder().encode("xyz"))
                .roles("USER");
    }
}
```

#### 测试

整个过程请看视频

https://res.cloudinary.com/incoder/video/upload/v1594910477/blog/video/auth.mp4

1. 获取授权
    ```
    # 浏览器请求地址
    http://localhost:8080/oauth/authorize?client_id=clientapp&redirect_uri=http://localhost:9001/callback&response_type=code&scope=read_userinfo
    ```
2. 使用授权码获取 Token
    ![](https://res.cloudinary.com/incoder/image/upload/v1594911902/blog/authcode.png)
3. 请求资源服务（业务请求）
    ![](https://res.cloudinary.com/incoder/image/upload/v1594912116/blog/biz-request.png)

如果你没有安装 [Postman](https://www.postman.com)，对使用 `curl` 命令比较熟悉，那么可替换上面第 2，3 步操作

* 使用授权码获取 Token
    ```bash
    # 请自行更换 code 参数的值
    curl -X POST --user clientapp:112233 http://localhost:8080/oauth/token -H "content-type: application/x-www-form-urlencoded" -d "code=8uYpdo&grant_type=authorization_code&redirect_uri=http://localhost:9001/callback&scope=read_userinfo"
    ```
* 请求资源服务（业务请求）
    ```bash
    # 请自行更换 authorization 参数
    curl -X GET http://localhost:8080/api/userinfo -H "authorization: Bearer 36cded80-b6f5-43b7-bdfc-594788a24530"
    ```
![](https://res.cloudinary.com/incoder/image/upload/v1594949675/blog/curl-auth.png)

#### 测试

```
http://localhost:8080/oauth/authorize?client_id=clientapp&redirect_uri=http://localhost:9001/callback&response_type=token&scope=read_userinfo&state=abc
```

```
http://localhost:9001/callback#access_token=60fbfadc-f801-4514-a5fb-52c6fd42f6cb&token_type=bearer&state=abc&expires_in=119
```

### 密码模式

https://github.com/spring-projects/spring-boot/issues/11136#issuecomment-381338605

#### 代码实现

##### 授权服务器配置

```java
@Configuration
@EnableAuthorizationServer
public class OAuth2AuthorizationServer extends AuthorizationServerConfigurerAdapter {

    @Resource
    private BCryptPasswordEncoder passwordEncoder;

    /**
     * 用于配置客户端信息（id，secret，grant_type 等信息），要求至少配置一个客户端、
     * 默认不支持 Resource Owner Password 授权类型，
     * 如果要使用该类型，需要在 AuthorizationServerEndpointsConfigurer 中提供 AuthenticationManager 用于用户认证
     * 授权模式系统默认提供如下方式，请查看：{@link OAuth2AuthorizationServerConfiguration.BaseClientDetailsConfiguration#oauth2ClientDetails()}方法
     * <ol>
     *     <li>authorization_code：授权码模式</li>
     *     <li>password：密码模式</li>
     *     <li>client_credentials：客户端模式</li>
     *     <li>implicit：简化模式</li>
     *     <li>refresh_token：刷新 token 模式</li>
     * </ol>
     *
     * @param clients clients
     * @throws Exception exception
     */
    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        // 在内存中，用于演示，不适用于实际生产环境
        clients.inMemory()
                // withClient + secret 这两个就是凭证
                .withClient("clientapp")
                .secret(passwordEncoder.encode("112233"))
                // 重定向地址，用于授权成功后跳转
                .redirectUris("http://localhost:9001/callback")
                // 授权码模式
                .authorizedGrantTypes("authorization_code")
                // 权限细分
                .scopes("read_userinfo", "read_contacts");
    }
}
```

##### Security Web安全配置

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Bean
    public BCryptPasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.requestMatchers()
                .antMatchers("/login", "/oauth/authorize")
                .and()
                .authorizeRequests()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .permitAll()
                .and()
                .csrf()
                .disable();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("jerry")
                .password(passwordEncoder().encode("xyz"))
                .roles("USER");
    }
}
```

#### 测试

整个过程请看视频

https://res.cloudinary.com/incoder/video/upload/v1594910477/blog/video/auth.mp4

1. 获取授权
    ```
    # 浏览器请求地址
    http://localhost:8080/oauth/authorize?client_id=clientapp&redirect_uri=http://localhost:9001/callback&response_type=code&scope=read_userinfo
    ```
2. 使用授权码获取 Token
    ![](https://res.cloudinary.com/incoder/image/upload/v1594911902/blog/authcode.png)
3. 请求资源服务（业务请求）
    ![](https://res.cloudinary.com/incoder/image/upload/v1594912116/blog/biz-request.png)

如果你没有安装 [Postman](https://www.postman.com)，对使用 `curl` 命令比较熟悉，那么可替换上面第 2，3 步操作

* 使用授权码获取 Token
    ```bash
    # 请自行更换 code 参数的值
    curl -X POST --user clientapp:112233 http://localhost:8080/oauth/token -H "content-type: application/x-www-form-urlencoded" -d "code=8uYpdo&grant_type=authorization_code&redirect_uri=http://localhost:9001/callback&scope=read_userinfo"
    ```
* 请求资源服务（业务请求）
    ```bash
    # 请自行更换 authorization 参数
    curl -X GET http://localhost:8080/api/userinfo -H "authorization: Bearer 36cded80-b6f5-43b7-bdfc-594788a24530"
    ```
![](https://res.cloudinary.com/incoder/image/upload/v1594949675/blog/curl-auth.png)

## 扩展

## 附录

* [芋道 Spring Security OAuth2 存储器](http://www.iocoder.cn/Spring-Security/OAuth2-learning-store/?self)
* [浅谈 OAuth 2.0 (二) - 授权类型](https://liaodanqi.me/2019/10/24/oauth-grant-type/)
* [Spring Security OAuth2](http://linyishui.top/2019111701.html)