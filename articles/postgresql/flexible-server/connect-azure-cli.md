---
title: 'クイックスタート: Azure CLI を使用して接続する - Azure Database for PostgreSQL - フレキシブル サーバー'
description: このクイックスタートでは、Azure CLI を使用して、Azure Database for PostgreSQL - フレキシブル サーバーに接続するいくつかの方法を紹介します。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f4eec89aadee1966271286b9280916af973e4b1c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614345"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>クイックスタート: Azure CLI から Azure Database for PostgreSQL - フレキシブル サーバーに接続してクエリを実行する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーは現在パブリック プレビュー段階です。

このクイックスタートでは、Azure CLI と ```az postgres flexible-server connect``` コマンドを使用して Azure Database for PostgreSQL フレキシブル サーバーに接続する方法を紹介します。 このコマンドを使用すると、データベース サーバーとの接続をテストしたり、クエリを実行したりすることができます。 対話モードを使用して、複数のクエリを実行することもできます。 

## <a name="prerequisites"></a>前提条件
- Azure アカウント。 所有していない場合は、[無料試用版を入手](https://azure.microsoft.com/free/)してください。
- [Azure CLI](/cli/azure/install-azure-cli) の最新バージョン (2.20.0 以降) をインストールする
- Azure CLI から ```az login``` コマンドを使用してログインする 
- ```az config param-persist on``` を使用してパラメーターの永続化を有効にする。 パラメーターの永続化により、リソース グループや場所など、多数の引数を繰り返さなくてもローカル コンテキストを使用できるようになります。

## <a name="create-an-postgresql-flexible-server"></a>PostgreSQL フレキシブル サーバーを作成する

最初に作成するのは、マネージド PostgreSQL サーバーです。 [Azure Cloud Shell](https://shell.azure.com/) から次のスクリプトを実行して、このコマンドから返された **サーバー名**、**ユーザー名**、**パスワード** をメモします。

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
このコマンドは、さらに引数を追加することでカスタマイズできます。 すべての引数については、[az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) を参照してください。

## <a name="view-all-the-arguments"></a>すべての引数を確認する
```--help``` 引数を指定すると、このコマンドのすべての引数を確認できます。 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>データベース サーバー接続をテストする
データベースへの接続をテストして検証するには、開発環境から次のコマンドを実行します。

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**例:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
接続に成功した場合、次の出力が表示されます。
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

接続に失敗した場合は、これらの解決策を試してください。
- クライアント マシンでポート 5432 が開放されているかどうかを確認します。
- サーバー管理者のユーザー名とパスワードが正しいかどうかを確認します。
- クライアント マシンにファイアウォール規則が構成されているかどうかを確認します。
- 仮想ネットワークにプライベート アクセスを使用してサーバーを構成した場合、クライアント マシンが同じ仮想ネットワークに存在することを確認します。

## <a name="run-single-query"></a>単一のクエリを実行する
単一のクエリを実行するには、```--querytext``` 引数 (```-q```) を使用してコマンドを実行します。

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**例:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

出力は次のようになります。

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>対話モードを使用して複数のクエリを実行する
対話 (**interactive**) モードを使用して、複数のクエリを実行することができます。 対話モードを有効にするには、次のコマンドを実行します。

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**例:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

次に示すように、**psql** シェルのエクスペリエンスが表示されます。

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サーバーを管理する](./how-to-manage-server-cli.md)
