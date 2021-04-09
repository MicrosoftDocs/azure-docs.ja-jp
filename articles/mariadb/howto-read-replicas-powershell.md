---
title: 読み取りレプリカの管理 - Azure PowerShell - Azure Database for MariaDB
description: PowerShell を使用して Azure Database for MariaDB の読み取りレプリカを設定し、管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 713f99f67706caded0694d88ece220373920e555
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664989"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-powershell"></a>PowerShell を使用して Azure Database for MariaDB の読み取りレプリカを作成し、管理する方法

この記事では、PowerShell を使用して Azure Database for MariaDB サービスの読み取りレプリカを作成および管理する方法を学習します。 読み取りレプリカの詳細については、[概要](concepts-read-replicas.md)を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell を使用して、読み取りレプリカを作成して管理できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にこれをインストールする必要があります: `Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell モジュールは、一般提供された段階で将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 読み取りレプリカ機能は、汎用またはメモリ最適化の価格レベルの Azure Database for MariaDB サーバーにのみ使用可能です。 ソース サーバーがこれらの価格レベルのいずれであるかを確認します。

### <a name="create-a-read-replica"></a>読み取りレプリカを作成します

> [!IMPORTANT]
> 既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

読み取りレプリカ サーバーは、次のコマンドを使用して作成できます。

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDbServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMariaDbServerReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーが作成されるリソース グループ。  |
| 名前 | mydemoreplicaserver | 作成する新しいレプリカ サーバーの名前。 |

リージョンをまたがる読み取りレプリカを作成するには、**Location** パラメーターを使用します。 次の例では、**米国西部** リージョンにレプリカを作成します。

```azurepowershell-interactive
Get-AzMariaDbServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMariaDServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

レプリカを作成できるリージョンの詳細については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。

既定では、**Sku** パラメーターが指定されていない限り、読み取りレプリカはソースと同じサーバー構成で作成されます。

> [!NOTE]
> レプリカをマスターと維持できるようにするために、レプリカ サーバーの構成をソースと同じかそれ以上の値にしておくことをお勧めします。

### <a name="list-replicas-for-a-source-server"></a>ソース サーバーのレプリカを一覧表示する

特定のソース サーバーのレプリカをすべて表示するには、次のコマンドを実行します。

```azurepowershell-interactive
Get-AzMariaDReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMariaDReplica` コマンドには、次のパラメーターが必要です。

| 設定 | 値の例 | 説明  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  レプリカ サーバーを作成するリソース グループ。  |
| ServerName | mydemoserver | ソース サーバーの名前または ID。 |

### <a name="delete-a-replica-server"></a>レプリカ サーバーを削除します

読み取りレプリカ サーバーを削除するには、`Remove-AzMariaDbServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>ソース サーバーを削除する

> [!IMPORTANT]
> ソース サーバーを削除すると、すべてのレプリカ サーバーへのレプリケーションを停止し、ソース サーバー自体を削除します。 これでレプリカ サーバーは、読み取りと書き込みの両方をサポートするスタンドアロン サーバーになります。

ソース サーバーを削除するには、`Remove-AzMariaDbServer` コマンドレットを実行します。

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MariaDB サーバーを再起動する](howto-restart-server-powershell.md)