---
title: 読み取りレプリカの管理 - Azure PowerShell - Azure Database for MySQL
description: PowerShell を使用して Azure Database for MySQL の読み取りレプリカを設定し、管理する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c85af0f4078010fa5b6a1d116b3bfda942c0490c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816934"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>PowerShell を使用して Azure Database for MySQL の読み取りレプリカを作成し、管理する方法

この記事では、PowerShell を使用して Azure Database for MySQL サービスの読み取りレプリカを作成および管理する方法を学習します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell を使用して、読み取りレプリカを作成して管理できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MySQL サーバー](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MySql -AllowPrerelease`。
> Az.MySql PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用とメモリ最適化のどちらかの価格レベルにおける Azure Database for MySQL サーバーにのみ使用可能です。 マスター サーバーがこれらの価格レベルのいずれかを確認します。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
> 既存のレプリカがないマスターのレプリカを作成すると、マスターは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| 名前 | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |

リージョンをまたがる読み取りレプリカを作成するには、**Location** パラメーターを使用します。 次の例では、**米国西部**リージョンにレプリカを作成します。

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。

既定では、**Sku** パラメーターが指定されていない限り、読み取りレプリカはマスターと同じサーバー構成で作成されます。

> [!NOTE]
> レプリカをマスターと維持できるようにするために、レプリカ サーバーの構成をマスターと同じかそれ以上の値にしておくようお勧めします。

### <a name="list-replicas-for-a-master-server"></a>マスター サーバーのレプリカを一覧表示する

特定のマスター サーバーのすべてのレプリカを表示するには、次のコマンドを実行します。

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| ServerName | mydemoserver | マスター サーバーの名前または ID。 |

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを削除するには、`Remove-AzMySqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>マスター サーバーを削除します

> [!IMPORTANT]
> マスター サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、マスター サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

マスター サーバーを削除するには、`Remove-AzMySqlServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MySQL サーバーを再起動する](howto-restart-server-powershell.md)
