---
title: サーバー パラメーターの構成 - Azure PowerShell - Azure Database for PostgreSQL
description: この記事では、PowerShell を使って Azure Database for PostgreSQL のサービス パラメーターを構成する方法について説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24106e40a78d4b5607c352ffd49a310533760a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489950"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>PowerShell を使用して Azure Database for PostgreSQL サーバーのパラメーターをカスタマイズする

PowerShell を使用して、Azure Database for PostgreSQL サーバーの構成パラメーターを一覧表示、表示、更新できます。 エンジン構成のサブセットは、サーバー レベルで公開され、変更が可能です。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for PostgreSQL サーバー](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell モジュールがプレビュー段階にある間は、コマンド `Install-Module -Name Az.PostgreSql -AllowPrerelease` を使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります。
> Az.PostgreSql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーのサーバー構成パラメーターを一覧表示する

サーバー内の変更可能なすべてのパラメーターとその値を一覧表示するには、`Get-AzPostgreSqlConfiguration` コマンドレットを実行します。

次の例では、リソース グループ **myresourcegroup** に含まれているサーバー **mydemoserver** のサーバー構成パラメーターが一覧表示されます。

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

一覧表示されている各パラメーターの定義については、PostgreSQL リファレンスの「[環境変数](https://www.postgresql.org/docs/12/libpq-envars.html)」セクションを参照してください。

## <a name="show-server-configuration-parameter-details"></a>サーバー構成パラメーター詳細を表示する

サーバーの特定の構成パラメーターに関する詳細を表示するには、`Get-AzPostgreSqlConfiguration` コマンドレットを実行して **Name** パラメーターを指定します。

この例では、リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターの詳細が表示されます。

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>サーバー構成パラメーターの値を変更する

特定のサーバー構成パラメーターの値を変更することもできます。これによって PostgreSQL サーバー エンジンの基盤となる構成値が更新されます。 構成を更新するには、`Update-AzPostgreSqlConfiguration` コマンドレットを使用します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver** の **slow\_query\_log** サーバー構成パラメーターを更新する場合。

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL サーバーのストレージを自動拡張する](howto-auto-grow-storage-powershell.md)。