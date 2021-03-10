`postfix` 是一个配置简单的 SMTP 邮件服务器。

Debian 还提供了另一个强大的邮件服务器： `Exim4` 并且默认安装且推荐使用。

> [[教學] Mail Server 架設與設定指南-Postfix](https://xenby.com/b/260-%E6%95%99%E5%AD%B8-mail-server-%E6%9E%B6%E8%A8%AD%E8%88%87%E8%A8%AD%E5%AE%9A%E6%8C%87%E5%8D%97-postfix)

如果有接入数据库或 LDAP 的需求，则需要安装其他模块，例如 `postfix-ldap` 等。

默认只开放 25 端口，但实际上是通过 STARTLS 加密传输的，需要额外指定开放 465 端口

> [[Postfix] SMTP Mail Server 架設教學指南 – Postfix with Ubuntu](https://code.yidas.com/mail-server-postfix-with-ubuntu/)

