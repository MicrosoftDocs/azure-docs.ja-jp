---
title: サーバー パラメーターの構成 - Azure PowerShell - Azure Database for MySQL
description: この記事では、PowerShell を使って Azure Database for MySQL のサービス パラメーターを構成する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 973025dfd8c0141ed0884539fe5207cc64ec822c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541863"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-powershell"></a>PowerShell を使用して Azure Database for MySQL のサーバー パラメーターを構成する

PowerShell を使用して、Azure Database for MySQL サーバーの構成パラメーターを一覧表示、表示、更新できます。 エンジン構成のサブセットは、サーバー レベルで公開され、変更が可能です。

>[!Note]
> サーバー パラメーターは、[Azure CLI](./howto-configure-server-parameters-using-cli.md)、[PowerShell](./howto-configure-server-parameters-using-powershell.md)、または [Azure portal](./howto-server-parameters.md) を使用して、サーバー レベルでグローバルに更新できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーのサーバー構成パラメーターを一覧表示する

サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、`Get-AzMySqlConfiguration` コマンドレットを実行します。

次の例では、リソース グループ **myresourcegroup** に含まれているサーバー **mydemoserver** のサーバー構成パラメーターが一覧表示されます。

```azurepowershell-interactive
Get-AzMySqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

一覧表示されている各パラメーターの定義については、「MySQL reference」(MySQL リファレンス) の「[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)」(サーバー システム変数) セクションをご覧ください。

## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する

サーバーの特定の構成パラメーターに関する詳細を表示するには、`Get-AzMySqlConfiguration` コマンドレットを実行して **Name** パラメーターを指定します。

この例では、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターの詳細が表示されます。

```azurepowershell-interactive
Get-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する

特定のサーバー構成パラメーターの値を変更することもでき、MySQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、`Update-AzMySqlConfiguration` コマンドレットを使用します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターを更新する場合。

```azurepowershell-interactive
Update-AzMySqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用した Azure Database for MySQL サーバーのストレージの自動拡張](howto-auto-grow-storage-powershell.md)。
