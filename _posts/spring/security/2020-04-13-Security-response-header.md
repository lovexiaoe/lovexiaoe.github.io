---
title: spring-security-response-header
published: true
category: spring-security
---

### 默认的安全头
Spring Security提供了默认的响应安全头：
```
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Content-Type-Options: nosniff
Strict-Transport-Security: max-age=31536000 ; includeSubDomains
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```
从上面可以看到，spring security 默认关闭了缓存，启用了xss攻击保护。

### Content Security Policy (CSP)
CSP是web应用减少内容注入漏洞的机制，如Xss。是一种声明式策略，声明web应用希望从哪里加载资源。如，
```
Content-Security-Policy: script-src https://trustedscripts.example.com
```
如果客户端向从script-src外加载资源，将会被阻止。

### Referrer Policy
推荐人策略让应用管理推荐字段，浏览器让目标应用知道用户上一个访问应用是什么。
```
Referrer-Policy: same-origin
```