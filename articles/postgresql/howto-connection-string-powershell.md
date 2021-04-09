---
title: PowerShell を使用して接続文字列を生成する - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL に接続するための接続文字列を生成する Azure PowerShell の例を提供します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708171"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>PowerShell を使用して Azure Database for PostgreSQL 接続文字列を生成する方法

この記事では、Azure Database for PostgreSQL サーバー用の接続文字列を生成する方法について説明します。 接続文字列を使用すると、さまざまなアプリケーションから Azure Database for PostgreSQL に接続できます。

## <a name="requirements"></a>必要条件

この記事では、次のガイドで作成されたリソースを出発点として使用します。

* [クイック スタート: PowerShell を使用して Azure Database for PostgreSQL サーバーを作成する](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>接続文字列を取得する

アプリケーションを Azure Database for PostgreSQL に接続するための接続文字列を生成するには、`Get-AzPostgreSqlConnectionString` コマンドレットを使用します。 次の例では、**mydemoserver** から PHP クライアントの接続文字列が返されます。

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

`Client` パラメーターの有効な値は次のとおりです。

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL サーバー パラメーターをカスタマイズする](howto-configure-server-parameters-using-powershell.md)
