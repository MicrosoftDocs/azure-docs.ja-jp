---
title: 接続文字列 - Azure Database for MySQL
description: このドキュメントでは、Azure Database for MySQL に接続するアプリケーションについて、現在サポートされている接続文字列を示します。ADO.NET (C#)、JDBC、Node.js、ODBC、PHP、Python、および Ruby の接続文字列が含まれます。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-python, devx-track-js
ms.openlocfilehash: 40af2660f0052a876ef9310bc2426295ba67558b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541489"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Azure Database for MySQL にアプリケーションを接続する方法
このトピックでは、Azure Database for MySQL でサポートされているすべての接続文字列の種類と共に、テンプレートと例を紹介します。 実際に使用する接続文字列のパラメーターと設定は異なる可能性があります。

- 証明書を取得するには、[SSL を構成する方法](./howto-configure-ssl.md)に関するページを参照してください。
- {your_host} = \<servername>.mysql.database.azure.com
- {your_user}@{servername} = 正しく認証されるためのユーザー ID の形式です。  userID のみを使用すると、認証は失敗します。

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

この例では、サーバー名は `mydemoserver`、データベース名は `wpdb`、ユーザー名は `WPAdmin`、パスワードは `mypassword!2` です。 その結果、接続文字列は次のようになります。

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>Azure ポータルから接続文字列の詳細を取得する
[Azure portal](https://portal.azure.com) で、Azure Database for MySQL サーバーに移動し、 **[接続文字列]** をクリックして、インスタンスの文字列リストを取得します。:::image type="content" source="./media/howto-connection-strings/connection-strings-on-portal.png" alt-text="Azure portal 上の [接続文字列] ウィンドウ":::

この文字列は、ドライバー、サーバー、その他のデータベース接続パラメーターといった詳細情報を提供します。 独自のパラメーター (データベース名やパスワードなど) を使用してこれらの例を変更します。 その文字列を使用して、コードやアプリケーションからサーバーに接続できます。

## <a name="next-steps"></a>次のステップ
- 接続ライブラリの詳細については、[接続ライブラリの概念](./concepts-connection-libraries.md)に関するページを参照してください。
