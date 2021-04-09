---
title: クイック スタート:PHP を使用して接続する - Azure Database for MySQL
description: このクイックスタートでは、Azure Database for MySQL に接続してデータを照会するために使用できる、PHP コード サンプルをいくつか紹介します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: bf62eca26d846cc529df43b519b1377f88c8aede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132904"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>クイック スタート:PHP を使用して Azure Database for MySQL に接続してデータを照会する
このクイックスタートでは、[PHP](https://secure.php.net/manual/intro-whatis.php) アプリケーションを使用して Azure Database for MySQL に接続する方法を紹介します。 ここでは、SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、削除する方法を説明します。

## <a name="prerequisites"></a>前提条件
このクイックスタートでは、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) を使用して、Azure Database for MySQL シングル サーバーを作成します <br/> [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) を使用して、Azure Database for PostgreSQL の単一サーバーを作成します (まだない場合)。
- パブリック アクセスとプライベート アクセスのどちらを使用しているかに基づいて、次の **いずれか** のアクションを実行して、接続を有効にします。

    |アクション| 接続方法|ハウツー ガイド|
    |:--------- |:--------- |:--------- |
    | **ファイアウォール規則を構成する** | パブリック | [ポータル](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
    | **サービス エンドポイントを構成する** | パブリック | [ポータル](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
    | **プライベート リンクを構成する** | プライベート | [ポータル](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [データベースと管理者以外のユーザーを作成する](./howto-create-users.md?tabs=single-server)
- お使いのオペレーティング システム用の最新バージョンの PHP をインストールします
    - [macOS の PHP](https://secure.php.net/manual/install.macosx.php)
    - [Linux の PHP](https://secure.php.net/manual/install.unix.php)
    - [Windows の PHP](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> このクイックスタートでは、[MySQLi](https://www.php.net/manual/en/book.mysqli.php) ライブラリを使用して、接続を管理し、サーバーのクエリを実行します。

## <a name="get-connection-information"></a>接続情報の取得
データベース サーバーの接続情報は、Azure portal で次の手順のようにして取得できます。

1. [Azure Portal](https://portal.azure.com/) にログインします。
2. Azure Databases for MySQL のページに移動します。 **Azure Database for MySQL** を検索して選択できます。
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL を見つける":::

2. MySQL サーバー (**mydemoserver** など) を選択します。
3. **[概要]** ページで、 **[サーバー名]** の横にある完全修飾サーバー名と、 **[サーバー管理者ログイン名]** の横にある管理者ユーザー名をコピーします。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

> [!IMPORTANT]
> - パスワードを忘れた場合は、[パスワードをリセットする](./howto-create-manage-server-portal.md#update-admin-password)ことができます。
> - **host、username、password、** **db_name** の各パラメーターは実際の値に置き換えてください**

## <a name="step-1-connect-to-the-server"></a>手順 1:サーバーへの接続
SSL は、既定で有効になっています。 ローカル環境から接続するには、[DigiCertGlobalRootG2 SSL 証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)をダウンロードする必要がある場合があります。 このコードからは次のものが呼び出されます。
- MySQLi を初期化するための [mysqli_init](https://secure.php.net/manual/mysqli.init.php)。
- SSL 証明書のパスを指し示すための [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php)。 これはローカル環境では必要ですが、App Service Web アプリまたは Azure 仮想マシンには必要ありません。
- MySQL に接続するための [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php)。
- 接続を閉じるための [mysqli_close](https://secure.php.net/manual/mysqli.close.php)。


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, NULL, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[問題がある場合は、お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>手順 2:テーブルを作成する
次のコードを使用して接続します。 このコードからは次のものが呼び出されます。
- クエリを実行するための [mysqli_query](https://secure.php.net/manual/mysqli.query.php)。
```php
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
```

## <a name="step-3-insert-data"></a>手順 3:データの挿入
**INSERT** SQL ステートメントを使用してデータを挿入するには、次のコードを使用します。 このコードでは、次のメソッドが使用されます。
- 準備された挿入ステートメントを作成するための [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- 挿入された各列の値にパラメーターをバインドするための [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)。
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- メソッドを使用してステートメントを閉じるための [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>手順 4:データの読み取り
**SELECT** SQL ステートメントを使用してデータを読み取るには、次のコードを使用します。  そのコードでは、次のメソッドが使用されます。
- **SELECT** クエリを実行するための [mysqli_query](https://secure.php.net/manual/mysqli.query.php)
- 結果の行をフェッチするための [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php)。

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>手順 5: データの削除
**DELETE** SQL ステートメントを使用して行を削除するには、次のコードを使用します。 そのコードでは、次のメソッドが使用されます。
- 準備された削除ステートメントを作成するための [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php)
- パラメーターをバインドするための [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php)
- 準備された削除ステートメントを実行するための [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- ステートメントを閉じるための [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php)

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで使用したすべてのリソースをクリーンアップするには、次のコマンドを使用してリソース グループを削除します。

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [ポータルを使用して Azure Database for MySQL サーバーを管理する](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI を使用して Azure Database for MySQL サーバーを管理する](./how-to-manage-single-server-cli.md)

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/mysql-doc-feedback)
