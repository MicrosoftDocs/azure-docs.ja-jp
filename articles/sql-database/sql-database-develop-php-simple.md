---
title: "Windows 上で PHP を使用して SQL Database に接続する | Microsoft Docs"
description: "Windows クライアントから、Azure SQL Database に接続して、クライアントが必要とするソフトウェア コンポーネントへのリンクを提供するサンプル PHP プログラムを示します。"
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>PHP を使用して SQL Database に接続する
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

このトピックでは、PHP を使って Azure SQL Database に接続し、クエリを実行する方法について説明します。 このサンプルは、Windows または Linux から実行できます。 


## <a name="step-1-create-a-sql-database"></a>手順 1: SQL Database を作成する
「 [作業の開始](sql-database-get-started.md) 」ページで、サンプル データベースを作成する方法についてご確認ください。  ガイドに従って、 **AdventureWorks データベースのテンプレート**を作成することが重要です。 以下に示す例は、 **AdventureWorks スキーマ**とのみ動作します。 データベースを作成したら、自分の IP アドレスへのアクセスを有効にします。[使用の開始ページ](sql-database-get-started.md)の説明にあるように、ファイアウォール ルールを有効にします。

## <a name="step-2-configure-development-environment"></a>手順 2: 開発環境を設定する

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
ターミナルを開き、python スクリプトの作成先となるディレクトリに移動します。 次のコマンドを入力して、**Microsoft ODBC Driver for Linux**、**pdo_sqlsrv**、および **sqlsrv** をインストールします。 Microsoft PHP Driver for SQL Server は、Linux 上の Microsoft ODBC Driver を使って SQL データベースに接続します。

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- [WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) から PHP 7.1.1 (x64) をインストールします 
- [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) をインストールします。 
- [Microsoft PHP Driver for SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) の非スレッド セーフな dll をダウンロードし、PHP\v7.x\ext フォルダーに置きます。
- 次に、php.ini (C:\Program Files\PHP\v7.1\php.ini) ファイルを編集して、dll への参照を追加します。 For example:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

この時点で、dll は PHP に登録されています。

## <a name="step-3-run-sample-code"></a>手順 3: サンプル コードを実行する
**sql_sample.php** という名前のファイルを作成し、その中に次のコードを貼り付けます。 これは次を使用してコマンド ラインから実行できます。

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>SQL Database に接続する
[sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php) 関数は、SQL Database に接続するために使います。

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>SQL SELECT ステートメントの実行
[sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php) 関数は、SQL Database に対するクエリから結果セットを取得するために使うことができます。 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>行を挿入し、パラメーターを渡し、生成されたプライマリ キーを取得する
SQL Database では、[IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) プロパティと [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) オブジェクトを使用して、[プライマリ キーの値](https://msdn.microsoft.com/library/ms179610.aspx)を自動生成できます。 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>次のステップ
* 「 [SQL Database の開発: 概要](sql-database-develop-overview.md)
* [Microsoft PHP Driver for SQL Server](https://github.com/Microsoft/msphpsql/)
* [問題/質問を登録します](https://github.com/Microsoft/msphpsql/issues)。

## <a name="additional-resources"></a>その他のリソース
* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [SQL Database の機能](https://azure.microsoft.com/services/sql-database/)

