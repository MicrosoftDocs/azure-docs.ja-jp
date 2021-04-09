---
title: 接続文字列 - Azure Database for MariaDB
description: このドキュメントでは、Azure Database for MariaDB に接続するアプリケーションについて、現在サポートされている接続文字列を示します。ADO.NET (C#)、JDBC、Node.js、ODBC、PHP、Python、および Ruby の接続文字列が含まれます。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8119f0bfd1f9007cab0df93ad2c7ef22b4db2199
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662179"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Azure Database for MariaDB にアプリケーションを接続する方法
このトピックでは、Azure Database for MariaDB でサポートされているすべての接続文字列の種類と共に、テンプレートと例を紹介します。 実際に使用する接続文字列のパラメーターと設定は異なる可能性があります。

- 証明書を取得するには、[SSL を構成する方法](./howto-configure-ssl.md)に関するページを参照してください。
- {your_host} = [servername].mariadb.database.azure.com
- {your_user}@{servername} = 正しく認証されるためのユーザー ID の形式です。  userID のみを使用すると、認証は失敗します。

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

この例では、サーバー名は `mydemoserver`、データベース名は `wpdb`、ユーザー名は `WPAdmin`、パスワードは `mypassword!2` です。 その結果、接続文字列は次のようになります。

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Azure ポータルから接続文字列の詳細を取得する
[Azure portal](https://portal.azure.com) 上で、Azure Database for MariaDB サーバーに移動し、 **[接続文字列]** をクリックして、インスタンスの文字列リストを取得します。![Azure portal 上の [接続文字列] ウィンドウ](./media/howto-connection-strings/connection-strings-on-portal.png)

この文字列は、ドライバー、サーバー、その他のデータベース接続パラメーターといった詳細情報を提供します。 独自のパラメーター (データベース名やパスワードなど) を使用してこれらの例を変更します。 その文字列を使用して、コードやアプリケーションからサーバーに接続できます。

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
