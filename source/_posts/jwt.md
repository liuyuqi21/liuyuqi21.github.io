---
title: JWT 相关设计
date: 2018-10-14 15:55:17
tags: JWT
categories: 系统设计
---
## Token 认证的优势
- 无状态，可以在多个服务间共享
- 有效避免了 CSRF 攻击
- 单点登录友好

## 有效期

- 方案一：在服务端保存 Token 状态，用户每次操作都会自动刷新 Token。在前后端分离、单页 App 这些情况下，每秒种可能发起很多次请求，每次都去刷新过期时间会产生非常大的代价。
- 方案二：使用 Refresh Token，一旦 Token 过期，就反馈给前端，前端使用 Refresh Token 申请一个全新 Token 继续使用。当然 Refresh Token 也是有有效期的，但是这个有效期就可以长一点了，比如，以天为单位的时间。Refresh Token 过期用户就要重新登录。

## 并发请求，过期的时候同一个页面发来两个请求
用 Redis，将旧token作为键，新token作为值，设置一个30秒过期的时间。当第二个请求来的时候，已经知道token在backlist中了，我们可以去redis查询下是否存在这么个旧token，存在的话给他新的 Token。

## jWT
- Token 有三部分组成，中间用.隔开，并使用 Base64 编码
    - header：Token 的类型，苏使用的加密算法
    ```json     
    {
    "typ": "JWT",
    "alg": "HS256"
    }
    ```
    - payload
    - signature：用 Base64 对 header.payload 进行编码，然后用 Secret 对编码后的内容进行加密，加密后的内容即为 Signature
## OAuth 2.0
- Third-party applicaton：第三方应用程序，客户端
- HTTP service：HTTP 服务提供商
- Resource Owner：资源所有者
- User Agent：用户代理
- Authorization server：认证服务器，即服务提供商专门用来处理认证的服务器。
- Resource server：资源服务器，即服务提供商存放用户生成的资源的服务器。它与认证服务器，可以是同一台服务器，也可以是不同的服务器。
## 运行流程
（A）用户打开客户端以后，客户端要求用户给予授权。

（B）用户同意给予客户端授权。

（C）客户端使用上一步获得的授权，向认证服务器申请令牌。

（D）认证服务器对客户端进行认证以后，确认无误，同意发放令牌。

（E）客户端使用令牌，向资源服务器申请获取资源。

（F）资源服务器确认令牌无误，同意向客户端开放资源。
**B（客户端授权）是关键**
### 客户端的授权模式
- 授权码模式
- 简化模式
- 密码模式
- 客户端模式
- put 替换某个资源，需提供完整的资源信息
- patch 部分修改资源，提供部分资源信息

# 参考资料
- [理解 OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)