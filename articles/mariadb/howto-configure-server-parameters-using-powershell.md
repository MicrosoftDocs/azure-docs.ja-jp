---
title: サーバー パラメーターの構成 - Azure PowerShell - Azure Database for MariaDB
description: この記事では、PowerShell を使って Azure Database for MariaDB のサービス パラメーターを構成する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be9e61363beeb2f040aba44e67076c3d66997eee
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87490117"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>PowerShell を使用して Azure Database for MariaDB のサーバー パラメーターを構成する

PowerShell を使用して、Azure Database for MariaDB サーバーの構成パラメーターを一覧表示、表示、更新できます。 エンジン構成のサブセットは、サーバー レベルで公開され、変更が可能です。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](https://docs.microsoft.com/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にインストールする必要があります: `Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell モジュールは一般提供されると、将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのサーバー構成パラメーターを一覧表示する

サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、`Get-AzMariaDbConfiguration` コマンドレットを実行します。

次の例では、リソース グループ **myresourcegroup** に含まれているサーバー **mydemoserver** のサーバー構成パラメーターが一覧表示されます。

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

一覧表示されている各パラメーターの定義については、「MariaDB reference」(MariaDB リファレンス) の「[Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)」(サーバー システム変数) セクションをご覧ください。

## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する

サーバーの特定の構成パラメーターに関する詳細を表示するには、`Get-AzMariaDbConfiguration` コマンドレットを実行して **Name** パラメーターを指定します。

この例では、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターの詳細が表示されます。

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する

特定のサーバー構成パラメーターの値を変更することもでき、MariaDB サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、`Update-AzMariaDbConfiguration` コマンドレットを使用します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターを更新する場合。

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用した Azure Database for MariaDB サーバーのストレージの自動拡張](howto-auto-grow-storage-powershell.md)。
