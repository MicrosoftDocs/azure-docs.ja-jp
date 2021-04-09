---
title: クイック スタート:PHP を使用して接続する - Azure Database for MySQL - フレキシブル サーバー
description: このクイックスタートでは、Azure Database for MySQL - フレキシブル サーバーに接続してデータのクエリを実行するために使用できる、PHP コード サンプルをいくつか紹介します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: dc6b069e3c7686ec6964dab890e503aa193cf6fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92545108"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>クイック スタート:PHP を使用して Azure Database for MySQL - フレキシブル サーバーに接続し、データのクエリを実行する

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

このクイックスタートでは、[PHP](https://secure.php.net/manual/intro-whatis.php) アプリケーションを使用して Azure Database for MySQL フレキシブル サーバーに接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。 この記事では、PHP を使用した開発には慣れているものの、Azure Database for MySQL フレキシブル サーバーの使用は初めてであるユーザーを想定しています。

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、次のいずれかのガイドで作成されたリソースを出発点として使用します。

- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](./quickstart-create-server-portal.md)
- [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーを作成する](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>クライアント ワークステーションを準備する
1. "*プライベート アクセス (VNet 統合)* " を指定してフレキシブル サーバーを作成した場合は、サーバーと同じ VNet 内のリソースからサーバーに接続する必要があります。 仮想マシンを作成して、フレキシブル サーバーと共に作成された VNet に追加できます。 「[Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理する](./how-to-manage-virtual-network-cli.md)」を参照してください。

2. *パブリック アクセス (使用できる IP アドレス)* を指定してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。 「[Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-cli.md)」を参照してください。

### <a name="install-php"></a>PHP のインストール

独自のサーバーに PHP をインストールするか、PHP が含まれた Azure [Web アプリ](../../app-service/overview.md)を作成します。  ファイアウォール規則の作成方法については、「[ファイアウォール規則の作成および管理](./how-to-manage-firewall-portal.md)」を参照してください。

#### <a name="macos"></a>macOS

1. [PHP 7.1.4 バージョン](https://secure.php.net/downloads.php)をダウンロードします。
2. PHP をインストールし、さらなる構成については [PHP マニュアル](https://secure.php.net/manual/install.macosx.php)を参照します。

#### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. [PHP 7.1.4 非スレッドセーフ バージョン (x64)](https://secure.php.net/downloads.php) をダウンロードします。
2. PHP をインストールし、さらなる構成については [PHP マニュアル](https://secure.php.net/manual/install.unix.php)を参照します。

#### <a name="windows"></a>Windows

1. [PHP 7.1.4 非スレッドセーフ バージョン (x64)](https://windows.php.net/download#php-7.1) をダウンロードします。
2. PHP をインストールし、さらなる構成については [PHP マニュアル](https://secure.php.net/manual/install.windows.php)を参照します。

## <a name="get-connection-information"></a>接続情報の取得

Azure Database for MySQL フレキシブル サーバーに接続するために必要な接続情報を取得します。 完全修飾サーバー名とサインイン資格情報が必要です。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、作成したサーバー (例: **mydemoserver**) を検索します。
3. サーバー名を選択します。
4. サーバーの **[概要]** パネルから、 **[サーバー名]** と **[サーバー管理者ログイン名]** を書き留めます。 パスワードを忘れた場合も、このパネルからパスワードをリセットすることができます。
 <!---:::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connecting-to-flexible-server-using-tlsssl-in-php"></a>PHP で TLS/SSL を使用してフレキシブル サーバーに接続する

アプリケーションから TLS/SSL 経由でフレキシブル サーバーへの暗号化された接続を確立するには、次のコード サンプルを参照してください。 TLS/SSL 経由で通信するために必要な証明書は、[https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) からダウンロードできます。

```php using SSL
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

## <a name="connect-and-create-a-table"></a>接続とテーブルの作成

接続し、**CREATE TABLE** SQL ステートメントを使用してテーブルを作成するには、次のコードを使用します。

このコードでは、PHP に含まれている **MySQL Improved 拡張機能** (mysqli) クラスを使用します。 このコードでは、[mysqli_init](https://secure.php.net/manual/mysqli.init.php) メソッドと [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) メソッドを呼び出して MySQL に接続します。 次に、[mysqli_query](https://secure.php.net/manual/mysqli.query.php) メソッドを呼び出してクエリを実行します。 その後、[mysqli_close](https://secure.php.net/manual/mysqli.close.php) メソッドを呼び出して接続を閉じます。

host、username、password、db_name の各パラメーターは実際の値に置き換えてください。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>データの挿入

接続し、**INSERT** SQL ステートメントを使用してデータを挿入するには、次のコードを使用します。

このコードでは、PHP に含まれている **MySQL Improved 拡張機能** (mysqli) クラスを使用します。 このコードでは、[mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) メソッドを使用して、準備済みの INSERT ステートメントを作成した後、[mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) メソッドを使用して、挿入される列の値ごとにパラメーターをバインドします。 このコードでは、[mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) メソッドでステートメントを実行してから、[mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) メソッドでステートメントを閉じます。

host、username、password、db_name の各パラメーターは実際の値に置き換えてください。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>データの読み取り

接続し、**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。  このコードでは、PHP に含まれている **MySQL Improved 拡張機能** (mysqli) クラスを使用します。 このコードでは、[mysqli_query](https://secure.php.net/manual/mysqli.query.php) メソッドを使用して SQL クエリを実行し、[mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) メソッドを使用して結果の行をフェッチします。

host、username、password、db_name の各パラメーターは実際の値に置き換えてください。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>データの更新

接続し、**UPDATE** SQL ステートメントを使用してデータを更新するには、次のコードを使用します。

このコードでは、PHP に含まれている **MySQL Improved 拡張機能** (mysqli) クラスを使用します。 このコードでは、[mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) メソッドを使用して、準備済みの UPDATE ステートメントを作成し、[mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) メソッドを使用して、更新される列の値ごとにパラメーターをバインドします。 このコードでは、[mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) メソッドでステートメントを実行してから、[mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) メソッドでステートメントを閉じます。

host、username、password、db_name の各パラメーターは実際の値に置き換えてください。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>データの削除
接続し、**DELETE** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。

このコードでは、PHP に含まれている **MySQL Improved 拡張機能** (mysqli) クラスを使用します。 このコードでは、[mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) メソッドを使用して、準備済みの DELETE ステートメントを作成し、[mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) メソッドを使用して、ステートメント内の WHERE 句のパラメーターをバインドします。 このコードでは、[mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) メソッドでステートメントを実行してから、[mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) メソッドでステートメントを閉じます。

host、username、password、db_name の各パラメーターは実際の値に置き換えてください。

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```
## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL - フレキシブル サーバーでのトランスポート層セキュリティ (TLS 1.2) を使用した暗号化された接続](./how-to-connect-tls-ssl.md)
- [Azure Database for MySQL フレキシブル サーバーでのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーのファイアウォール規則を作成および管理する](./how-to-manage-firewall-portal.md)
- [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーの仮想ネットワークを作成および管理する](./how-to-manage-virtual-network-portal.md)