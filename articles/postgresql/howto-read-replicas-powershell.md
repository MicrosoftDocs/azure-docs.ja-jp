---
title: 読み取りレプリカの管理 - Azure PowerShell - Azure Database for PostgreSQL
description: PowerShell を使用して Azure Database for PostgreSQL の読み取りレプリカを設定し、管理する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674533"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>PowerShell を使用して Azure Database for PostgreSQL の読み取りレプリカを作成し、管理する方法

この記事では、PowerShell を使用して Azure Database for PostgreSQL サービスの読み取りレプリカを作成および管理する方法を学習します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell を使用して、読み取りレプリカを作成して管理できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for PostgreSQL サーバー](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.PostgreSql PowerShell モジュールがプレビュー段階にある間は、コマンド `Install-Module -Name Az.PostgreSql -AllowPrerelease` を使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります。
> Az.PostgreSql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 読み取りレプリカ機能は、General Purpose または Memory Optimized のいずれかの価格レベルの Azure Database for PostgreSQL サーバーにのみ使用可能です。 プライマリ サーバーがこれらの価格レベルのいずれかであることを確認します。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzPostgreSqlServerReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| 名前 | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |

リージョンをまたがる読み取りレプリカを作成するには、**Location** パラメーターを使用します。 次の例では、**米国西部** リージョンにレプリカを作成します。

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。

既定では、**Sku** パラメーターが指定されていない限り、読み取りレプリカはプライマリと同じサーバー構成で作成されます。

> [!NOTE]
> レプリカが確実にマスターに追随できるように、レプリカ サーバーの構成をプライマリと同じかそれ以上の値にしておくことをお勧めします。

### <a name="list-replicas-for-a-primary-server"></a>プライマリ サーバーのレプリカを一覧表示する

特定のプライマリ サーバーのレプリカをすべて表示するには、次のコマンドを実行します。

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzPostgreSQLReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| ServerName | mydemoserver | プライマリ サーバーの名前または ID。 |

### <a name="stop-a-replica-server"></a>レプリカ サーバーを停止する

読み取りレプリカ サーバーを停止すると、読み取りレプリカは独立したサーバーに昇格します。 これを行うには、`Update-AzPostgreSqlServer` コマンドレットを実行し、ReplicationRole 値を `None` に設定します。

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを削除するには、`Remove-AzPostgreSqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>プライマリ サーバーを削除する

> [!IMPORTANT]
> プライマリ サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションが停止し、プライマリ サーバー自体が削除されます。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

プライマリ サーバーを削除するには、`Remove-AzPostgreSqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for PostgreSQL サーバーを再起動する](howto-restart-server-powershell.md)