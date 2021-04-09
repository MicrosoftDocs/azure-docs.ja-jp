---
title: PowerShell:マネージド インスタンスを作成する
titleSuffix: Azure SQL Managed Instance
description: この記事では、マネージド インスタンスを作成するための Azure PowerShell サンプル スクリプトを紹介しています。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: fd091cedb67c07b7de2c7e8540e99c3e8daf7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323795"
---
# <a name="use-powershell-to-create-a-managed-instance"></a>PowerShell を使用してマネージド インスタンスを作成する

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

この PowerShell サンプル スクリプトは、新しい仮想ネットワーク内の専用サブネットにマネージド インスタンスを作成します。 また、仮想ネットワークのルート テーブルとネットワーク セキュリティ グループも構成します。 スクリプトが正常に実行されると、マネージド インスタンスに仮想ネットワーク内またはオンプレミス環境からアクセスできます。 [Azure SQL Database Managed Instance に接続するように Azure VM を構成する](../connect-vm-instance-configure.md)方法に関するページと「[オンプレミスから Azure SQL Managed Instance へのポイント対サイト接続を構成する](../point-to-site-p2s-configure.md)」を参照してください。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](../resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](../resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/managed-instance/create-and-configure-managed-instance.ps1 "Create managed instance")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドのいくつかを使用します。 下の表の使用されるコマンドおよびその他のコマンドの詳細については、コマンド固有のドキュメントへのリンクをクリックしてください。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-AzVirtualNetworkSubnetConfig) | 仮想ネットワークにサブネット構成を追加します。 |
| [Get-AzVirtualNetwork](/powershell/module/az.network/Get-AzVirtualNetwork) | リソース グループ内の仮想ネットワークを取得します。 |
| [Set-AzVirtualNetwork](/powershell/module/az.network/Set-AzVirtualNetwork) | 仮想ネットワークの目標状態を設定します。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Get-AzVirtualNetworkSubnetConfig) | 仮想ネットワーク内のサブネットを取得します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Set-AzVirtualNetworkSubnetConfig) | 仮想ネットワークのサブネット構成の目標状態を構成します。 |
| [New-AzRouteTable](/powershell/module/az.network/New-AzRouteTable) | ルート テーブルを作成します。 |
| [Get-AzRouteTable](/powershell/module/az.network/Get-AzRouteTable) | ルート テーブルを取得します。 |
| [Set-AzRouteTable](/powershell/module/az.network/Set-AzRouteTable) | ルート テーブルの目標状態を設定します。 |
| [New-AzSqlInstance](/powershell/module/az.sql/New-AzSqlInstance) | マネージド インスタンスを作成します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

Azure SQL Managed Instance 用の PowerShell サンプル スクリプトは、他にも [Azure SQL Managed Instance 用の PowerShell スクリプト](../../database/powershell-script-content-guide.md)のページでご覧いただけます。
