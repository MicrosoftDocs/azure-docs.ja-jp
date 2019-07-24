---
title: Azure Database for MySQL でのサービスのパラメーターの構成
description: この記事では、Azure CLI コマンド ライン ユーティリティを使って Azure Database for MySQL のサービス パラメーターを構成する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: b0d7bbdc3e1dcad6f6cecb57b15e2e5df6b3fd28
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53534895"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Azure CLI を使用したサーバー構成パラメーターのカスタマイズ
Azure コマンド ライン ユーティリティ である Azure CLI を使用して、Azure Database for MySQL サーバーの構成パラメーターを一覧表示、表示、および更新できます。 エンジン構成のサブセットは、サーバー レベルで公開され、変更が可能です。 

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) コマンドライン ユーティリティ。または、ブラウザーで Azure Cloud Shell を使用する。

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのサーバー構成パラメーターを一覧表示する
サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、[az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** のサーバー構成パラメーターを一覧表示できます。
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
一覧表示されている各パラメーターの定義については、「MySQL reference」(MySQL リファレンス) の「[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)」(サーバー システム変数) セクションをご覧ください。

## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する
サーバーの特定の構成パラメーターに関する詳細を表示するには、[az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) コマンドを実行します。

この例は、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー構成パラメーターの詳細を示します。
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する
特定のサーバー構成パラメーターの値を変更することもでき、MySQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、[az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) コマンドを使用します。 

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mysql.database.azure.com** の **slow\_query\_log** サーバー構成パラメーターを更新するには、次のコマンドを実行します。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
構成パラメーターの値をリセットする際に、省略可能な `--value` パラメーターを指定しない場合、サービスでは既定値を適用します。 上記の例の場合、以下のようになります。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
このコードは、**slow\_query\_log** 構成を既定値 **OFF** にリセットします。 

## <a name="working-with-the-time-zone-parameter"></a>タイム ゾーン パラメーターを使用する

### <a name="populating-the-time-zone-tables"></a>タイム ゾーン テーブルに入力する

サーバーのタイム ゾーン テーブルには、MySQL コマンド ラインや MySQL Workbench などのツールから `az_load_timezone` ストアド プロシージャを呼び出すことでデータを入力できます。

> [!NOTE]
> MySQL Workbench から `az_load_timezone` コマンドを実行するとき、場合によっては、`SET SQL_SAFE_UPDATES=0;` を利用し、最初にセーフ アップデート モードをオフにする必要があります。

```sql
CALL mysql.az_load_timezone();
```

利用可能なタイム ゾーン値を表示するには、次のコマンドを実行します。

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>グローバル レベルのタイム ゾーンを設定する

グローバル レベルのタイム ゾーンは、[az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) コマンドを利用して設定できます。

次のコマンドでは、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mysql.database.azure.com** のサーバー構成パラメーター **time\_zone** が **US/Pacific** に更新されます。

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>セッション レベルのタイム ゾーンを設定する

セッション レベルのタイム ゾーンは、MySQL コマンド ラインや MySQL Workbench などのツールから `SET time_zone` コマンドを実行することで設定できます。 下の例では、タイム ゾーンが **US/Pacific** タイム ゾーンに設定されます。  

```sql
SET time_zone = 'US/Pacific';
```

[日付と時刻関数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)については MySQL ドキュメントを参照してください。


## <a name="next-steps"></a>次の手順

- [Azure ポータルでサーバー パラメーター](howto-server-parameters.md)を構成する方法