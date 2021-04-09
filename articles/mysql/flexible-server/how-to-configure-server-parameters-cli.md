---
title: サーバー パラメーターを構成する - Azure CLI - Azure Database for MySQL Flexible Server
description: この記事では、Azure CLI コマンド ライン ユーティリティを使って Azure Database for MySQL Flexible Server のサービス パラメーターを構成する方法について説明します。
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489541"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL Flexible Server のサーバー パラメータを構成する

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

Azure コマンド ライン ユーティリティ である Azure CLI を使用して、Azure Database for MySQL フレキシブル サーバーのパラメーターを一覧表示、表示、および更新できます。 このサーバー パラメーターは、サーバーの作成時に既定値と推奨値を使用して構成されます。  

この記事では、Azure CLI を使用して、サーバー パラメーターの一覧表示、表示、および更新を行う方法について説明します。

>[!Note]
> サーバー パラメーターは、[Azure CLI](./how-to-configure-server-parameters-cli.md) または [Azure portal](./how-to-configure-server-parameters-portal.md) を使用して、サーバー レベルでグローバルに更新できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL フレキシブル サーバー](quickstart-create-server-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) コマンドライン ユーティリティ。または、ブラウザーで Azure Cloud Shell を使用する。

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーのサーバー パラメーターを一覧表示する
サーバー内のすべてのパラメーターとその値を一覧表示するには、[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のサーバー パラメーターを一覧表示できます。
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
一覧表示されている各パラメーターの定義については、「MySQL reference」(MySQL リファレンス) の「[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)」(サーバー システム変数) セクションをご覧ください。

## <a name="show-server-parameter-details"></a>サーバー パラメーターの詳細を表示する
サーバーの特定のパラメーターに関する詳細を表示するには、[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter) コマンドを実行します。

この例は、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー パラメーターの詳細を示します。
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>サーバー パラメーター値を変更する
特定のサーバー パラメーターの値を変更することもできます。これによって MySQL サーバー エンジンの基盤となる構成値が更新されます。 サーバー パラメーターを更新するには、[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter) コマンドを使用します。 

リソース グループ **myresourcegroup** 以下のサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー パラメーターを更新します。
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
パラメーターの値をリセットする際に、省略可能な `--value` パラメーターを指定しない場合、サービスには既定値が適用されます。 上記の例の場合、以下のようになります。
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
このコードを使用すると、**slow\_query\_log** が既定値の **OFF** にリセットされます。 

## <a name="setting-non-modifiable-server-parameters"></a>変更不可のサーバー パラメーターの設定

更新するサーバー パラメーターが変更不可である場合は、必要に応じて `init_connect` を使用して、接続レベルでパラメーターを設定できます。 これにより、サーバーに接続する各クライアントのサーバー パラメーターが設定されます。 

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **init\_connect** サーバー パラメーターを更新して、文字セットなどの値を設定します。
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` を使用すると、セッション レベルで SUPER 特権を必要としないパラメーターを変更することができます。 `init_connect` を使用してパラメーターを設定できるかどうかを確認するには、`set session parameter_name=YOUR_DESIRED_VALUE;` コマンドを実行し、"**Access denied; you need SUPER privilege(s)** "(アクセスが拒否されました。SUPER 特権が必要です) エラーが発生する場合は、'init_connect' を使用してパラメーターを設定することはできません。

## <a name="working-with-the-time-zone-parameter"></a>タイム ゾーン パラメーターを使用する

### <a name="populating-the-time-zone-tables"></a>タイム ゾーン テーブルに入力する

サーバーのタイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `mysql.az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。

> [!NOTE]
> MySQL Workbench から `mysql.az_load_timezone` コマンドを実行するとき、場合によっては、`SET SQL_SAFE_UPDATES=0;` を利用し、最初にセーフ アップデート モードをオフにする必要があります。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> タイム ゾーン テーブルにデータが正しく入力されるようにするには、サーバーを再起動する必要があります。<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

利用可能なタイム ゾーン値を表示するには、次のコマンドを実行します。

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>グローバル レベルのタイム ゾーンを設定する

グローバル レベルのタイム ゾーンは、[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter) コマンドを使用して設定できます。

次のコマンドを使用すると、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mysql.database.azure.com** のサーバー パラメーター **time\_zone** が **US/Pacific** に更新されます。

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。  

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure ポータルでサーバー パラメーター](./how-to-configure-server-parameters-portal.md)を構成する方法
