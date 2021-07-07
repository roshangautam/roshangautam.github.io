---
title: "How to reset mysql root password ?"
description: "Did you forget your mysql root password ? Do you have ssh access to your server ? If the answer to these questions is a YES then follow the instructions below to reset the root password for your mysql instance."
coverImage: "/assets/images/posts/covers/mysql-logo.jpg"
date: "2014-05-28"
author:
  name: Roshan Gautam
  picture: "https://avatars.githubusercontent.com/u/978347?v=4"
ogImage:
  url: "/assets/images/posts/covers/mysql-logo.jpg"
---

<p style="text-align: center;">
  <image src="./mysql-logo.jpg"/>
</p>

Did you forget your mysql root password ? Do you have ssh access to your server ? If the answer to these questions is a YES then follow the instructions below to reset the root password for your mysql instance.

1. Stop **mysqld** and restart it with the `--skip-grant-tables` option. This enables anyone to connect without a password and with all privileges. Because this is insecure, you might want to use `--skip-grant-tables` in conjunction with `--skip-networking` to prevent remote clients from connecting.

2. Connect to mysql server with this command:

```sbtshell
$ **`mysql`**
```

3. Issue the following statements in the mysql client. Replace the password with the password that you want to use.

```sbtshell
mysql> **`UPDATE mysql.user SET Password=PASSWORD('MyNewPass')`**->**`WHERE User='root';`**mysql> **`FLUSH PRIVILEGES;`**
```

The `FLUSH` statement tells the server to reload the grant tables into memory so that it notices the password change.

You should now be able to connect to the MySQL server as `root` using the new password. Stop the server, then restart it normally (without the `--skip-grant-tables` and `--skip-networking` options).
